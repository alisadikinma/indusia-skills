# Pillar 06 — UHF RFID Gate & Asset Tracking

## Spec Recommendation (Berdasarkan Hardware User)

User sudah pilih: **UHF RFID 800-928 MHz, EPC Gen 2 / ISO 18000-6C, Integrated Reader + Antenna IP65/IP67**.

Spec konfirmasi yang harus di-verify ke vendor:

| Parameter | Required Spec | Catatan |
|---|---|---|
| **Frequency** | **920–925 MHz (Indonesia FCC band)** | Postel/Kominfo regulation. JANGAN pakai EU band 865-868 MHz — tidak legal di Indonesia. |
| **Protocol** | EPC Class 1 Gen 2 v2 / ISO 18000-6C | ✅ user confirmed |
| **Antenna gain** | 8-9 dBi circular polarization | Untuk gate truck, 8 dBi cukup |
| **Tx power** | 0-30 dBm adjustable | Start 26 dBm, tune to 28 if missed reads |
| **Read range** | 5-12m (depends on tag) | Target 5-7m utk gate depo |
| **Anti-collision** | Q algorithm dynamic | Default Q=4, tune to 6-7 untuk multi-tag truck |
| **Interface** | Wiegand 26/34 + RS232/RS485 + **TCP/IP** | TCP/IP wajib untuk MQTT push |
| **IP rating** | IP65/IP67 | ✅ outdoor wajib |
| **Power** | 9-12V DC | Wajib pakai surge protector (Batam petir) |

---

## Use Case di IRN — Multi-Asset Gate Logging

```
GATE DEPO IRN — 1 scan event = multi-asset captured

Truck lewat gate
       ↓
   Reader UHF detect (5-7m range, 1-2 detik dwell time)
       ↓
   Multi-tag detected:
   ┌─────────────────────────────────────────┐
   │ • EPC-DRIVER-001    (lanyard sopir)     │
   │ • EPC-VEHICLE-PM22  (sticker di kabin)  │
   │ • EPC-CHASSIS-IRN40-027 (on-metal tag)  │
   │                                         │
   │ Optional:                               │
   │ • EPC-CONTAINER-MRKU4449924 (sticker on container) │
   └─────────────────────────────────────────┘
       ↓
   Backend log gate_event:
   {
     timestamp: 2026-05-10 14:22:15,
     gate_id: "DEPO_IRN_GATE_1",
     direction: "OUT" (deteksi via 2 reader pair atau 1 reader + tilt),
     driver_id: "001-PARDAMEAN",
     vehicle_id: "PM22-B9281SEHH",
     chassis_id: "IRN40-027",
     container_no: "MRKU4449924" (kalau ada),
     auto_paired: true,
     anomaly: null
   }
```

---

## Tag Selection per Asset Type

| Asset | Tag Type | Mounting | Cost (Rp/pcs) | Lifespan |
|---|---|---|---|---|
| **Driver** | Standard inlay (kartu/lanyard) | Lanyard / wallet | 8-15k | 2-3 thn |
| **Vehicle (kabin)** | Rugged ABS housing | Sticker windshield atau dashboard | 80-150k | 5-7 thn |
| **Chassis (metal)** | **On-metal tag** (foam-backed atau ceramic) | Welded bracket atau strong adhesive | 30-60k | 5-10 thn |
| **Container (metal)** | On-metal tag | Stick or pop-rivet | 30-60k | 3-5 thn |

**WHY on-metal beda?** UHF RF terdistorsi di permukaan logam. Tag biasa (paper/plastic inlay) tidak bisa baca di metal — perlu antenna spacer. On-metal tag punya built-in spacer dan tuning.

### Total Tag BoM

| Item | Qty IRN | Unit (Rp) | Total (Rp) |
|---|---|---|---|
| Driver tag (lanyard) | 50 | 12,000 | 600,000 |
| Vehicle tag (kabin sticker) | 50 (truck) + 12 (crane) | 100,000 | 6,200,000 |
| Chassis tag (on-metal) | 95 | 45,000 | 4,275,000 |
| Container tag (kalau IRN ada container sendiri / coordination dengan shipping line) | optional | 45,000 | optional |
| **Total Tag CapEx** | | | **~Rp 11jt** |

---

## Gate Hardware BoM (1 Gate Setup Lengkap)

