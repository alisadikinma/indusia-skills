# Pillar 07 — Decision Frameworks: 8 Frameworks Praktis untuk Owner

## Why Frameworks Matter

Owner trucking + crane di Batam buat **30-50 keputusan per minggu**: trip ini accept atau tolak? Sopir ini SP1 atau pecat? Beli chassis baru atau rent? Buka lane Tg Pinang atau tidak?

Tanpa framework, keputusan jadi **gut-feel** — tergantung mood, tergantung berita kemarin, tergantung saudara siapa yang lobi. Akumulasi 100 gut-feel decisions = bisnis hancur diam-diam.

Framework = **algoritma keputusan eksplisit** — input + rule + output. Bisa diaudit, bisa dievaluasi, bisa dipindah-tangan ke admin / partner.

8 frameworks ini sudah dipakai owner trucking sukses di Batam (dan logistics Indonesia luas). Customize sesuai konteks Anda.

---

## Framework 1 — Accept/Reject Trip Decision

### Inputs Needed
- Tarif dari customer
- Estimated BBM cost (route distance × konsumsi rata-rata)
- Tol + cas hidden
- Driver komisi (25% dari net)
- Allocated overhead per trip
- Customer DSO history
- Slot availability sopir + chassis + vehicle

### Decision Tree

```
1. CALCULATE: contribution = tarif - BBM - tol - hidden_cost - komisi
2. IF contribution < Rp 200,000:
     → REJECT (kecuali idle prevention case)
3. IF contribution < Rp 50,000 AND chassis idle > 5 hari:
     → ACCEPT (better small contribution than zero)
4. IF customer DSO > 90 hari:
     → NEGOSIASI: tarif +15% atau tolak
     → kalau tarif tidak naik, REJECT untuk volume baru
5. IF customer baru AND tidak ada credit history:
     → DP 30% atau COD trip pertama
6. IF capacity available AND contribution acceptable:
     → ACCEPT
```

### Edge Cases
- **Strategic customer (long-term volume tinggi)** → bisa terima 1-2 trip loss-leader untuk lock relationship, **tapi cap maksimal 5 trip/bulan**
- **Empty repositioning combo** → kalau bisa kombinasi trip empty + load di hari yang sama, contribution dihitung gabungan
- **Penalty / cancel cost** → kalau IRN reject, customer bisa cari kompetitor → reputasi turun. Reject hanya saat alternatif clearly better

---

## Framework 2 — Hire/Fire Driver Decision

### Inputs Needed
- Performance score (multi-factor)
- Tenure di IRN
- Training investment
- Replacement cost & time
- Pattern of issue (1x vs repeat)

### Decision Matrix

```
PERFORMANCE SCORE (rolling 90 days):
  - On-time delivery rate (%)
  - BBM efficiency (km/L vs target)
  - Customer complaint count
  - Safety score (harsh-driving from telematics)
  - Document completeness rate (foto, signature)
  - Anti-fraud incident count

OVERALL SCORE:
  90-100: Senior promotion track + bonus
  75-89: Healthy performer, retain
  60-74: Improvement plan 60 days, monitoring
  40-59: SP1 + 30-day improvement plan
  <40: Terminate

PROFESSIONAL CONDUCT (binary):
  Confirmed fuel theft (kencing solar) → SP1 first time, terminate second
  Manipulasi waktu / route → SP1
  Sabotage equipment → terminate immediately
  Customer complaint serious (perilaku, sopan) → SP1
  Repeated late >3x/month → improvement plan
```

### Hire Side (Recruitment Filter)

```
MINIMUM HIRE CRITERIA:
- SIM B1/B2 valid sesuai unit
- 2+ tahun pengalaman trucking (preferable Batam)
- Referensi dari 1 senior driver IRN existing (network filter)
- Klinik check (no narkoba, sehat fisik)
- KTP + verify alamat (bukan transient)

PROBATION 60 hari:
- Pasang ekspectasi sanction matrix di kontrak
- Senior driver buddy 30 hari
- Performance score weekly review
```

---

## Framework 3 — Buy vs Rent Chassis Decision

### Inputs Needed
- Monthly utilization (% hari aktif)
- Chassis cost (CapEx)
- Maintenance + depreciation (annual)
- Rent rate per day (kalau rent dari kompetitor / leasing)
- Cost of capital / interest rate
- Expected useful life (10-15 tahun)

### Calculation

