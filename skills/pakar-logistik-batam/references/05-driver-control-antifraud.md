# Pillar 05 — Driver Control & Anti-Fraud: Kencing Solar, Manipulasi Waktu, Control Matrix

## Pain Story

Pak Indra audit konsumsi BBM 6 bulan terakhir. Truk PM-22 (head Hino 500, sopir Pak X) konsumsi rata-rata **6.8 km/L**. Truk PM-26 (sama tipe, sama route mostly, sopir Pak Y) konsumsi **9.2 km/L**. Selisih 2.4 km/L. Atas konsumsi 80 trip/bulan × 30 km × Rp 7,500/L:

- PM-22: 80 × 30 / 6.8 × Rp 7,500 = **Rp 2,647,000/bulan BBM**
- PM-26: 80 × 30 / 9.2 × Rp 7,500 = **Rp 1,956,000/bulan BBM**
- Selisih: **Rp 691,000/bulan**

Untuk fleet 50 unit, kalau 30% sopir "kencing", potential loss = **30% × 50 × Rp 691k = Rp 10jt/bulan = Rp 124jt/tahun**.

Ini konservatif. Kasus ekstrem: Polda Sumut tangkap gudang penampungan solar curian, **21,000 liter** dari truk tangki Pertamina. Polda Jambi tangkap sopir yang "kencing" di pinggir jalan ke jeriken.

**Kencing solar bukan urban legend. Itu industri shadow yang real.** Yang naif tidak punya kontrol. Yang pintar bangun **control matrix**.

---

## Pemahaman Sopir — Kenapa Mereka Nyolong (Root Cause, Bukan Stigma)

❌ **Mitos:** "Sopir nakal, harus dihukum keras."
✅ **Realita:** Sopir nyolong karena **5 root cause**, dan masing-masing butuh control beda:

### Root Cause 1 — Gaji Riil Tidak Cukup

Komisi 25% dari (tarif - BBM - tol) = sekitar **Rp 5-8jt/bulan**. UMR Batam 2026 = ~Rp 5.4jt. Untuk sopir keluarga, UMR pas-pasan. Plus BBM dipotong sebelum komisi → sopir punya **incentive ekonomi** untuk turunkan BBM (kencing) untuk naikkan komisi.

**Solve:** revisit driver pay model. Bonus efisiensi BBM (km/L > target → bonus) **lebih efektif** dari sanction-only.

### Root Cause 2 — Peer Pressure & Norma Komunitas

WA group sopir trucking Batam = budaya "share modus". Sopir baru cepat belajar. "Bro, kalau gak ikutan, kapan kaya?"

**Solve:** **anonymous tip line** + reward whistleblower (Rp 500k cash kalau report valid). Kerusakan norma butuh insentif counter.

### Root Cause 3 — Family Emergency Liquidity

Anak sakit, butuh cash mendadak. Sopir tidak punya saving. Ngutang ke saudara malu. Kencing solar = **fast cash Rp 200-500k**.

**Solve:** **kasbon system yang humanis** — fast approval Rp 1-2jt untuk emergency, potong gaji bertahap. Eliminate "fast cash" temptation.

### Root Cause 4 — Pengawasan Lemah / Trust-Default

Owner tidak ada visibility — sopir tahu. "Ini kayak ATM tanpa kamera."

**Solve:** **triangulation tech** (lihat di bawah).

### Root Cause 5 — Long-Term No Career Path

Sopir tahu komisi 25% adalah ceiling. Tidak ada promosi. Tidak ada pension. **No future = no loyalty**.

**Solve:** career ladder sopir (junior → senior → fleet captain → trainer). Insentif loyalty (5 tahun = bonus Rp 5jt).

---

## Modus Kencing Solar — Yang Wajib Anda Tahu

### Modus 1 — "Kencing Saat Berhenti"
- Saat truk parkir (bisa di depo, SPBU, atau spot pinggir jalan)
- Buka penutup tangki, turunin solar via selang ke jeriken / drum
- **Volume:** 5-30 liter per kejadian
- **Detection:** GPS shows stopped > 10 menit di lokasi non-customer + fuel level drop signal

### Modus 2 — "Mark-up Struk SPBU"
- Sopir isi 50L tapi minta struk 80L dari operator SPBU (kongkalikong)
- Selisih 30L × Rp 7,500 = Rp 225k → split dengan operator
- **Detection:** struk vs odometer reading mismatch over time

### Modus 3 — "Tanggal-Akhir-Bulan Skema"
- Kasbon di awal bulan, isi BBM banyak akhir bulan via struk SPBU palsu, pakai duit kasbon untuk kebutuhan sendiri
- **Detection:** kasbon vs invoice BBM mismatch

### Modus 4 — "Pencurian Tangki Truk Tangki"
- Untuk sopir BBM kuota industri (relevan kalau IRN punya truk tangki, less common)
- Polda Sumut case: 21,000L disita

