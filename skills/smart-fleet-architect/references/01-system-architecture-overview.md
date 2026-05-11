# Pillar 01 — System Architecture Overview

> **Anchor (Day R3, 2026-05-11) — 3-Pillar Positioning + Owner Visibility Gap:** Every architecture component below maps to **AI (owner's brain extension) + IoT (sensory extension) + Mobile (nerve system)**. Every layer must answer: *which of 7 owner visibility gaps does this close?* See Addendum at file bottom for canonical 3-pillar architecture frame + CEO AI Assistant integration point (LOCK-3).

## Recommendation Decision Tree (Cepat)

```
Fleet size?
├── <50 vehicles → MONOLITH Django + PostgreSQL + Redis (skip semua microservice talk)
├── 50-200 vehicles → MONOLITH ber-modular (clear domain boundaries) — most likely IRN ada di sini
├── 200-500 vehicles → Modular monolith + 2-3 strategic microservices (sensor ingestion, mapping)
└── >500 vehicles → Microservice arsitektur penuh

Cloud or on-prem?
├── Data PII customer Indonesia (driver KTP, customer NPWP) → Indonesia-resident cloud (Biznet, AlibabaCloud Jakarta) atau on-prem
├── Sensor stream telemetri tinggi → Cloud lebih ekonomis (Biznet OK, AWS Singapore acceptable kalau via Direct Connect)
└── Hybrid → backend di Jakarta cloud, primary backup on-prem Batam office (PLN/listrik realita)
```

---

## Recommended Stack — IRN Specific (50-100 Vehicles, Crane Phase 2)

### Backend

| Layer | Pilihan | Alasan |
|---|---|---|
| **Framework** | **Django 5 + Django REST Framework** | Sudah ada dasar di IRN existing system (lihat screenshot — green Bootstrap, Indonesian datetime format = Django convention). Ekosistem mature, banyak admin built-in. |
| Alternative | FastAPI + SQLAlchemy | Pilih hanya kalau team lebih nyaman async-first. Lebih cepat untuk API performance, tapi admin UI tidak built-in. |
| **Database** | **PostgreSQL 16 + PostGIS extension** | PostGIS untuk geo (vehicle position, geofence, yard map). PostgreSQL handle 28k delivery/year easy. Replication mature. |
| **Cache & Queue** | **Redis 7** | Caching hot data, Celery task queue, rate limiting |
| **Async Worker** | **Celery + Celery Beat** | Task processing (email parsing, OCR, batch jobs), scheduled tasks |
| **Real-time messaging** | **MQTT (Mosquitto / EMQX)** | Sensor stream (GPS, fuel level, RFID gate events) — push-based, low latency |
| **Object Storage** | **S3-compatible (MinIO self-host atau Biznet Object Storage)** | Foto delivery, ePoD signature, document scan |

### Frontend

| Layer | Pilihan |
|---|---|
| **Admin / Dispatcher Web** | Vue 3 + Tailwind CSS + Pinia (state) atau Next.js + Tailwind |
| **Driver Mobile** | **PWA (Phase 0-3 month)** → **Flutter (Phase 1+)** — lihat ref `03-driver-mobile-app-architecture.md` |
| **Customer Self-Service** | Next.js (server-side render untuk public tracking links) |
| **Map Library** | **MapLibre GL JS** (open source fork of Mapbox GL) atau **Mapbox GL JS** kalau pakai tile Mapbox |

### DevOps

| Layer | Pilihan |
|---|---|
| **Container** | Docker + Docker Compose (untuk <200 vehicles, Kubernetes overkill) |
| **CI/CD** | GitLab CI atau GitHub Actions, deploy via SSH+Docker pull |
| **Monitoring** | Grafana + Prometheus (metrics), Loki (logs), Sentry (errors) |
| **Backup** | pg_dump nightly + S3 sync, 30-day retention |

---

## Estimated Cost Stack (IDR/Bulan)

### Cloud (Biznet Jakarta)

| Resource | Spec | Rp/bulan |
|---|---|---|
| App server | 4 vCPU, 8GB RAM, 80GB SSD | 800k-1.2jt |
| DB server | 4 vCPU, 16GB RAM, 200GB SSD | 1.2-1.8jt |
| Redis + MQTT | 2 vCPU, 4GB RAM | 500-700k |
| Object storage | 500GB | 300-500k |
| Bandwidth | 500GB egress | 1-2jt |
| Backup storage | 1TB | 200-400k |
| **TOTAL** | | **~Rp 4-7jt/bulan** |

### On-Premise (di Office IRN Batam)

| Item | Spec | One-time IDR | Yearly OpEx |
|---|---|---|---|
| Server (Dell/HP entry) | 8-core Xeon, 32GB RAM, 2TB SSD RAID | 25-40jt | - |
| UPS | 3000VA, 30 menit | 5-8jt | - |
| Internet 100Mbps Indihome Bizz | - | - | 12-18jt/year |
| Genset backup (PLN realita) | 5kVA | 15-25jt | maintenance 2-3jt/year |
| AC ruangan server | - | 8-12jt | listrik 5jt/year |
| Replacement reserve | (10% per year) | - | 4jt/year |
| **TOTAL** | | **~50-85jt CapEx** | **~25-30jt/year** |

**Rekomendasi:** **Cloud (Biznet Jakarta) untuk MVP**. Murah, scaleable, no hardware headache. Migrate hybrid kalau revenue dan compliance demand on-prem.

---

## Anti-Patterns

❌ **Microservice di MVP** — IRN bukan Netflix. Microservice = team > 10 dev. Monolith Django hingga 200 vehicles aman.

❌ **NoSQL (MongoDB) untuk transactional data** — Anda butuh ACID untuk invoice + payment. Postgres.

❌ **Kubernetes "untuk masa depan"** — overkill untuk 5-10 servers. Docker Compose cukup. K8s ke depan saat tim ops > 3 orang.

❌ **Cloud AWS Singapore tanpa Direct Connect** — latency 30-50ms tambahan, bandwidth mahal kalau egress ke Batam. Pakai Biznet/AlibabaCloud Jakarta minimal.

❌ **GraphQL "karena modern"** — REST cukup untuk 95% case. GraphQL menambah complexity tanpa solve pain real.

❌ **Self-host MQTT broker tanpa monitoring** — broker crash = sensor stream stop = blind. Pakai EMQX Cloud atau monitoring discipline.

---

## High-Level Architecture Diagram

```
┌────────────────────────────────────────────────────────────────────┐
│                         CLIENTS                                     │
├──────────────┬──────────────┬───────────────┬─────────────────────┤
│ Driver App   │ Dispatcher   │ Owner Web     │ Customer Self-Svc   │
│ (PWA/Flutter)│ Console (Vue)│ Dashboard     │ Tracking Link       │
└──────┬───────┴──────┬───────┴───────┬───────┴──────────┬──────────┘
       │              │               │                  │
       │              │               │                  │
       └──────────────┴───────────────┴──────────────────┘
                              │
                              │ HTTPS/REST + WebSocket
                              ▼
┌────────────────────────────────────────────────────────────────────┐
│                    APPLICATION SERVER (Django)                      │
│  ┌──────────┬───────────┬──────────┬──────────┬────────────────┐  │
│  │ Delivery │ Dispatch  │ Finance  │ Anti-Fra │ Compliance     │  │
│  │ Module   │ Module    │ Module   │ Module   │ (SILO/SIO)     │  │
│  └──────────┴───────────┴──────────┴──────────┴────────────────┘  │
└────┬──────────┬──────────┬──────────┬──────────┬─────────────────┘
     │          │          │          │          │
     ▼          ▼          ▼          ▼          ▼
┌─────────┐ ┌──────┐ ┌─────────┐ ┌──────────┐ ┌──────────────┐
│ Postgres│ │Redis │ │ MQTT    │ │ Object   │ │ External     │
│ +PostGIS│ │      │ │ Broker  │ │ Storage  │ │ Integration  │
└─────────┘ └──────┘ └────┬────┘ └──────────┘ └──────────────┘
                          │                    │
                          │                    ├── SITC/Infinity portal
                          │                    ├── WhatsApp Business API
                          │                    ├── Coretax e-Faktur API
                          │                    ├── NLE INSW
                          │                    └── Email parser (IMAP)
                          │
              ┌───────────┼───────────┐
              │           │           │
              ▼           ▼           ▼
         ┌────────┐  ┌────────┐  ┌──────────┐
         │GPS dev │  │Fuel    │  │UHF RFID  │
         │Telton/ │  │sensor  │  │Gate      │
         │Concox  │  │Omnicomm│  │Reader    │
         │(50 unit│  │(pilot 5│  │(2 gate)  │
         │)       │  │ → all) │  │          │
         └────────┘  └────────┘  └──────────┘
```

---

## Phase Roadmap

### MVP (Bulan 1-3)
- Monolith Django + Postgres + Redis di Biznet Jakarta cloud
- 1 server app + 1 DB server
- MQTT broker single node
- Manual deploy via Docker Compose
- Backup nightly via pg_dump
- **Estimated cost MVP: Rp 4-6jt/bulan**

### Phase 1 (Bulan 4-6)
- DB read replica untuk reporting/analytics
- Celery worker pisah node (handle email parser + OCR + batch jobs)
- Monitoring Grafana + Sentry mature
- Disaster recovery drill 1x

### Phase 2 (Bulan 7-12)
- Modular monolith refactor (clear domain boundaries antar Delivery, Dispatch, Finance, Anti-Fraud, Compliance)
- Sensor ingestion service pisah (mulai jadi microservice candidate)
- Multi-region backup
- Penetration test

### Future (Year 2+)
- Microservice partial split kalau scaling demand
- ML platform untuk anomaly + prediction
- Multi-tenant SaaS (kalau IRN mau SaaS-ifikasi platform-nya)

---

## Compliance Considerations

### UU PDP 27/2022 (Indonesia Personal Data Protection)

| Data | Treatment |
|---|---|
| Driver KTP, foto, GPS history | Personal data → enkripsi at-rest, audit log access |
| Customer NPWP, contact | Personal data + bisnis → consent untuk processing |
| Sensor telemetri vehicle | Bisnis data, no PII concern |
| Container info | Tidak personal, aman |

**Implementation:**
- Audit log untuk semua read/write personal data
- Right-to-be-forgotten endpoint (driver yang resign request data deletion)
- Data residency: hindari data PII keluar Indonesia tanpa consent

### Sektor Logistics Specific

- **Kemenhub** mungkin minta API integration untuk truck registration validation
- **Bea Cukai NLE INSW** — kalau IRN sebagai PPJK/freight forwarder, ada kewajiban data sharing

---

## Cross-References

- Data model detail → `02-data-model-schema.md`
- Driver app stack → `03-driver-mobile-app-architecture.md`
- Mapping cost detail → `08-mapping-gps-tracking-stack.md`
- Integration details → `09-integration-ai-ml-layer.md`
- Anti-fraud-with-dignity workflow (NEW Phase C) → `10-anti-fraud-with-dignity.md`
- CEO AI Assistant architecture (NEW Phase C, LOCK-3) → `11-ceo-ai-assistant-architecture.md`
- Sister skill business context → `pakar-logistik-batam/01-domain-fundamentals.md`

---

## Addendum (Day R3, 2026-05-11) — 3-Pillar Architecture Frame

### Pillar 1 — AI (Owner's Brain Extension)

Components yang menggantikan kebutuhan owner manual tracking:

| Component | Owner Gap Solved | Tech |
|---|---|---|
| Auto-dispatch driver↔order matching | Gap 1, 2 | Optimization (assignment problem, greedy + scoring) |
| Fraud anomaly detection (30-day rolling outlier) | Gap 3 | Statistical (z-score per-driver baseline, ESD test) |
| ETA prediction model | Gap 2 | Regression (XGBoost on historical trip features) |
| OCR invoice/BL auto-classification | Backend admin time savings | OCR + Coretax e-Faktur classification |
| Dashcam computer vision (driver fatigue) | Safety + Gap 1 | Edge inference on Android (TFLite Mediapipe) |
| **CEO AI Assistant (LLM + RAG + tool-calling)** | **ALL 7 gaps surface via conversation** | Claude Sonnet 4.6 / Opus 4.7 + tool-calling to ops data |

### Pillar 2 — IoT (Owner's Sensory Extension)

Hardware yang jadi "mata + telinga" owner di lapangan:

| Component | Owner Gap Solved | Hardware (locked CLAUDE.md) |
|---|---|---|
| ESP32-S3 BLE fuel bridge + 240-33 Ohm resistive sensor | Gap 3 | ~Rp 250-500k/vehicle |
| UHF RFID gate (920-925 MHz EPC Gen 2 / ISO 18000-6C) | Gap 4, 5, 6, 7 | Reader IP65 (user-procured) + EPC Gen 2 tags |
| Dual BLE gateway (driver HP + yard Raspberry Pi) | Gap 3 (auto-sync) | Pi 4 + long-range antenna + UPS Rp 800k-1.5jt MANDATORY |
| Dashcam (Phase 2) | Safety + Gap 1 evidence | TBD Phase 2 |
| Future: TPMS, container-seal | Phase 2+ | Deferred |

**Resilience design (WS8 input):** Yard station UPS Rp 800k-1.5jt MANDATORY karena PLN brownout monthly routine di Mukakuning/Tunas/Batamindo industrial estate. Tanpa UPS = RFID gate logs hilang during brownout window = Gap 4-7 data corruption.

### Pillar 3 — Mobile (Owner's Nerve System)

Software flow yang surface semua data ke owner di HP:

| Component | Owner Gap Solved | Stack (locked CLAUDE.md) |
|---|---|---|
| Driver app (PWA Phase 0 → Flutter Phase 1) | Gap 1 data capture | PWA bulan 1-3, migrate Flutter bulan 4-6 |
| Owner dashboard di HP (glance-able, signal-over-noise) | All 7 gaps surface | Flutter or React Native, design canon → `senior-ux-architect-id` (NEW skill) |
| Customer tracking app | Differentiator | Flutter |
| WhatsApp Business API | CEO AI Assistant + customer comms | Wati / Maytapi / 360dialog / Mekari Qontak (decision pending — Wati most likely) |

**Network defensibility (WS8 input):** Telkomsel >97% 4G coverage Batam, 5G median 88 Mbps Q1-Q2 2025. Phone-as-GPS technically defensible Phase 1. **No hardware GPS needed** (saves ~Rp 35-45jt CapEx vs 23 vehicles × hardware GPS tracker).

### Competitive Architecture Differentiator vs PT Eddi Batam Logistik

Eddi sudah punya TMS+IoT+big-data corporate-facing. INDUSIA differentiator architecture:

1. **Phone-as-GPS** lower CapEx (~Rp 35-45jt savings) → SME-friendly
2. **Fuel-fraud loop** (resistive sensor + 30-day rolling anomaly + owner-confirmation gate) — Eddi belum public document anti-fraud loop
3. **Owner-direct dashboard di HP** (BUKAN corporate big-data overload) — Eddi premium-tier corporate dashboard
4. **Crane add-on synergy** — Eddi pure trucking, IRN trucking+crane combo
5. **CEO AI Assistant conversational layer** — UNIQUE TO INDUSIA, no Indonesia logistik competitor has this

Detailed competitive analysis → `pakar-logistik-batam/06-tacit-batam-context.md` Addendum H (PT Eddi profile).