```
BUY:
  Annualized ownership cost = (CapEx / useful_life) + maintenance + interest
  Per-day cost = Annualized / 365

RENT:
  Per-day cost = market rent rate

DECISION:
  IF utilization > 70% AND per-day-buy < per-day-rent × 0.85:
    → BUY
  IF utilization < 50%:
    → RENT
  IF utilization 50-70%:
    → MIXED (buy core fleet, rent saat peak)
```

### Concrete Example (Chassis 40ft di Batam, 2026)

| Parameter | Value |
|---|---|
| CapEx new chassis 40ft | Rp 220-280jt |
| Useful life effective | 12 tahun |
| Annual depreciation | Rp 18-23jt |
| Annual maintenance | Rp 8-12jt |
| Cost of capital (10%) | Rp 22-28jt |
| **Total annual cost owned** | **Rp 48-63jt** |
| Per-day cost (365) | Rp 130-170k |
| Per-day cost (active 220 hari) | Rp 220-285k |
| Market rent rate | Rp 250-350k/hari |

**Conclusion:** kalau utilization >60%, buy lebih ekonomis. <60%, rent.

---

## Framework 4 — Open New Lane / Geography

### Inputs Needed
- Volume opportunity (estimated trips/month)
- Margin estimate
- Competitor density
- Customer commitment (kontrak vs spot)
- Operational complexity (driver familiarity, fuel availability, return load)

### Decision Tree

```
1. ESTIMATE: monthly potential revenue
2. IF monthly revenue < Rp 50jt:
     → SKIP unless strategic anchor customer
3. IF 2-way volume (impor + ekspor) < 70% capacity:
     → empty repositioning kill margin → SKIP
4. IF competitor density tinggi AND no differentiator:
     → SKIP (race to bottom)
5. IF customer commit kontrak 6+ bulan AND volume verified:
     → ENTER with 1-2 dedicated unit
6. IF spot only:
     → TEST 30 hari dengan 1 unit, evaluate
```

### Lane Examples Batam

| Lane | Verdict (untuk fleet 30-50 unit) |
|---|---|
| **Batu Ampar → Tanjung Uncang** | Core volume, mandatory |
| **Batu Ampar → Batamindo** | Healthy margin, balanced |
| **Sekupang → Bottom Centre** | Smaller volume, optional |
| **Kabil → EPC O&G site** | High value, project-based |
| **Batam → Tanjung Pinang** (cross-island via ferry) | Complex, low volume — SKIP |
| **Batam → Karimun** | Niche, kompetitor sudah set — SKIP |

---

## Framework 5 — Pricing per Customer Tier

### Tiering Logic

```
TIER A — STRATEGIC PARTNER:
  Criteria: 6+ months kontrak, volume > 50 trip/bulan
  Pricing: 5-10% discount vs standard
  SLA: 95% on-time
  Payment: 60 hari standard

TIER B — STANDARD CUSTOMER:
  Criteria: regular volume, no kontrak
  Pricing: standard
  SLA: 90% on-time
  Payment: 60-75 hari

TIER C — SPOT / IRREGULAR:
  Pricing: standard +5-10%
  SLA: 85%
  Payment: 30 hari max atau COD
  
TIER D — RISK CUSTOMER:
  Criteria: DSO > 120 hari history, atau new without credit
  Pricing: standard +15-20% (compensate working capital)
  Payment: COD or DP 30%
  
TIER E — REJECT:
  History default / dispute / blacklist
  Tidak terima order
```

### Repricing Trigger
- Annual review setiap Q1
- Trigger event: DSO jeblok, complaint major, lost volume kompetitor

---

## Framework 6 — Credit Risk Assessment Customer Baru

### Steps Wajib

```
STEP 1: VERIFICATION
- Cek NPWP + akta perusahaan
- Cek alamat fisik (Google Maps + pernah ke sana?)
- Cek website + LinkedIn → real business or shell?

STEP 2: REFERENCES
- Minta 2 reference dari supplier sebelumnya
- Telepon (jangan email saja) supplier reference

STEP 3: TRADE BUREAU
- Cek di komunitas APTRINDO / forum trucking — tanya ada history?
- Cek di WA group sopir — pernah ada complaint?

STEP 4: TRIAL TERMS
- Trip pertama: COD atau DP 50%
- Setelah 3 bulan smooth → upgrade to standard terms

STEP 5: ONGOING MONITORING
- Monthly DSO check
- Late payment >2x = reset to COD
- DSO > 120 = pause new orders
```

### Red Flags

🚩 Customer terlalu cepat setuju standard terms tanpa nego
🚩 Tidak mau give NPWP / details
🚩 Alamat office tidak verifiable
🚩 Reference yang di-provide tidak respond / generic feedback
🚩 Pattern bayar — sometimes prompt, sometimes 6 bulan delay (potential cherry-picking suppliers)