### Modus 5 — "Manipulasi Odometer / Long Route"
- Sopir bilang ke admin "saya lewat jalan jauh karena macet" — tarif sama, BBM lebih, komisi sama, tapi BBM hilang ke tangki sendiri
- **Detection:** GPS route history vs claimed route

---

## The Control Matrix (Bukan Single Tool)

### Diagram Konsep

```
                    ANTI-FRAUD CONTROL MATRIX
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
   TECHNICAL           BEHAVIORAL              FINANCIAL
   CONTROL             CONTROL                 CONTROL
        │                     │                     │
   ┌────┴────┐           ┌────┴────┐           ┌────┴────┐
   │ Fuel    │           │ Rotasi  │           │ Bonus   │
   │ sensor  │           │ driver  │           │ efisien │
   │         │           │         │           │ BBM     │
   ├─────────┤           ├─────────┤           ├─────────┤
   │ GPS +   │           │ Anony.  │           │ Kasbon  │
   │ geofen  │           │ tip     │           │ humani  │
   │         │           │ line    │           │         │
   ├─────────┤           ├─────────┤           ├─────────┤
   │ Dashcam │           │ Audit   │           │ Career  │
   │ AI      │           │ random  │           │ ladder  │
   │         │           │         │           │         │
   ├─────────┤           ├─────────┤           ├─────────┤
   │ Triangu │           │ Mystery │           │ Sanction│
   │ lation  │           │ shopper │           │ matrix  │
   │ rule    │           │ SPBU    │           │ progres.│
   └─────────┘           └─────────┘           └─────────┘
```

### Triangulation Rule Engine (Critical Concept)

**1 alert dari 1 sensor = noise. Alert dari 2+ sensor sekaligus = signal.**

Contoh rule:

```
RULE "FUEL_THEFT_CANDIDATE":
  IF (GPS_speed = 0 for >10 min)
  AND (location NOT IN [customer, depot, fuel_station])
  AND (fuel_level_drop > 5L within 15 min window)
  THEN flag as FRAUD_CANDIDATE, severity HIGH

RULE "FAKE_REFUEL":
  IF (fuel_card_charge = 80L)
  AND (fuel_sensor_increase < 60L within 30 min)
  THEN flag as FUEL_CARD_FRAUD

RULE "ROUTE_DEVIATION":
  IF (actual_km > expected_km × 1.30)
  AND (no traffic event detected in route)
  THEN flag as POTENTIAL_LONG_ROUTE
```

False positive handling:
- HIGH severity → manual review by supervisor
- 3 false positives in row → adjust threshold
- Confirmed fraud → log to driver_record + sanction matrix trigger

---

## Sanction Matrix (Progressive Discipline)

| Pelanggaran | First Time | Second Time (within 6 mo) | Third Time |
|---|---|---|---|
| Kencing solar (confirmed) | Verbal warning + denda Rp 500k + monitoring 30 hari | Suspended 7 hari + denda Rp 1jt | Terminate |
| Manipulasi odometer | Komisi potong 50% trip itu + warning | SP1 + monitoring 60 hari | Terminate |
| Manipulasi waktu (lambat sengaja) | Komisi potong | SP1 | Terminate |
| Kongkalikong SPBU | SP1 langsung + denda | Terminate |
| Sabotage sensor | SP1 langsung + denda Rp 1jt | Terminate |

**Critical:**
1. Sanction matrix harus **di kontrak driver** dari awal (transparent)
2. Documented trail (digital signature, foto bukti, log sistem)
3. **Konsisten** — tidak boleh "Pak X dimaafkan, Pak Y dipecat" → toxic culture
4. Reward sisi positif sama besar — bonus efisiensi BBM, perfect attendance

---

## Tech Stack Anti-Fraud (High-Level — Detail di Sister Skill)

### Layer 1 — Sensor & Data Collection

| Tech | Purpose | Cost (rough) |
|---|---|---|
| **Fuel level sensor capacitive** (Omnicomm LLS 5 / Escort TD-150) | Continuous fuel level monitoring | Rp 1.5-3jt/sensor + install |
| **GPS tracker** (Teltonika FMC640 / Concox JM-VL03) | Location + speed + ignition + harsh-driving | Rp 800k-1.6jt/unit |
| **AI dashcam dual-lens** (PRO7 / MileTek / 70mai budget) | Driver behavior, distraction, identity verification | Rp 2-8jt/unit |
| **Fuel card / e-toll** (Pertamina / Patra Niaga) | Cashless refuel, struk digital | Per-card setup |

### Layer 2 — Aggregation & Rule Engine

- Backend: PostgreSQL + time-series for sensor stream (PostGIS for geo)
- MQTT broker for sensor push
- Rule engine: simple if-then untuk start, ML anomaly detection later

### Layer 3 — Alert & Sanction Workflow

- Real-time dashboard for supervisor
- Mobile push to fleet manager when HIGH severity alert
- Auto-create incident ticket → assign to investigator → outcome logged

**Detail implementasi tech:** lihat sister skill `smart-fleet-architect/05-anti-fraud-tech-stack.md`

---

## Wrong Solution / Gimmick Trap

