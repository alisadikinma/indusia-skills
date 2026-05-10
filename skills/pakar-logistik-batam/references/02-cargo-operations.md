# Pillar 02 — Cargo Operations: Container Lifecycle, SP2, Demurrage, Multi-Port

## Pain Story

Hari Jumat sore. Admin Cici di IRN dispatch 12 sopir untuk antar kontainer ke 12 customer berbeda. Pak Hamdardi tarik PT Esun di Tanjung Uncang, Pak Pardamean tarik PT Yixin di Bottom Centre, dst. Sampai jam 5 sore, **8 sopir kirim foto pengantaran lewat WA group**. 4 sopir tidak kirim. Cici WA satu-satu, 2 jawab "udah Bu, sebentar foto", 2 silent.

Senin pagi, owner ngecek invoice. **Ada 4 surat jalan dari Jumat hilang/tidak ter-upload**. Customer salah satu (TPK) komplain "saya belum tanda tangan terima barang", padahal sudah seminggu. Akhirnya: invoice 4 trip = Rp 3.6 juta tertunda 2 minggu, customer questionable, sopir defensif "saya ada antar kok bu, fotonya kirim WA tapi mungkin hilang".

Ini terjadi tiap minggu. Bukan sopir bohong. Bukan admin lalai. Ini sistem yang lupa **container lifecycle ada lebih dari 1 leg**.

---

## Container Lifecycle (Yang Sebenarnya)

```
[1. KAPAL SANDAR di Hamin 2/3]
        ↓ (2-6 jam ETA SP2)
[2. SP2 KELUAR]
        ↓ (sopir IRN dispatched)
[3. PICKUP di terminal] → foto + timestamp
        ↓ (1-3 jam delivery)
[4. ANTAR KE CUSTOMER] → foto + customer signature
        ↓ (3-7 hari customer bongkar)
[5. CUSTOMER LAPOR SUDAH KOSONG] (atau IRN cek manual)
        ↓ (sopir lain dispatched untuk tarik)
[6. TARIK EMPTY dari customer] → foto
        ↓
[7. SIMPAN DI DEPO IRN] → slot assignment (ini perlu CYMS, lihat smart-fleet-architect)
        ↓ (idle 2-30 hari)
[8. ORDER LOADING EXPORT masuk]
        ↓
[9. PICKUP EMPTY dari depo IRN] → fokus admin: pilih chassis yang ETA out paling cepat
        ↓
[10. ANTAR KE CUSTOMER untuk LOADING] → customer bongkar barang ekspor masuk
        ↓ (1-3 hari customer loading)
[11. PICKUP LOADED dari customer]
        ↓
[12. ANTAR KE TERMINAL untuk EKSPOR (PEB)]
        ↓
[13. SOPIR DAPAT TANDA TERIMA dari shipping line]
        ↓
[14. INVOICE GENERATED]
```

**Kunci insight:** 1 kontainer punya **2 leg minimum** (impor delivery + tarik empty), bisa **4 leg** (impor + tarik + loading export + drop terminal). Setiap leg = 1 trip = 1 invoice line. Sopir yang antar (leg 1) seringkali **bukan** sopir yang tarik (leg 2). Ini **multi-driver same container** — kalau sistem tidak tracking dengan benar, audit-trail rusak.

---

## Pain-Source Specific (dari transcript IRN)

### Pain A — Multi-leg dengan sopir berbeda
> *"Yang antar beda, yang tarik beda. Pas mau invoicing, document leg 1 ada di sopir A, leg 2 ada di sopir B. Admin bingung mana yang mana."*

**Root cause:** Sistem mencatat per-trip tanpa link container_id. Tidak ada single source-of-truth "kontainer ABC-123 status timeline."

**Solve:** Container_lifecycle table di backend, setiap event (pickup, deliver, customer_signed, return_request, return_pickup, depot_in, slot_assigned, ...) di-stamp ke container_id + timestamp + actor. Dispatch UI show "kontainer ABC-123 sekarang di status X."

### Pain B — Foto delivery hilang di WA
> *"Sopir foto pengantaran di WA, foto-foto bercampur dengan chat lain, hilang."*