| Item | Qty | Unit (Rp) | Total (Rp) | Catatan |
|---|---|---|---|---|
| **Reader UHF integrated** (yang user beli) | 1 | 3,000,000 | 3,000,000 | Generic Daily/Yanzeo class |
| **Mounting bracket galvanized** | 1 | 500,000 | 500,000 | Tinggi 3m, kokoh |
| **Cable (Cat6 outdoor + power)** | 30m | 15,000/m | 450,000 | Sesuai jarak ke ruang server |
| **Cable conduit + accessories** | - | 350,000 | 350,000 | |
| **Surge protector** (line power + data) | 2 | 400,000 | 800,000 | Petir Batam wajib! |
| **UPS 600VA** | 1 | 1,200,000 | 1,200,000 | 30-60 menit backup PLN mati |
| **Gateway (RPi 4 + 4G modem fallback)** | 1 | 1,500,000 | 1,500,000 | Optional kalau ruang server ada wired |
| **Lampu LED indicator** (hijau OK / merah block) | 2 | 250,000 | 500,000 | Visual feedback ke sopir |
| **Speaker buzzer** | 1 | 200,000 | 200,000 | Beep saat scan berhasil |
| **Tag-tag (driver + vehicle + chassis)** | - | - | 11,000,000 | Lihat tabel atas |
| **Instalasi + tuning teknisi (1-2 hari)** | 1 | 2,500,000 | 2,500,000 | |
| **TOTAL 1 Gate Setup** | | | **~Rp 22jt** | |

**Multi-gate (gate masuk + keluar pisah):** ~Rp 32-40jt total.

---

## Integration Architecture

### Reader → Backend Flow

```
[Reader UHF di gate]
    │
    │ (1) TCP/IP push raw EPC reads
    │
    ▼
[Local Gateway (RPi atau directly cloud)]
    │
    │ (2) Process: dedup, filter noise, format
    │
    ▼
[MQTT broker (cloud)]
    │ Topic: irn/gate/depo1/events
    │
    ▼
[Backend listener service]
    │
    │ (3) Match EPC → entity (driver / vehicle / chassis)
    │ (4) Triangulation — driver+vehicle+chassis match?
    │ (5) Detect direction (IN/OUT) via 2-reader pair atau RSSI gradient
    │ (6) Persist gate_event to Postgres
    │ (7) Trigger downstream:
    │     - Auto attendance (clock-in/out)
    │     - Update chassis location to "DEPO" or "OUT"
    │     - Anomaly check (chassis OUT tanpa driver = alert)
```

### MQTT Message Format

```json
{
  "topic": "irn/gate/depo1/events",
  "timestamp": "2026-05-10T14:22:15Z",
  "gate_id": "DEPO_IRN_GATE_1",
  "reads": [
    {
      "epc": "E2806894000050000123456A",
      "rssi": -42,
      "antenna": 1,
      "first_seen": "2026-05-10T14:22:14.300Z",
      "last_seen": "2026-05-10T14:22:15.800Z",
      "read_count": 47
    },
    {
      "epc": "E2806894000050000123456B",
      "rssi": -38,
      "antenna": 1,
      "first_seen": "2026-05-10T14:22:14.500Z",
      "last_seen": "2026-05-10T14:22:15.700Z",
      "read_count": 52
    }
  ]
}
```

### Direction Detection (IN vs OUT)

**Option A — 2 reader pair (recommended):**
```
[Reader_A] (luar gate) ←─── Truck moving ───→ [Reader_B] (dalam gate)

If A reads first then B → direction = IN
If B reads first then A → direction = OUT
```

**Option B — 1 reader + RSSI gradient:**
- Track RSSI value over time
- RSSI naik = approaching
- RSSI turun = leaving
- Combine with last-known position state machine

**Option C — 1 reader + tilt sensor / loop sensor (vehicle detected):**
- Vehicle loop detector (induction) signals presence
- 1 reader scans
- Combine with last gate_event direction state

**Recommendation IRN:** Option A (2 reader pair) untuk reliability, ~+Rp 3jt/gate.

---

## Anti-Collision Tuning (Q Algorithm)

UHF Gen 2 protocol pakai "Q" parameter untuk handle multiple tags simultan.

```
Q = log2(N) where N = max simultaneous tags expected

Q=2 → up to 4 tags        (default, single asset gate)
Q=3 → up to 8 tags        (small fleet asset)
Q=4 → up to 16 tags       (default factory)
Q=5 → up to 32 tags       (warehouse)
Q=6 → up to 64 tags       (✅ truck dengan 3-5 tag bareng)
Q=7 → up to 128 tags      (yard scanning)
Q=8+ → high-density      (large yard pass-through)
```

**For IRN gate (5-10 tag simultan):** start with Q=5, tune to Q=6 if missed reads.

---

## Anomaly Detection Examples