❌ **"Pasang fuel sensor saja, masalah selesai"** — sensor tanpa rule engine = noise. Sensor tanpa sanction matrix = sopir akan adapt (sabotage, atau pindah modus).

❌ **"Tracking GPS saja sudah cukup"** — GPS tracking ada di tahun 2010. Sopir sudah belajar caranya — pasang foil di GPS, buka battery, dll.

❌ **"Pakai AI machine learning canggih"** — datang setelah data 6+ bulan + rule-based foundation. Skip ML di MVP.

❌ **"Pecat sopir yang dicurigai"** — tanpa bukti = legal risk + lose investment training. Disiplinkan progressive.

❌ **"Dashcam saja sudah lengkap"** — dashcam tidak baca fuel level. Triangulation needed.

---

## Right Solution by OUTCOME

| Solusi | Outcome (Konservatif) | Effort |
|---|---|---|
| **Fuel sensor + GPS triangulation** di pilot 5 unit dulu | Detect 80% kencing solar, recover 50% loss = Rp 50-100k/sopir/bulan = Rp 30-60jt/tahun untuk fleet 50 | Rp 50-100jt CapEx + 1 bulan setup |
| **Bonus efisiensi BBM** (km/L > target → bonus Rp 100-300k/bulan) | Reduce kencing motivation 30-50% = Rp 30-70jt/tahun saved | Process change |
| **Anonymous tip line** + reward Rp 500k | Norm-breaking, 2-3 confirmed cases/year | Free |
| **AI dashcam pilot** (5 unit) | Reduce accident incident 87%, reduce insurance premium 15% = Rp 5-15jt/tahun | Rp 20-50jt CapEx |
| **Sanction matrix transparent** + di kontrak | Reduce fraud 30-50% just from fear | Process change |
| **Career ladder** (junior → senior → fleet captain) | Reduce turnover 40%, retention senior driver | Process change |
| **Fuel card** Pertamina + struk digital | Eliminate paper struk fraud | Per card setup |

---

## Customer Archetype yang Paling Rasakan

**"Pak Indra" — Owner Trucking 30-100 Unit**
- Sudah lihat selisih BBM antar unit
- Sudah curiga sopir tertentu, tapi tidak punya bukti
- Sudah baca kasus Polda di koran
- **Pain emosional:** "Saya bayar mereka, tapi mereka curi saya."
- **Reaksi positif:** "Akhirnya saya bisa tunjukkan bukti, bukan asumsi."

**"Bu Rinda" — Istri Owner / Akuntansi**
- Pegang BBM bookkeeping
- Lihat angka konsumsi naik tanpa proporsional dengan trip
- **Reaksi positif:** "Sistem yang flag truk-truk yang konsumsi tinggi otomatis."

---

## Quotable Hooks untuk Video Script

1. *"5 liter solar dari truk Anda, 22 trip per bulan, 1 sopir saja = Rp 2.5 juta hilang. Anda punya 50 truk."*
2. *"Yang nyolong solar bukan musuh Anda. Itu sistem yang gak kasih mereka pilihan lain. Solusinya: bangun sistem yang kasih pilihan."*
3. *"Fuel sensor saja gak cukup. Tanpa GPS + struk + odometer, itu cuma alarm yang sopir Anda akan belajar mainin."*
4. *"Sopir terbaik Anda mau bonus efisiensi. Sopir nyolong takut sensor. Sistem yang menang kasih dua-duanya."*
5. *"Anda gak akan pecat semua sopir. Tapi Anda harus tahu siapa yang efisien, siapa yang tidak. Itu beda strategi besar."*

---

## Feature Implications (MoSCoW)

| Feature | Priority | ROI |
|---|---|---|
| Fuel sensor integration + GPS triangulation rule engine | **MUST** | Rp 30-60jt/tahun saved |
| Anomaly alert dashboard untuk supervisor | **MUST** | Real-time visibility |
| Bonus efisiensi BBM auto-calc | **MUST** | Behavioral incentive |
| Sanction matrix workflow (warning → SP1 → terminate) | **MUST** | Disciplinary discipline |
| Anonymous tip line (in-app form) | **SHOULD** | Norm-breaking |
| AI dashcam integration (Phase 2) | **SHOULD** | Safety + verification |
| Mystery shopper SPBU rotation tracker | **COULD** | Counter struk fraud |
| Driver behavior scoring (multi-factor) | **SHOULD** | Career ladder data |
| ML-based anomaly detection | **WONT (MVP)** | Phase 2+ after data |

---

## Cross-References

- Tech detail fuel sensor + GPS + dashcam → sister skill `smart-fleet-architect/05-anti-fraud-tech-stack.md`
- RFID gate untuk auto-attendance + asset triangulation → sister skill `smart-fleet-architect/06-rfid-uhf-attendance-asset.md`
- Driver pay model (komisi 25% vs hybrid) → `04-unit-economics.md`
- Driver community context (WA group dynamics) → `06-tacit-batam-context.md`
- Hire/fire decision framework → `07-decision-frameworks.md`
