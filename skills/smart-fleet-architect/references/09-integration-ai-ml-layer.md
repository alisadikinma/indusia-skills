# Pillar 09 — Integration & AI/ML Layer

## External Integration Map

```
┌──────────────────────────────────────────────────────────┐
│              IRN BACKEND (Django)                         │
└─────┬──────────┬──────────┬──────────┬──────────┬───────┘
      │          │          │          │          │
      │ inbound  │ inbound  │ outbound │ in/out   │ in/out
      ▼          ▼          ▼          ▼          ▼
┌─────────┐ ┌────────┐ ┌─────────┐ ┌──────────┐ ┌────────┐
│ Email   │ │WhatsApp│ │ Coretax │ │ NLE      │ │ SITC / │
│ parser  │ │ Biz API│ │ e-Faktur│ │ INSW BC  │ │ Infinity│
│         │ │        │ │         │ │          │ │ portal │
│ IMAP    │ │ webhook│ │ REST API│ │ REST API │ │ scrape │
└─────────┘ └────────┘ └─────────┘ └──────────┘ └────────┘
```

---

## Integration 1 — SITC / Infinity Portal Scraping

### Problem

SITC dan Infinity punya web portal sendiri. Mereka **tidak punya public API**. Order bisa lewat:
1. **Email** (paling umum) — admin terima list container
2. **WhatsApp** (informal)
3. **Portal mereka** (login required, navigate manual)

### Strategy: Multi-Source Parser

**A. Email Parser (Recommended primary)**

```python
# Celery task: poll inbox every 5 min
@celery_app.task
def poll_email_inbox():
    imap = imaplib.IMAP4_SSL('imap.gmail.com')
    imap.login(SETTINGS.email, SETTINGS.email_password)
    imap.select('INBOX')
    
    # Filter: from SITC / Infinity addresses
    _, data = imap.search(None, 
        '(UNSEEN FROM "@sitc-batam.com")')
    
    for num in data[0].split():
        _, msg_data = imap.fetch(num, '(RFC822)')
        msg = email.message_from_bytes(msg_data[0][1])
        
        # Parse subject + body
        result = parse_sitc_order_email(msg)
        if result:
            create_draft_deliveries(result)
            mark_email_processed(msg)
    
    imap.close()
    imap.logout()


def parse_sitc_order_email(msg):
    """
    Pattern: SITC emails biasanya ada list container, vessel, ETA
    Use regex + LLM-based extraction kombinasi
    """
    body = get_email_body(msg)
    
    # Try regex first (fast, deterministic)
    container_pattern = r'\b[A-Z]{4}\d{7}\b'
    containers = re.findall(container_pattern, body)
    
    vessel_pattern = r'(?i)(?:vessel|MV|MS)\s+([A-Z][A-Z0-9 ]+)'
    vessel_match = re.search(vessel_pattern, body)
    
    eta_pattern = r'(?i)ETA[:\s]+(\d{1,2}[/-]\d{1,2}[/-]\d{4}\s+\d{1,2}:\d{2})'
    eta_match = re.search(eta_pattern, body)
    
    if not containers:
        # Fallback to LLM extraction (slower but flexible)
        return llm_extract_order(body)
    
    return {
        'vessel': vessel_match.group(1) if vessel_match else None,
        'eta': parse_datetime(eta_match.group(1)) if eta_match else None,
        'containers': containers,
        'source_email_id': msg.get('Message-ID')
    }


def llm_extract_order(text):
    """
    Use Anthropic Claude / OpenAI for free-form extraction
    """
    prompt = f"""Extract from this email:
- Vessel name
- ETA (datetime)
- List of container numbers (format: 4 letters + 7 digits)
- Origin port
- Customer name (consignee)

Email:
{text}

Return as JSON.
"""
    response = anthropic.messages.create(
        model="claude-haiku-4-5",
        max_tokens=1000,
        messages=[{"role": "user", "content": prompt}]
    )
    return json.loads(response.content[0].text)
```

**B. WhatsApp Business API**

WhatsApp Business API (via Meta atau third-party seperti Twilio, Wati, Onesignal) untuk parse incoming messages.

```python
# Webhook handler
@app.post('/webhook/whatsapp')
async def whatsapp_webhook(request):
    data = await request.json()
    message = data['messages'][0]
    
    if message['type'] == 'text':
        body = message['text']['body']
        sender = message['from']
        
        # Try parse as order
        result = parse_whatsapp_order(body)
        if result:
            create_draft_deliveries(result)
            send_confirmation_reply(sender, result)
        else:
            # Save as raw incoming message untuk admin review
            store_incoming_message(message)
```