```python
def check_gate_anomaly(gate_event):
    anomalies = []
    
    # 1. Chassis OUT but no driver+vehicle
    if gate_event.chassis_id and not (gate_event.driver_id and gate_event.vehicle_id):
        anomalies.append({
            'type': 'CHASSIS_OUT_UNATTACHED',
            'severity': 'CRITICAL',
            'message': 'Chassis terdeteksi keluar gate tanpa driver+vehicle pair'
        })
    
    # 2. Driver IN but no vehicle (pulang tanpa truk?)
    if gate_event.driver_id and not gate_event.vehicle_id and gate_event.direction == 'IN':
        # OK kalau driver memang lapor pakai motor / jalan kaki
        anomalies.append({
            'type': 'DRIVER_IN_NO_VEHICLE',
            'severity': 'INFO',
            'message': 'Driver masuk tanpa kendaraan'
        })
    
    # 3. Driver tag detected tapi tidak match dengan vehicle's expected driver
    if gate_event.driver_id and gate_event.vehicle_id:
        expected_driver = get_assigned_driver(gate_event.vehicle_id)
        if expected_driver and expected_driver != gate_event.driver_id:
            anomalies.append({
                'type': 'DRIVER_VEHICLE_MISMATCH',
                'severity': 'HIGH',
                'message': f'Vehicle {gate_event.vehicle_id} ditugaskan ke {expected_driver}, tapi terbaca {gate_event.driver_id}'
            })
    
    # 4. Same chassis IN/OUT pattern abnormal (e.g., out tanpa pernah recorded in)
    last_event = get_last_gate_event(gate_event.chassis_id)
    if last_event and last_event.direction == gate_event.direction:
        anomalies.append({
            'type': 'DUPLICATE_DIRECTION',
            'severity': 'MEDIUM',
            'message': f'Chassis {gate_event.chassis_id} sudah {gate_event.direction} sebelumnya, tidak ada lawan event'
        })
    
    return anomalies
```

---

## Complementary to Other Systems

### Auto-Attendance

Saat driver tag IN ke gate depo pagi → auto clock-in. Saat tag OUT sore → clock-out.

**Replace fingerprint scanner** untuk driver yang tidak datang ke kantor (mostly drive direct ke pickup port). Driver Anda yang **tidak punya gate transit** (dispatcher in office) tetap pakai fingerprint / face recognition.

### Asset Tracking

- Last-known location chassis = dimana terakhir scan (depo IRN out / depo IRN in)
- Idle alert: kalau chassis sudah IN > 30 hari → alert untuk loading order
- Stuck alert: chassis OUT > expected SLA (3-7 hari) → contact customer cas/storage charge

### Multi-Asset Triplet Audit

Kalau klaim "Pak Pardamean tarik chassis IRN40-027 hari Senin" tapi gate_event tidak ada record → **audit trail gap**. Sistem bisa flag.

---

## Anti-Patterns

❌ **Pakai LF/HF RFID (125kHz / 13.56MHz) untuk gate truck** — range 5-10cm, gak workable. UHF wajib.

❌ **Skip surge protector untuk hemat Rp 800k** — petir Batam ambruk, ganti reader Rp 3jt + downtime 2 minggu.

❌ **Tag biasa di chassis metal** — failed read 80%. On-metal tag wajib.

❌ **Reader di luar tanpa enclosure tambahan** — IP65 minimum, tapi tropical humidity Batam masih bikin korosi cable. Sealed gland connection wajib.

❌ **Belum test multi-tag collision sebelum live** — 1 truk lewat dengan 3 tag bareng, 1 missed = data lost.

❌ **Backend tidak validate EPC vs entity table** — random EPC dari truk tetangga bisa pollute data. Always lookup whitelist.

---

## ROI Estimate

```
1 gate setup cost: Rp 22jt CapEx + Rp 200k/month OpEx
Tags total: Rp 11jt (one-time)
Total Year-1: Rp 35jt + Rp 2.4jt = Rp 37jt

Benefits:
- Auto-attendance eliminate paper sheet + admin verify (1 jam/hari × Rp 50k × 25 hari) = Rp 1.25jt/month = Rp 15jt/year
- Last-known chassis location automation (admin save 30 menit/hari) = Rp 7.5jt/year
- Anti-fraud audit trail (estimate prevent 1 case/month at Rp 500k value) = Rp 6jt/year
- SLA-based cas charge automation (lihat pillar 02 ROI) = Rp 8-15jt/year

Total Year-1 benefit: ~Rp 35-45jt
PAYBACK: 9-12 bulan
```

---

## Cross-References

- Tag tampering / sensor tampering → `05-anti-fraud-tech-stack.md`
- Yard slot integration with gate → `07-container-yard-management.md`
- Data model gate_event → `02-data-model-schema.md`
- Driver attendance UX → `03-driver-mobile-app-architecture.md`