---

## Framework 7 — Vehicle / Crane Replacement

### Inputs
- Maintenance cost trend (last 6 months)
- Downtime hours per month
- Revenue per active day
- Trade-in / sale value of current unit
- Replacement cost
- Tax impact (depreciation reset)

### Decision Tree

```
IF maintenance_cost > revenue × 20% for 6 consecutive months:
  → REPLACE
IF downtime > 25% of working days:
  → REPLACE
IF maintenance trend escalating + age > 8 years (truck) / 12 years (crane):
  → REPLACE within 12 months
ELSE:
  → KEEP, run tighter maintenance schedule
```

### Replacement Cycle Indonesian Trucking

- **Head truck (Hino, Mitsubishi)**: 8-12 tahun ekonomis
- **Chassis 40ft / 20ft**: 12-15 tahun
- **Mobile crane**: 12-15 tahun (engine major overhaul at year 8-10)
- **Crawler crane**: 20-25 tahun (component replacement)

---

## Framework 8 — Tech / Software Investment

### When to Invest

```
IF (manual_admin_hours_saved × cost_per_hour × 12) > annual_cost_of_tech:
  → INVEST

IF tech reduces fraud risk × probability × impact > cost:
  → INVEST

IF customer demand for digital service (e.g., self-tracking link) is increasing:
  → INVEST for retention

IF competitor adopting tech that gives them edge:
  → MATCH within 6-12 months OR differentiate
```

### Concrete Example — TMS Subscription

```
Manual cost current state:
  - Admin 1 person × Rp 6jt/bulan × 50% time = Rp 3jt/bulan = Rp 36jt/year saved
  - Reduce dispute / lost invoice 5% = Rp 50jt/year recovered
  - Reduce kencing solar 30% via fuel sensor = Rp 30jt/year saved
  
TOTAL value: Rp 116jt/year

TMS subscription (typical Indonesian TMS):
  - Rp 100-300k/vehicle/month for 50 vehicles = Rp 5-15jt/month = Rp 60-180jt/year

NET: Anywhere from neutral to +Rp 56jt/year, plus intangible gains (visibility, customer satisfaction)
DECISION: INVEST (start with smaller pilot 5-10 vehicles to validate)
```

### Anti-Pattern: Don't Invest Just Because

❌ Kompetitor pasang dashcam → must match
❌ "Digital transformation is the future"
❌ Vendor presentation impressive

✅ INVEST when **named pain × measured cost > tech investment + adoption friction**

---

## Customer Archetype yang Pakai Frameworks Ini

**"Pak Indra" — Owner**
- Sebelum framework: gut-feel, sometimes impulsive
- Setelah framework: konsisten, bisa delegate keputusan ke admin senior
- **Quote:** "Sekarang saya gak harus pegang setiap keputusan. Admin tahu rule-nya."

---

## Quotable Hooks untuk Video Script

1. *"Owner trucking pintar gak buat keputusan dengan perasaan. Mereka punya 8 framework — gut-feel cuma untuk hal yang gak masuk framework."*
2. *"Setiap trip Anda accept atau reject = setoran masa depan bisnis Anda. Sistem yang kasih hitungan dalam 5 detik = sistem yang Anda butuhkan."*
3. *"Anda gak akan pernah tahu sopir mana harus dipromosi atau dipecat — kecuali Anda punya score multi-factor yang konsisten 90 hari."*
4. *"Customer baru minta 90 hari payment? Anda kasih. Tapi premium 15%. Itu bukan greedy — itu kompensasi modal kerja Anda."*
5. *"Beli chassis baru karena 'rasanya butuh' = Rp 250jt impulse. Beli chassis karena utilization 75% terbukti = Rp 250jt investasi."*

---

## Feature Implications (MoSCoW)

| Feature | Priority | ROI |
|---|---|---|
| Trip P&L decision helper (auto-flag accept/reject) | **MUST** | Margin discipline |
| Driver performance score multi-factor 90-day | **MUST** | Hire/fire support |
| Customer DSO + tier auto-assignment | **MUST** | Pricing discipline |
| Vehicle / chassis utilization analytics | **SHOULD** | Buy/rent decision |
| Tech ROI calculator | **COULD** | Self-service decision |

---

## Cross-References

- Unit economics calculation → `04-unit-economics.md`
- Driver performance scoring → `05-driver-control-antifraud.md`
- Customer reputation tier → `06-tacit-batam-context.md`
- Decision automation features → sister skill `smart-fleet-architect/04-dispatcher-ai-autoassign.md`
