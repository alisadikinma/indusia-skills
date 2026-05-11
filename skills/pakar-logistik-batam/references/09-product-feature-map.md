# Pillar 09 — Product Feature Map: Pain → Feature MoSCoW + ROI

> **Anchor (Day R3, 2026-05-11) — Owner Visibility Gap:** Pain klien = **owner blind to ops** (7 specific "gak tau"), BUKAN industry inefficiency. Setiap feature di file ini WAJIB map ke ≥1 dari 7 gap + ≥1 dari 3 pillar (AI/IoT/Mobile). See Owner Pain → Pillar Mapping section di bawah, dan CEO AI Assistant sebagai unifying feature spine.

## Tujuan File Ini

Menjadi **single source-of-truth** untuk product/feature roadmap. Setiap fitur di sistem IRN (atau produk SaaS yang akan dijual ke pasar Batam logistik) di-map ke:

1. **Pain** yang di-address (dengan referensi ke pillar 1-8)
2. **MoSCoW priority** (Must / Should / Could / Won't di MVP)
3. **ROI estimate** (Rp dihemat / jam dipangkas / % fraud reduction)
4. **Phase** (MVP 0-3 bulan / Phase 1: 4-6 / Phase 2: 7-12 / Future)
5. **Anti-feature** counterpart — fitur yang JANGAN dibangun walaupun keren

Ini menggabungkan keluaran 8 pillar sebelumnya jadi satu rencana product yang actionable.

---

## Master Feature Roadmap (24 Fitur Inti)

### CATEGORY A — Order & Delivery Lifecycle

| # | Feature | Pain Source | Priority | ROI Estimate | Phase |
|---|---|---|---|---|---|
| A1 | **Manual delivery entry + lifecycle tracking** | Pillar 02 | **MUST** | Foundation | MVP |
| A2 | **WhatsApp Business API parser** (auto-create draft delivery) | Pillar 01, 02 | **MUST** | Rp 1.25jt/bulan admin time | MVP |
| A3 | **Email parser SITC/Infinity portal** (auto-create draft) | Pillar 01 | **MUST** | Rp 8-15jt/bulan | MVP→Phase 1 |
| A4 | **Driver mobile PWA: foto + GPS + signature ePoD + offline queue** | Pillar 02, 05 | **MUST** | Rp 5-15jt/bulan | MVP |
| A5 | **Multi-leg link** (impor + tarik empty + load + drop ekspor → 1 container_id) | Pillar 02 | **SHOULD** | Audit trail, dispute reduction | MVP |
| A6 | **Auto-email surat jalan + foto ke customer** post-delivery | Pillar 02 | **MUST** | 10 jam/bulan customer service | MVP |
| A7 | **Customer self-service tracking link** | Pillar 02 | **SHOULD** | Reduce inbound calls 80% | Phase 1 |
| A8 | **Geofence auto-prompt sopir** (trigger upload status saat masuk radius customer) | Pillar 02, 05 | **MUST** | 30 menit/hari admin time | MVP |

### CATEGORY B — Dispatching & AI

| # | Feature | Pain Source | Priority | ROI | Phase |
|---|---|---|---|---|---|
| B1 | **Live map dispatcher console** (vehicle GPS pos + chassis + driver status) | Pillar 02 | **MUST** | Foundation visibility | MVP |
| B2 | **Manual driver-trip assignment** (admin pick) | Pillar 02 | **MUST** | Replace WA chaos | MVP |
| B3 | **AI auto-assign suggestion** (rank driver by distance + ETA + capacity + fatigue + cashbond) | Pillar 02, 04, 05, 07 | **SHOULD** | 20% admin time + 5% margin | Phase 1 |
| B4 | **Route batching suggestion** (cluster multi-deliveries 1 driver) | Pillar 04 | **SHOULD** | Revenue +10-20% via combo trips | Phase 1 |
| B5 | **SLA breach prediction + alert** (kalau ETA leleh, alert dispatcher 1 jam sebelum) | Pillar 02 | **COULD** | Reduce customer complaint | Phase 2 |

### CATEGORY C — Anti-Fraud & Driver Control

| # | Feature | Pain Source | Priority | ROI | Phase |
|---|---|---|---|---|---|
| C1 | **Fuel level sensor integration** (Omnicomm/Escort) | Pillar 05 | **MUST** | Rp 30-60jt/year | MVP (pilot 5 unit) → Phase 1 (full fleet) |
| C2 | **GPS tracker integration** (Teltonika/Concox) | Pillar 05 | **MUST** | Foundation | MVP |
| C3 | **Triangulation rule engine** (GPS + fuel sensor + struk + odometer) | Pillar 05 | **MUST** | Anti-fraud signal | MVP |
| C4 | **Anti-fraud alert dashboard + investigation workflow** | Pillar 05 | **MUST** | Real-time visibility | MVP |
| C5 | **AI dashcam integration** (driver behavior + identity) | Pillar 05 | **SHOULD** | Insurance premium reduce 15% | Phase 2 |
| C6 | **Bonus efisiensi BBM auto-calc** (km/L > target → bonus) | Pillar 04, 05 | **MUST** | Reduce fraud motivation 30% | MVP |
| C7 | **Sanction matrix workflow** (warning → SP1 → terminate) | Pillar 05 | **MUST** | Discipline framework | MVP |
| C8 | **Anonymous tip line in-app** | Pillar 05 | **SHOULD** | Norm-breaking | Phase 1 |
| C9 | **Driver performance score 90-day multi-factor** | Pillar 05, 07 | **MUST** | Hire/fire support | Phase 1 |

### CATEGORY D — Compliance (Crane-Specific)

| # | Feature | Pain Source | Priority | ROI | Phase |
|---|---|---|---|---|---|
| D1 | **SILO/SIA/Riksa Uji expiry tracker** (per crane unit) | Pillar 03 | **MUST** | Save Rp 200-800jt/year | MVP (kalau sudah masuk crane line) |
| D2 | **SIO operator expiry tracker** (per operator) | Pillar 03 | **MUST** | Same | MVP |
| D3 | **Insurance tracker** (CAR/EAR + TPL + operator PA) | Pillar 03 | **MUST** | Compliance | MVP |
| D4 | **Job site assessment digital form** (sopir/supervisor) | Pillar 03 | **SHOULD** | Reduce subsidence accident 80% | Phase 1 |
| D5 | **Lifting plan calculator** (load chart + radius input) | Pillar 03 | **COULD** | Operator safety tool | Phase 2 |
| D6 | **Operator fatigue tracker** (max 10 jam Permenaker) | Pillar 03, 05 | **MUST** | Compliance + safety | MVP |
| D7 | **Pricing calculator** (per crane class + mob + OT) | Pillar 03 | **SHOULD** | Eliminate quote errors | MVP |

### CATEGORY E — Finance & Tax

| # | Feature | Pain Source | Priority | ROI | Phase |
|---|---|---|---|---|---|
| E1 | **Auto invoice from completed deliveries** (monthly aggregate per customer) | Pillar 08 | **MUST** | Foundation | MVP |
| E2 | **PPh 23 + PPN auto-calc per invoice** | Pillar 08 | **MUST** | Compliance | MVP |
| E3 | **Coretax / e-Faktur integration** | Pillar 08 | **MUST** | Save 56 jam/bulan Bu Rinda | Phase 1 |
| E4 | **Customer DSO dashboard + auto reminder** | Pillar 04, 08 | **MUST** | Rp 100-200jt cash unlock | MVP |
| E5 | **Kasbon ledger + auto-deduction** | Pillar 08 | **MUST** | Rp 28jt+ recovered | MVP |
| E6 | **Auto cas / storage charge calculation** (per customer SLA) | Pillar 02 | **MUST** | Rp 3-8jt/bulan revenue recovery | MVP |
| E7 | **Bukti potong PPh 23 tracker** (e-Bupot reconciliation) | Pillar 08 | **SHOULD** | Recover Rp 5-15jt/year | Phase 1 |
| E8 | **Bank reconciliation auto-match** | Pillar 08 | **SHOULD** | Closing speed | Phase 1 |
| E9 | **Cash flow forecast rolling 30/60/90** | Pillar 04 | **MUST** | Prevent payday panic | Phase 1 |

### CATEGORY F — Decision Support

| # | Feature | Pain Source | Priority | ROI | Phase |
|---|---|---|---|---|---|
| F1 | **Per-trip P&L tracking real-time** | Pillar 04, 07 | **MUST** | Margin discipline | MVP |
| F2 | **Trip accept/reject decision helper** (auto-flag low margin) | Pillar 07 | **MUST** | Recover 5% margin | Phase 1 |
| F3 | **Vehicle / chassis utilization analytics** | Pillar 04, 07 | **SHOULD** | Buy/rent decision | Phase 1 |
| F4 | **Customer credit risk scoring** | Pillar 06, 07 | **SHOULD** | Reduce default risk | Phase 1 |
| F5 | **Idle chassis/vehicle alert** | Pillar 04 | **MUST** | Save Rp 6-12jt/bulan | MVP |
| F6 | **Combo route suggestion** (rule-based) | Pillar 04, 07 | **SHOULD** | Revenue +10-20% | Phase 1 |

### CATEGORY G — Hardware Integration & Asset

| # | Feature | Pain Source | Priority | ROI | Phase |
|---|---|---|---|---|---|
| G1 | **UHF RFID gate logging** (driver + vehicle + chassis triplet) | Pillar 02, 05 | **SHOULD** | Auto-attendance + asset tracking | Phase 1 |
| G2 | **Container Yard Management (CYMS)** — slot addressing + retrieval planner | (sister skill) | **COULD** | Phase 2 — wait until >300 chassis | Phase 2 |
| G3 | **Drone yard audit** | (advanced) | **WONT (MVP)** | Future-Tech | Future |

### CATEGORY H — Reporting & Analytics

| # | Feature | Pain Source | Priority | ROI | Phase |
|---|---|---|---|---|---|
| H1 | **Daily delivery report PDF** (per driver, per vehicle, per route) | Pillar 02, 08 | **MUST** | Operations visibility | MVP |
| H2 | **Monthly P&L dashboard** | Pillar 04 | **MUST** | Owner-level decisions | MVP |
| H3 | **Driver performance ranking + leaderboard** | Pillar 05, 07 | **SHOULD** | Behavioral incentive | Phase 1 |
| H4 | **Customer profitability analysis** (revenue vs collect cost) | Pillar 06, 07 | **COULD** | Strategic customer decisions | Phase 2 |

---

## MoSCoW Summary by Phase

### MVP (Bulan 1-3) — 23 fitur MUST

Ini adalah **minimum viable product** untuk validate dengan IRN dan 2-3 customer pilot:

**Order/Delivery (8):** A1, A2, A4, A5, A6, A8 + driver attendance basic
**Dispatcher (2):** B1, B2
**Anti-Fraud (5):** C1 pilot, C2, C3, C4, C6, C7
**Compliance (kalau crane sudah masuk) (3):** D1, D2, D3, D6
**Finance (5):** E1, E2, E4, E5, E6
**Decision (2):** F1, F5
**Reporting (2):** H1, H2

### Phase 1 (Bulan 4-6) — Add ~12 fitur

A3 email parser, A7 customer self-service, B3 AI auto-assign, B4 route batching, C5 dashcam pilot, C8 tip line, C9 performance score, D4 site assessment, E3 Coretax, E7 bukti potong, E8 bank reconcile, E9 cash flow forecast, F2 trip helper, F3 utilization, F4 credit risk, F6 combo route, G1 RFID gate, H3 leaderboard

### Phase 2 (Bulan 7-12) — Add ~5 fitur

B5 SLA prediction, C5 dashcam expand, D5 lifting plan, G2 CYMS, H4 customer profitability

### Future / WON'T MVP

G3 drone, AI-based yield optimization, blockchain document tracking, predictive maintenance ML, ML demurrage prediction

---

## Anti-Features (Yang TIDAK Dibangun, Walaupun Tampak Keren)

❌ **Blockchain document tracking** — solve dengan API + database
❌ **Crypto payment integration** — customer Indonesia tidak butuh
❌ **AR/VR for crane operator training** — overkill, video YouTube + classroom cukup
❌ **Voice command driver app** — sopir lebih akrab dengan tap, voice noise di kabin truk
❌ **AI chatbot 24/7 customer service** — phase 2+, MVP cukup form contact
❌ **Carbon footprint dashboard** — bukan trigger purchase di Indonesia logistics
❌ **Social network sopir in-app** — tidak boost engagement, distraction
❌ **NFT certificate / digital badge sopir** — gimmick

---

## Recommended MVP Scope Prioritized (Untuk IRN)

**3-month MVP: 18 features** (skip non-critical Must, defer Should/Could)

```
Month 1 — Foundation:
  □ Core data model (delivery, vehicle, chassis, driver, customer, fuel, kasbon)
  □ Driver mobile PWA basic (foto + GPS + signature)
  □ Auto invoice generation
  □ Customer DSO dashboard

Month 2 — Visibility:
  □ Live map dispatcher console
  □ WhatsApp Business API parser
  □ Auto-email surat jalan
  □ Geofence auto-prompt
  □ Per-trip P&L
  □ Daily report PDF

Month 3 — Anti-Fraud + Pilot Sensor:
  □ GPS tracker integration (Teltonika installed all 50 vehicles)
  □ Fuel level sensor pilot (5 vehicles)
  □ Triangulation rule engine v1
  □ Anti-fraud alert dashboard
  □ Bonus efisiensi BBM
  □ Sanction matrix workflow
  □ Kasbon ledger
  □ Auto cas calculation
```

**Target outcome MVP:**
- Driver compliance upload 95% (vs ~60% saat ini)
- DSO turun 5-10 hari
- Kasbon outstanding turun 50%
- Margin per trip visible real-time
- Anti-fraud alerts logged

---

## Customer Archetype yang Pakai File Ini

**"Anda" — Owner Strategist / Founder / Product Manager**
- Membaca file ini saat plan budget bulan depan
- Decide investasi tech mana priorities

**Quotable line untuk pitch deck:**

*"Bukan 100 fitur yang membedakan kami. Adalah 18 fitur yang **disolve pain spesifik** owner trucking Batam, dengan ROI per-fitur yang bisa dihitung dari hari pertama."*

---

## Cross-References

- Pain detail per pillar → `01-domain-fundamentals.md` sampai `08-tax-finance-invoicing.md`
- Tech implementation per fitur → sister skill `smart-fleet-architect/01-09` + `11-ceo-ai-assistant-architecture.md` (NEW Phase C)
- ROI calculation methodology → `04-unit-economics.md`

---

## Addendum — Owner Visibility Gap × 3-Pillar Feature Matrix (Generic Pattern)

> **Purpose:** Generic feature-prioritization pattern. Project binds specific owner-pain inventory via `project-variables.md`. Batam-industry market data + competitor profile are domain knowledge applicable to any Batam-logistik client.

### Owner Pain → Pillar Mapping (Pattern Template)

**Pattern principle:** SME fleet/logistik owners are typically **blind to operations** — they rely on phoning drivers one-by-one or waiting for admin recap. The 3-pillar solution (AI + IoT + Mobile) closes specific visibility gaps. Each project enumerates its own specific gap inventory; the canonical pattern below shows how gaps map to pillars + features.

**Canonical 7-gap inventory (typical Batam-logistik SME, project may adjust):**

| # | Typical Owner Pain ("gak tau...") | Pillar(s) | Feature Manifestation Pattern |
|---|---|---|---|
| 1 | Kondisi lapangan real-time | **Mobile + IoT** | Live dashboard di HP owner + GPS via driver phone |
| 2 | ETA ideal vs aktual driver | **AI + Mobile** | ETA prediction model + timeline view |
| 3 | Pencurian solar oleh driver | **IoT + AI** | Resistive fuel sensor + 30-day rolling anomaly outlier (anti-fraud-with-dignity loop) |
| 4 | Attendance karyawan (datang/pulang jam berapa) | **IoT + Mobile** | RFID gate logging triplet + HR dashboard |
| 5 | Posisi container di jalan | **IoT + Mobile** | RFID + GPS + asset map |
| 6 | Posisi chassis | **IoT + AI** | UHF RFID gate scan + location inference |
| 7 | Posisi container di gudang | **IoT + Mobile** | CYMS RFID grid + yard heatmap |

Project may add/remove gaps based on specific owner interview. Each gap MUST map to ≥1 pillar + ≥1 feature.

### Unifying Feature Pattern: CEO AI Assistant

All identified gaps can surface via **single conversational interface** (`{{asisten_name}}`) — accessible WhatsApp chat + voice call. Example query patterns:

- *"Truk `{{vehicle_id}}` sudah sampai mana?"* → Field-condition + container-location gaps (Mobile + IoT)
- *"Solar hari ini gimana?"* → Fuel-fraud gap (IoT + AI anomaly surfacing)
- *"Hari ini ada yang telat?"* → Attendance gap (IoT + AI summarize)
- *"Container `{{container_id}}` di mana?"* → Container-location gaps (IoT RFID + AI location inference)

Tech architecture template → sister skill `smart-fleet-architect/11-ceo-ai-assistant-architecture.md`.

**Critical product principle:** Asisten = single feature that is **bukan tambahan, tapi spine** — bukan *"owner harus belajar 18 fitur"*, tapi *"owner bisa tanya Asisten kapan saja"*. Frame for pitch: *"Bukan beli sistem rumit yang Bapak harus belajar. Tapi punya asisten yang Bapak bisa tanya."*

### Market Sizing — Batam Logistik Industry (Domain Knowledge)

This data is general Batam-industry knowledge from WS9 ground-truth research, applicable to any Batam-logistik client's TAM analysis:

| Metric | Value | Source |
|---|---|---|
| Batam container throughput 2025 | **797k TEUs (+18% YoY)** | WS9 deep report — BP Batam + shipping line aggregates |
| Batu Ampar growth 2025 | **+24% YoY** (fastest in Indonesia) | WS9 |
| Trucking demand Batam 2025 → 2027 | **978k moves/year → 1.44M** (+47%) | WS9 derived from TEU × multimodal ratio |
| Crane rental TAM Batam | **Rp 400-700 M/year** | WS9 derived from shipyard + industrial estate capex pulse |
| Digital crane booking platform Indonesia | **ZERO** (whitespace) | WS9 competitive scan |

**Project-specific ROI projection** (revenue uplift / margin uplift / payback period numbers) is NOT generic domain knowledge — bind in each project's `project-variables.md`.

### Competitive Whitespace vs Incumbent (PT Eddi Batam Logistik as Batam-Industry Reference)

PT Eddi Batam Logistik (Halvin Chandra, Batu Ampar) = incumbent Batam-logistik SME competitor with established TMS + IoT + big-data layer. Public-documented capability gaps that an INDUSIA project can target:

- (a) Phone-as-GPS lower CapEx framing (incumbent uses hardware GPS)
- (b) Fuel-fraud anti-fraud loop public-documented (none from incumbent)
- (c) Owner-direct dashboard di HP (incumbent uses corporate big-data dashboard)
- (d) Crane add-on synergy (incumbent is pure trucking)
- (e) CEO AI Assistant conversational layer (UNIQUE — no Indonesia logistik competitor has this)

Use this competitive analysis as starting reference for any Batam-logistik client positioning. Specific framings (e.g., naming comparisons with own client fleet size) bind via `project-variables.md`.

### Pitch Hook Pattern

```
"`{{competitor_incumbent}}` punya [X] truk dengan sistem corporate. 
`{{client_company_short}}` punya `{{client_fleet_size}}` truk dengan sistem yang 
owner langsung pakai dari HP — dan ada Asisten yang tahu kondisi lapangan 24/7."
```

Project binds `{{competitor_incumbent}}`, `{{client_company_short}}`, `{{client_fleet_size}}` via `project-variables.md`.