**Cost WhatsApp Business API:**
- Meta direct: gratis untuk receive, $0.005-0.05/sent message tergantung type
- Twilio: $0.005/conversation + setup
- Wati: $39-99/month (bundled)

**C. Portal Scraping (Last Resort)**

Selenium/Playwright headless browser untuk login + navigate SITC portal. **Fragile** (portal change → break). Pakai hanya kalau email + WA tidak cover semua order.

---

## Integration 2 — NLE INSW (National Logistics Ecosystem)

### What It Provides

NLE INSW (https://nle.insw.go.id/) — government portal untuk integrasi customs + port + freight forwarder data.

**Untuk IRN:**
- Cek status PIB (Pemberitahuan Impor Barang) per consignee
- Cek SP2 issuance status real-time
- Track manifest dokumen

### Integration Approach

NLE menyediakan **API access** untuk registered PPJK (Pengusaha Pengurusan Jasa Kepabeanan). Kalau IRN bukan PPJK:
- Partner dengan PPJK existing (Logisly, dll yang punya in-house PPJK)
- ATAU register sebagai PPJK (effort + cost biaya tahunan + audit)

### MVP Approach

**Skip direct NLE integration di MVP**. Strategi:
- Manual portal check via admin (link saved as bookmark)
- Email parser dari SITC sudah include SP2 status biasanya
- Phase 2: pertimbangkan NLE direct integration

---

## Integration 3 — Coretax / e-Faktur API

### Coretax (Live Production 2025+)

Coretax = sistem terpadu DJP yang menggantikan e-Faktur, e-Bupot, e-SPT.

### API Capabilities

| Function | Endpoint Pattern |
|---|---|
| Generate Faktur Pajak | POST /coretax/v1/faktur |
| Submit Bukti Potong | POST /coretax/v1/bupot |
| Validate NPWP | GET /coretax/v1/npwp/{nomor} |
| Status check | GET /coretax/v1/faktur/{id} |

### Integration Steps

```python
import requests

class CoretaxClient:
    def __init__(self, certificate_path, password, npwp):
        self.session = requests.Session()
        self.session.cert = (certificate_path, password)
        self.npwp = npwp
        self.base_url = 'https://api.coretax.djp.go.id/v1'
    
    def create_faktur(self, faktur_data):
        payload = {
            "npwp_penjual": self.npwp,
            "npwp_pembeli": faktur_data['customer_npwp'],
            "tanggal": faktur_data['date'],
            "items": [
                {
                    "deskripsi": item['description'],
                    "qty": item['qty'],
                    "harga": item['price'],
                    "ppn": item['vat_amount']
                }
                for item in faktur_data['items']
            ]
        }
        response = self.session.post(
            f'{self.base_url}/faktur',
            json=payload
        )
        response.raise_for_status()
        return response.json()
```

**Catatan praktis:**
- Coretax API masih maturing — banyak quirks, downtime
- Wajib digital certificate dari DJP (one-time setup)
- Sandbox environment available untuk testing

### Phase

- MVP: skip Coretax direct integration. Generate invoice via aplikasi web manual entry.
- Phase 1 (bulan 4-6): integrate Coretax API, automate faktur generation.

---

## Integration 4 — OCR (Container Number, Surat Jalan, Bill of Lading)

### Use Cases

1. **Container number recognition** — sopir foto, sistem extract "MRKU4449924"
2. **Surat jalan OCR** — kalau ada paper backlog
3. **Bill of Lading parsing** — kalau IRN scanning customer dokumen

### OCR Engine Comparison

| Engine | Akurasi (latin char) | Akurasi (container) | Cost (per 1k) | Latency |
|---|---|---|---|---|
| **Tesseract (open-source)** | 85-90% | 70-80% (with custom train) | Free | <1 sec |
| **Google Vision API** | 95-98% | 90-95% | $1.50/1k | 0.5-2 sec |
| **Azure Computer Vision** | 95-98% | 90-95% | $1/1k | 0.5-2 sec |
| **AWS Textract** | 95-98% | 88-93% | $1.50/1k | 1-3 sec |
| **Custom YOLO + CRNN** | 92-96% | 95%+ (specialized) | Free (after train) | <0.5 sec |

### Recommendation

```
MVP: Google Vision API for container number
  Cost: ~5k extractions/month × $1.50/1k = ~$7.50/month = Rp 120k/month — affordable

Phase 2: Custom YOLO + CRNN trained on Indonesian truck plate + container
  Reason: better accuracy for specialized format, no per-call cost at scale
```

### Implementation Snippet

```python
from google.cloud import vision

def extract_container_number(image_path):
    client = vision.ImageAnnotatorClient()
    
    with open(image_path, 'rb') as f:
        content = f.read()
    
    image = vision.Image(content=content)
    response = client.text_detection(image=image)
    
    for text in response.text_annotations:
        # Container number pattern: 4 letters + 7 digits
        match = re.search(r'\b([A-Z]{4}\d{7})\b', text.description)
        if match:
            return match.group(1)
    
    return None
```

---

## AI/ML Features

### Feature 1: Auto-Assign Optimization (covered in Pillar 04)

### Feature 2: Demurrage Prediction

```python
# Predict probability container akan demurrage given current state

def predict_demurrage_risk(container):
    features = {
        'days_in_country': container.days_in_country,
        'free_time_remaining': container.free_time_remaining,
        'customer_avg_unload_time': customer.historical_avg_unload_days,
        'customer_dso': customer.dso_avg,
        'season': get_season(),  # peak season higher risk
        'shipping_line_strict': customer.line.strictness_score,
    }
    
    # Phase 1: rule-based
    if container.days_in_country > container.free_time * 0.8:
        return {'risk': 'HIGH', 'estimated_charge': calculate_charge(container)}
    
    # Phase 2: ML model (XGBoost atau simple regression)
    model = load_model('demurrage_predictor_v1')
    risk_score = model.predict_proba([list(features.values())])[0][1]
    return {'risk': classify(risk_score), 'score': risk_score}
```

### Feature 3: Anomaly Detection (Fuel/GPS)

(See `05-anti-fraud-tech-stack.md`)

### Feature 4: Customer Churn Risk

```python
# Identify customers showing signs of leaving

features = {
    'volume_change_30d': customer.volume_pct_change(30),
    'days_since_last_order': customer.days_since_last_order(),
    'complaint_count_90d': customer.complaints_count(90),
    'dso_trend': customer.dso_trend(),
    'price_sensitivity_score': customer.price_sensitivity()
}

# Train pakai historical churned customers
```

### Feature 5: Demand Forecasting

Prediksi volume order per bulan/minggu by customer + by route untuk:
- Kapan rekrut sopir tambahan
- Kapan beli chassis tambahan
- Kapan negosiasi rate dengan customer

---

## ML Implementation Phasing

### Phase 1 (after 6+ months data)

| Feature | Approach |
|---|---|
| Auto-assign | Greedy heuristic (rule-based) |
| Demurrage risk | Rule-based threshold |
| Anti-fraud anomaly | Rule engine |

### Phase 2 (after 12+ months data)

| Feature | Approach |
|---|---|
| Auto-assign | OR-Tools constraint solver |
| Demurrage prediction | XGBoost / LightGBM |
| Customer churn | Logistic regression / RF |
| Demand forecast | Prophet / ARIMA |

### Phase 3 (Year 2+)

| Feature | Approach |
|---|---|
| Auto-assign | RL with simulation |
| Anomaly detection | Autoencoder / Isolation Forest |
| Demand forecast | Neural net (LSTM/Transformer) |
| Container number OCR | Custom YOLO + CRNN |

---

## Anti-Patterns

❌ **AI/ML di MVP** — Anda butuh **6+ bulan data quality** dulu. Rule-based first.

❌ **OCR pakai Tesseract di production tanpa custom training** — accuracy untuk container number ~70-80% = banyak rework.

❌ **Direct portal scraping sebagai PRIMARY integration** — fragile. Fallback only.

❌ **Skip Coretax sampai dipaksa DJP** — adopt early akan less painful saat semua wajib.

❌ **Build ML in-house tanpa data scientist** — pakai cloud ML platform (Google AI Platform, AWS SageMaker) atau hire freelance.

❌ **Real-time ML inference untuk semua decision** — banyak yang batch-OK (daily prediction). Real-time hanya untuk anti-fraud + auto-assign.

---

## Cost Summary

### MVP (Bulan 1-3)

| Service | Monthly OpEx |
|---|---|
| WhatsApp Business API (Meta) | ~Rp 200-500k |
| Google Vision OCR (~5k ops) | ~Rp 120k |
| Email parser (no extra cost, IMAP free) | 0 |
| **Total** | **Rp 320-620k/bulan** |

### Phase 1 (Bulan 4-6)

| Service | Monthly OpEx |
|---|---|
| Add Coretax API | one-time setup ~Rp 5jt + maintenance |
| ML inference (cloud) | Rp 500k-1jt |
| **Total** | **+Rp 1-2jt/bulan** |

---

## Cross-References

- WhatsApp + email integration → triggers driver dispatch in `04-dispatcher-ai-autoassign.md`
- Coretax e-faktur business context → `pakar-logistik-batam/08-tax-finance-invoicing.md`
- OCR untuk container number → `02-data-model-schema.md` (container.container_no field)
- AI/ML strategy → `01-system-architecture-overview.md` phasing
