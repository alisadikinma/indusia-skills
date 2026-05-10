# Pillar 01 — Domain Fundamentals: BP Batam, Bea Cukai, Pelabuhan, Shipping Line

## Pain Story (Yang Setiap Owner Trucking Batam Rasakan)

Ini hari Senin pagi. Bapak Indra, owner perusahaan trucking 25 unit di Batu Ampar, terima pesan WA dari SITC: "Kapal *MV Spil Singapura* sandar Hamin 2 jam 14:00, list 8 kontainer impor, ETA SP2 sore." Pak Indra sudah dispatch 8 sopir standby. Jam 16:30 sopir lapor: "Pak, SP2 belum keluar. Petugas Bea Cukai bilang manifest ada selisih 1 kolom." Jam 19:00 SP2 baru keluar. 5 sopir sudah pulang karena lewat jam kerja. Pak Indra terpaksa bayar overtime + chassis idle 1 malam = **Rp 2.4 juta hangus**, customer cas-nya delay 1 hari = customer marah, **trip economics minus**.

Skenario ini terjadi 3-5 kali per bulan rata-rata. Penyebab? Bukan kebodohan operator, bukan kemalasan SITC. Ini sistem yang "ribet by design" karena 18 kementerian/lembaga punya silo data yang tidak fully sinkron.

---

## Root Cause Analysis (Bukan Symptom-Treatment)

**Symptom:** SP2 keluar telat → sopir overtime → margin trip hilang.

**Root cause sebenarnya:**
1. **Document fragmentation** — manifest kapal di-submit ke ina-portnet (Pelindo), invoice ke Bea Cukai, license ke BP Batam. Mismatch satu kolom = stuck.
2. **Silo agency culture** — meskipun NLE (National Logistic Ecosystem, Inpres 5/2020) mandate integrasi, real-world masih banyak fallback ke proses manual / WhatsApp ke petugas.
3. **Free Trade Zone paradox** — Batam = FTZ tapi treatment-nya complex karena ada bidirectional flow: barang dari luar (impor ke FTZ ≠ impor ke Indonesia mainland) dan barang dari mainland (PEB ke FTZ).
4. **Visibility hilang antar pihak** — owner trucking tahu sopir standby, shipping line tahu kapal sandar, Bea Cukai tahu document status. Ketiganya **tidak share state real-time**.

Logistics cost di Indonesia = **23.5% of GDP** (target NLE: turunkan ke <17%). Untuk konteks: Singapore ~8%, Malaysia ~13%. **Batam stuck di angka ini bukan karena geografi — karena document choreography.**

---

## Knowledge Anchor — Yang Wajib Diingat

### Pelabuhan Batam

| Pelabuhan | Tipe | Catatan |
|---|---|---|
| **Batu Ampar** | Container terminal utama | Hamin (Harbour Mining) berth 1-3, deep water, kapal SITC/Infinity/Maersk regular call |
| **Sekupang** | Mixed cargo + ferry | Cargo umum, ferry penumpang ke Singapore |
| **Kabil** | Industrial port | Dekat Kabil Industrial Estate, banyak EPC oil & gas |
| **Batam Centre** | Ferry penumpang | Bukan untuk kargo |
| **Nongsapura** | Ferry penumpang | Bukan untuk kargo |

Untuk container haulage, **fokus Batu Ampar (90%) + sebagian Kabil**. Ferry port irrelevant untuk trucking.

### Shipping Line yang Aktif di Batam

| Line | Karakteristik | Reputasi (tacit) |
|---|---|---|
| **SITC** | China-based, Asia coastal regional, banyak feeder service Singapore→Batam | Strict SLA, prompt pay tapi rewel di klaim damage |
| **Infinity (Infiniti Marine)** | Singapore-based, Singapore-Batam liner + TG Pelapas-Batam | Rate kompetitif, lebih flexible tapi kadang miss schedule |
| **Maersk** | Global mainline, biasa transshipment via Singapore | Premium rate, paling reliable schedule |
| **MSC** | Mainline, fewer direct calls Batam | Local charges published — referensi tariff industry |
| **Evergreen, COSCO, OOCL, Hapag-Lloyd** | Mainline occasional | Bukan customer harian trucking Batam, tapi muncul di transshipment |

### Dokumen Inti

- **B/L (Bill of Lading)** — bukti angkut, dikeluarkan shipping line
- **D/O (Delivery Order)** — release authority dari shipping line ke consignee
- **PIB (Pemberitahuan Impor Barang)** — declaration impor ke Bea Cukai
- **PEB (Pemberitahuan Ekspor Barang)** — declaration ekspor
- **SP2 (Surat Penyerahan Peti Kemas)** — gate pass keluar terminal — **inilah dokumen yang sopir trucking butuh untuk pickup**
- **Surat Jalan (delivery note)** — internal trucking document untuk delivery ke customer
- **Faktur Pajak** — untuk billing B2B

### Regulasi Penting