**Root cause:** WhatsApp = wrong tool. WA bukan PDM (Document Management).

**Solve:** Driver app mandatory upload foto via app, **bukan WA**. Foto auto-tagged dengan delivery_id + GPS + timestamp + container_id. Stored di S3 / object storage. Backup auto.

### Pain C — Sopir tidak update status realtime
> *"Sopir sampai customer 30 menit lalu, tapi belum upload foto. Admin gak tahu sudah sampai atau belum."*

**Root cause:** Manual upload tidak terjadi otomatis. Driver dependence pada disiplin sopir.

**Solve:**
- **Geofence trigger** — saat GPS sopir masuk radius 200m customer location → app auto-popup "sudah sampai? Tap untuk konfirmasi + foto."
- **Time-decay incentive** — kalau upload foto >30 menit setelah ETA, komisi sopir auto-potong 5% (kontrak driver mention dulu).

### Pain D — Customer tidak konfirm received
> *"Customer bilang 'belum terima' padahal sopir bilang 'sudah antar'. Kata-kata tidak punya bukti."*

**Root cause:** Tidak ada **digital signature / e-PoD (electronic Proof of Delivery)** di point of delivery.

**Solve:**
- Sopir bawa tablet / pakai HP — customer tanda tangan langsung di screen
- Auto-email **surat jalan ber-tanda-tangan + foto** ke customer email
- Customer tidak bisa deny — punya jejak digital

---

## Demurrage & Detention — Wajib Hafal

| Term | Apa | Siapa Bayar | Free Time Indonesia |
|---|---|---|---|
| **Demurrage** | Charge dari shipping line ke importir karena container terlalu lama di terminal/yard | Importir/customer | 5-7 hari (varies per line) |
| **Detention** | Charge dari shipping line ke importir karena container terlalu lama di luar terminal (di customer / depo IRN) | Importir/customer | 7-14 hari (varies per line) |
| **Cas / Storage charge** | Charge dari IRN ke customer karena terlalu lama bongkar (chassis stuck) | Customer | 3-7 hari (deal-spesifik) |

**Critical untuk IRN:** Cas customer = revenue stream! Customer deal 3 hari, mereka bongkar 5 hari = IRN charge **2 hari × Rp X**. Banyak owner trucking lupa charge ini → revenue leakage.

**Tarif rough estimate IRN (terjebak transcript):**
- 3 hari free time, hari ke-4 onwards = Rp 200-500k/hari (tergantung ukuran kontainer & deal)
- Beberapa customer deal 7 hari free → cas mulai hari ke-8

**Demurrage rates Indonesia (berdasarkan Maersk/MSC/OOCL published):**
- Tier 1 (hari 1-7): free atau 0
- Tier 2 (hari 8-14): USD 20-40/hari per 20ft
- Tier 3 (hari 15+): USD 60-120/hari per 20ft, escalating

Untuk Batam-specific: Maersk publish "Batam, Indonesia Import Combined Demurrage and Detention Tariff" terpisah dari Indonesia mainland.

---

## Wrong Solution / Gimmick Trap

❌ **"Pakai blockchain untuk lifecycle tracking"** — overkill. Solve dengan database PostgreSQL + indexed status field.

❌ **"OCR semua surat jalan secara auto"** untuk eliminate paper — bagus tapi bukan MUST. Yang MUST: digitize at point-of-delivery (sopir foto + signature). OCR untuk konversi paper backlog = nice-to-have.

❌ **"Setiap kontainer pasang IoT sensor"** — kontainer punya owner shipping line, IRN tidak boleh modifikasi. Solve dengan **GPS dari truck**, lalu link container_id ke trip via barcode/manual entry/OCR.

---

## Right Solution by OUTCOME