- **PP 41/2021** — perubahan kawasan FTZ Batam
- **Inpres 5/2020** — NLE (National Logistic Ecosystem)
- **PMK Bea Cukai** — banyak, tapi yang paling sering disebut: PMK 199/2019 (transit), PMK 109/2020 (FTZ specific)
- **UU PDP 27/2022** — Personal Data Protection (relevant kalau bangun sistem yang collect data driver/customer)
- **Permenaker K3** — pesawat angkat-angkut (lihat pillar 03 untuk crane)

---

## Wrong Solution / Gimmick Trap

❌ **"Pakai ERP global (SAP/Oracle) untuk solve"** — ERP enterprise $50k+ implementation tidak punya local hooks (NLE API, Bea Cukai SP2 status). End up: ERP-nya kosong di field critical.

❌ **"Bangun blockchain-based document tracking"** — masalah-nya bukan trust antar pihak, masalah-nya document state tidak sinkron real-time. Solve dengan API push, bukan blockchain ledger.

❌ **"Pakai EDI (Electronic Data Interchange) standard"** — EDI mahal, slow adoption, di Batam rata-rata SITC pakai email/portal/WA, bukan EDI EDIFACT.

---

## Right Solution by OUTCOME

| Solusi | Outcome | Cost / Effort |
|---|---|---|
| **API integrator NLE** (third-party seperti Logisly punya in-house PPJK, atau bangun adapter sendiri) | SP2 status realtime, eliminate stuck-container surprises = save 4-8 jam/trip × 50 trip/bulan = ~Rp 8-15jt/bulan | Effort: 2-3 bulan dev untuk adapter, atau subscribe service Rp 5-10jt/bulan |
| **Email/WA parser otomatis** dari SITC/Infinity portal → draft delivery di sistem internal | Eliminate manual re-key admin, 1 jam/hari × 25 hari = Rp 1.25jt/bulan saved | Effort: 1-2 minggu dev |
| **Customer self-service tracking link** | Eliminate 80% "where's my container" calls = save 30 menit/hari admin = Rp 600k/bulan | Effort: 1 bulan dev |

---

## Customer Archetype (Untuk Targeting Video Promo)

**"Pak Indra" — Owner Trucking 20-50 Unit di Batam**
- 35-55 tahun, mantan sopir / mantan admin yang naik kelas
- Office di Batu Ampar atau Bengkong
- Gaji bulanan tidak besar tapi reinvest ke fleet
- Stress-driver: kapan SP2 keluar, sopir mana lagi cuti, customer mana telat bayar
- **Pain emosional terbesar:** kehilangan kontrol — tidak tahu real-time apa yang terjadi di lapangan
- **Reaksi positif:** "Akhirnya ada sistem yang nyatuin SITC + sopir + customer + invoice di satu layar"

---

## Quotable Hooks untuk Video Script

1. *"Logistik Batam itu bukan soal mindahin kontainer — itu soal mindahin Rp 3.560 triliun data antar 18 kementerian."*
2. *"Setiap kapal sandar di Hamin 2, ada 8 sopir nunggu di luar gate. Yang nunggu pertama bukan kontainer — itu SP2."*
3. *"FTZ kasih Anda bebas pajak, tapi gak kasih Anda bebas dari paper-trail. 1 kolom mismatch = 1 malam idle."*
4. *"Owner trucking pintar gak fight document complexity. Mereka bangun visibility supaya tahu lebih cepat dari kompetitor."*
5. *"Yang bedakan trucking 20 unit vs 200 unit di Batam bukan jumlah truk — itu jumlah jam mereka SADAR posisi setiap kontainer."*

---

## Feature Implications (MoSCoW untuk Sistem IRN)

| Feature | Priority | ROI Estimate |
|---|---|---|
| API/scraper SITC + Infinity portal → auto-create draft delivery | **MUST** | ~Rp 8-15jt/bulan saved (8 jam/trip × 50 trip) |
| WhatsApp Business API integration (parse incoming order) | **MUST** | ~Rp 1.25jt/bulan admin time |
| Multi-port awareness (Batu Ampar / Sekupang / Kabil tab) | **MUST** | Eliminate cross-port confusion |
| NLE INSW SP2 status check (manual or API) | **SHOULD** | ~Rp 5jt/bulan reduced overtime |
| Vessel ETA awareness (vesseltracker / VesselFinder embed) | **SHOULD** | Better dispatch planning |
| Direct EDI standard with shipping line | **WONT** | Industry adoption rendah, ROI rendah |
| Blockchain document tracking | **WONT** | Overkill, gimmick trap |

---

## Cross-References

- Container lifecycle detail → `02-cargo-operations.md`
- Multi-port operations & container yard → `02-cargo-operations.md` + sister skill `smart-fleet-architect/07-container-yard-management.md`
- Shipping line credit risk profiling → `06-tacit-batam-context.md`
- API integration tech approach → sister skill `smart-fleet-architect/09-integration-ai-ml-layer.md`