| Solusi | Outcome | Effort |
|---|---|---|
| Container_lifecycle table + linked events | Eliminate "saya gak tau kontainer ABC sekarang dimana" — admin time saved 30 menit/hari = Rp 600k/bulan | 2 minggu dev |
| Driver app foto + GPS + signature ePoD | Eliminate "customer tidak konfirm" disputes = recover ~5% lost invoices = Rp 5-15jt/bulan untuk fleet 50 unit | 1-2 bulan dev (PWA) |
| Auto cas calculation berdasarkan SLA per customer | Recover revenue leakage cas yang tidak ditagih = ~Rp 3-8jt/bulan untuk 50 trip/bulan | 1 minggu dev |
| Auto-email surat jalan + foto ke customer pasca delivery | Eliminate phone calls, customer self-track = 10 jam/bulan customer service saved | 2 minggu dev |
| Geofence auto-trigger upload status | Eliminate sopir lupa upload, real-time visibility | 1 bulan dev (BG GPS reliability tricky di PWA — lihat sister skill) |

---

## Customer Archetype yang Paling Rasakan

**"Cici" — Admin Dispatcher di Trucking Company**
- 25-40 tahun, perempuan, kerja 6-7 hari/minggu
- Pegang WA group dengan 30+ sopir + 50+ customer
- HP dia ada 5 chat aktif sekaligus pukul 9 pagi
- **Pain emosional:** disuruh menjadi "sistem hidup" — manusia jadi data router
- **Reaksi positif:** "Akhirnya saya tidak perlu cek WA tiap 5 menit. Notification system kasih tahu kalau ada yang stuck."

**"Pak Hamdardi" — Sopir Senior**
- 30-50 tahun, sopir sudah 5+ tahun
- HP Android murah Rp 1-2jt
- Skeptis terhadap aplikasi (sebelumnya sudah disuruh pakai SITC app, ribet)
- **Pain emosional:** capek di-tagih admin "fotonya mana"
- **Reaksi positif:** "Aplikasi-nya 1 tombol foto, 1 tombol kirim — gak ribet. Komisi-nya ke-track jelas."

---

## Quotable Hooks untuk Video Script

1. *"Sopir Anda antar 12 kontainer sehari. Berapa fotonya yang sampai di sistem dalam 2 jam pertama? Coba hitung."*
2. *"Surat jalan di WA = surat jalan di kuburan. Senin Anda akan cari, tapi di antara ribuan chat — gak ketemu."*
3. *"Anda fokus tarif per trip. Sementara customer bongkar 5 hari pas deal 3 hari — Rp 400k/hari Anda lupa tagih, 30 trip/bulan = Rp 24jt revenue bocor."*
4. *"Container itu hidup 2 leg minimum. Sistem yang catat 1 leg = sistem yang bantu Anda nge-half-job."*
5. *"Dari Hamin 2 ke Tanjung Uncang ada 14 kemungkinan delay. Sistem yang bagus tunjukkan ke Anda 30 menit sebelum delay terjadi."*

---

## Feature Implications (MoSCoW)

| Feature | Priority | ROI Estimate |
|---|---|---|
| Container_lifecycle event log | **MUST** | Foundation seluruh sistem |
| Driver app: foto + GPS + signature ePoD + offline queue | **MUST** | Recover Rp 5-15jt/bulan |
| Geofence auto-prompt sopir | **MUST** | 30 menit/hari admin time |
| Auto-email surat jalan ke customer | **MUST** | 10 jam/bulan customer service |
| Auto cas calculation per SLA | **MUST** | Recover Rp 3-8jt/bulan |
| Multi-leg link (impor leg + tarik leg ke 1 container_id) | **SHOULD** | Audit trail, reduce dispute |
| OCR surat jalan untuk backlog | **COULD** | Reduce manual data entry historical |
| Customer self-service portal tracking | **SHOULD** | Reduce inbound calls |
| AI demurrage prediction | **WONT (MVP)** | Phase 2 — perlu data 6 bulan |

---

## Cross-References

- Multi-port pelabuhan detail → `01-domain-fundamentals.md`
- Container yard management at depot → sister skill `smart-fleet-architect/07-container-yard-management.md`
- Driver mobile app architecture decision → sister skill `smart-fleet-architect/03-driver-mobile-app-architecture.md`
- Customer credit risk (siapa yang sering molor bongkar) → `06-tacit-batam-context.md`
- ePoD legal validity Indonesia (UU ITE 11/2008) → `08-tax-finance-invoicing.md`
