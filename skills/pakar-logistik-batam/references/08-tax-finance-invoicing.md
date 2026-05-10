# Pillar 08 — Tax, Finance & Invoicing: PPh 23, e-Faktur, Billing Cycle, Kasbon

## Pain Story

Bu Rinda (istri Pak Indra, pegang akuntansi IRN) tutup buku November tanggal 8 Desember (telat 8 hari dari ideal). Kenapa?

- 247 surat jalan ada di **3 lokasi**: 180 di sistem IRN web, 40 foto di WA, 27 di paper file (sopir lupa upload)
- 14 invoice ke customer perlu **faktur pajak** elektronik (e-faktur Coretax) — Bu Rinda harus input manual ke aplikasi DJP
- 3 customer dispute jumlah trip — perlu cross-check dengan WA history sopir
- Kasbon driver 26 entry — perlu reconcile dengan timesheet + setoran komisi
- PPh 23 (2% jasa angkutan) — beberapa customer potong-langsung, beberapa Bu Rinda harus collect dan setor sendiri

Total **64 jam Bu Rinda** untuk monthly close. Padahal ideally 8 jam.

Tax & finance bukan area yang fancy, tapi **kalau berantakan = invoicing telat = cash flow sengsara = bisnis sengsara**. 80% perusahaan trucking Indonesia macet di sini.

---

## Pajak yang Wajib Dipahami Owner Trucking + Crane

### A. PPh Pasal 23 — Pajak Penghasilan atas Jasa

**Tarif:** 2% dari nilai bruto (untuk jasa angkutan + freight forwarding + crane rental)

**Mekanisme:** 
- Customer (yang membayar IRN) **wajib potong** PPh 23 saat bayar invoice
- IRN terima net (98% dari invoice value)
- Customer setor PPh 23 ke kas negara, kasih bukti potong ke IRN
- IRN claim bukti potong sebagai kredit pajak saat lapor PPh Badan tahunan

**Catatan:**
- **Wajib NPWP customer + IRN** — kalau IRN tidak punya NPWP, dipotong 4% (double rate)
- **PPh 23 bisa di-cross-check** dengan e-Bupot DJP — pastikan customer benar-benar setor

### B. PPN (Pajak Pertambahan Nilai)

**Tarif:** 11% (per 2022, possibly 12% per 2025 plans)

**Mekanisme:**
- Kalau IRN PKP (Pengusaha Kena Pajak, omzet >Rp 4.8M/tahun → wajib PKP), invoice ke customer **plus PPN 11%**
- IRN terima invoice + PPN
- Setor PPN selisih (output PPN dari penjualan - input PPN dari beli BBM/sparepart) ke kas negara bulanan

**Catatan:**
- Customer B2B besar (SITC, manufaktur Batamindo) hampir selalu minta faktur pajak
- Customer kecil sometimes minta non-PPN (mereka non-PKP) — IRN bisa kasih kuitansi biasa
- **Faktur pajak wajib via e-Faktur / Coretax (per 2025+)**

### C. Coretax (Sistem Pajak Indonesia, Live 2025)

Coretax = sistem terpadu DJP yang menggantikan e-Faktur, e-Bupot, e-SPT, dll. Rolling out gradual:

| Sistem Lama | Coretax Replacement |
|---|---|
| e-Faktur | Faktur Pajak module |
| e-Bupot | Withholding Tax module |
| DJP Online | Tax Account |
| e-Filing SPT | Reporting module |

**Dampak ke trucking:**
- Generate faktur pajak harus via Coretax API atau web UI
- Ada API integration option → bisa otomasi (lihat sister skill)
- Adopt period: berbulan-bulan, banyak hiccup → siapkan fallback manual

### D. PPh Badan (Tahunan)

- Tarif: 22% (turun bertahap)
- Threshold UMKM (omzet < Rp 50M/tahun): 0.5% PP 23 final
- IRN dengan revenue ~Rp 15-20M/tahun = di atas UMKM threshold → tarif normal

### E. PPh Pasal 21 — Karyawan

**Untuk gaji admin, accountant, mechanic:**
- Tarif progresif 5-35%
- IRN potong saat gaji + setor + lapor

**Untuk driver komisi 25%:**
- Bukan gaji tetap → bisa diperlakukan sebagai PPh 23 (jasa) atau PPh 21 (insidentil)
- Konsultasi dengan pajak konsultan → typical practice trucking Indonesia: PPh 21 dengan PTKP (Penghasilan Tidak Kena Pajak) standard

---

## B2B Billing Cycle Indonesia — The Reality

### Standard Cycle

```
Day 0:    Service delivered (trip selesai)
Day 1-30: IRN aggregate trip → monthly invoice (akhir bulan)
Day 31:   Invoice diserahkan ke customer (email/print)
Day 32-45: Customer review, balikkan kalau ada dispute
Day 45-60: Customer process payment internal
Day 60-75: Bayar (kalau term 60 hari) atau 75-95 (kalau 90 hari)
Day 95+:  Kalau telat, follow-up call admin

REAL DSO Indonesian trucking: 70-100 hari rata-rata
```

### Faktur Pajak Workflow Friction

```
1. IRN draft invoice
2. IRN generate faktur pajak via Coretax (per invoice line atau aggregate)
3. Faktur pajak harus include: NPWP IRN, NPWP customer, kode jenis pajak, tarif
4. Customer terima faktur pajak + invoice
5. Customer review:
   - Cocokkan dengan PO mereka
   - Cocokkan dengan delivery receipt
   - Cocokkan dengan tax rate
6. Kalau ada mismatch → IRN harus "ralat" faktur pajak (extra step)
7. Customer process via accounting team (bisa 1-2 minggu)
8. Customer setor PPh 23 (potong 2%) → IRN dapat bukti potong
9. Customer transfer net ke rekening IRN
```

**Setiap friction = delay payment.** Sistem yang bagus eliminate manual reconciliation.

---

## Kasbon System (Yang Wajib Anda Rapihkan)

### Definisi

Kasbon = pinjaman / advance dari perusahaan ke karyawan (umumnya driver), dipotong dari gaji / komisi bertahap.

### Use Cases di Trucking

1. **BBM advance** — sopir butuh cash beli solar di luar rute reguler
2. **Family emergency** — anak sakit, ortu meninggal, dll
3. **Tools** — beli HP baru karena rusak, sepatu safety, dll
4. **Kebutuhan rutin** — sebelum gajian, kebutuhan dapur

### Pain dari IRN Transcript

> *"Kasbon driver 26 entry, total outstanding Rp 57.5jt, none repaid yet"*

26 entry × ~Rp 2.2jt rata = Rp 57.5jt cash IRN nyangkut di driver. Ini "modal kerja yang dipinjamkan" — tidak menghasilkan revenue.

### Best Practice Kasbon Management

```
APPROVAL TIER (delegate to admin):
  < Rp 500k: dispatcher approve, no question
  Rp 500k - 2jt: admin approve, log alasan
  > Rp 2jt: owner approve, dokumentasi alasan
  > Rp 5jt: owner approve + cek financial situation driver

REPAYMENT:
  Auto-deduct 20-30% dari komisi per trip until paid
  Maximum tenure 6 bulan
  Tidak boleh stack kasbon baru sebelum yang lama lunas

DOCUMENTATION:
  - Tanda tangan driver
  - Approval record
  - Deduction schedule
  - Outstanding balance terlihat di driver app

LIMIT:
  - Max kasbon outstanding = 50% dari gaji 1 bulan rata-rata
  - 3x kasbon dalam 6 bulan tanpa pelunasan → review serius
```

### Anti-Pattern Kasbon

❌ **Kasbon tanpa dokumentasi** — saat dispute, IRN kalah
❌ **Tidak ada deduction schedule** — kasbon jadi "hadiah"
❌ **Kasbon terus tumbuh tanpa alarm** — Rp 57.5jt outstanding = strategic risk
❌ **Kasbon untuk hal yang harusnya gaji** — kalau driver butuh kasbon tiap bulan = gaji tidak cukup → revisit pay model

---

## Wrong Solution / Gimmick Trap

❌ **"Pakai akuntansi enterprise full-feature"** — SAP / Oracle ERP overkill, tidak punya hook ke trucking workflow.

❌ **"Pakai akuntansi gratisan internet (Wave, etc)"** — tidak compliant Indonesian tax format.

❌ **"Outsource semua ke akuntan publik"** — tidak salah, tapi mahal Rp 5-15jt/bulan dan **delay closing 1-2 minggu** karena bolak-balik dokumen.

❌ **"Tidak setor PPh 23 / PPN"** — tax authority audit cepat menemukan, fine + bunga + sanksi pidana untuk extreme case.

---

## Right Solution by OUTCOME

| Solusi | Outcome | Effort |
|---|---|---|
| **Auto invoice generation** dari delivery completed (akhir bulan otomatis aggregate) | Bu Rinda dari 64 jam → 8 jam closing = **Rp 30jt/tahun saved time** | 1 bulan dev |
| **Auto faktur pajak via Coretax API** | Eliminate manual entry, reduce errors | 2-3 bulan dev (Coretax API masih maturing) |
| **Customer DSO dashboard** + auto reminder email | Reduce DSO 5-10 hari = **Rp 100-200jt cash unlock** | 1 minggu dev |
| **Kasbon ledger + auto-deduction** | Eliminate Rp 57.5jt outstanding (recover 50% in 6 bulan) = **Rp 28jt cash recovered** | 2 minggu dev |
| **Per-trip P&L + tax-ready accounting** (already PPh 23 / PPN aware) | Reduce reconciliation time 80% | Built into TMS core |
| **Bukti potong tracker** (e-Bupot reconciliation) | Recover unclaimed PPh 23 credit (commonly 5-10% lost) = Rp 5-15jt/year | 1 minggu dev |

---

## Customer Archetype

**"Bu Rinda" — Akuntan / Owner Wife**
- Pegang multiple Excel + WA + paper → fragmentation pain extreme
- Closing bulanan = mini-trauma tiap bulan
- **Pain emosional:** "Saya kerja 10 hari nutup buku, sopir-sopir mainnya langsung"
- **Reaksi positif:** "Closing bulanan dari 10 hari jadi 1 hari = saya bisa **mulai punya weekend lagi**"

**"Pak Faisal" — Konsultan Pajak Akuntansi (External)**
- 30-50 tahun, pegang 5-10 client trucking
- Pain: data client berantakan, banyak rewrite
- **Pain emosional:** dia harus jadi "data cleaner" sebelum jadi konsultan
- **Reaksi positif:** "Client yang pakai sistem ini, saya cukup kasih jasa konsultasi strategis, gak entry data."

---

## Quotable Hooks untuk Video Script

1. *"Bu Rinda Anda kerja 64 jam tutup buku November. Itu 8 hari kerja. Untuk apa? 90% data entry yang harusnya otomatis."*
2. *"Anda kasih kasbon Rp 2jt ke sopir. 6 bulan kemudian, 26 sopir x Rp 2jt = Rp 52jt nyangkut. Tanpa ledger, itu 'hadiah' bukan 'pinjaman'."*
3. *"DSO 90 hari kalikan 50 trip = Rp 1.5 milyar tertahan. Reduce 5 hari = Rp 80jt cash unlock."*
4. *"Coretax bukan threat, itu opportunity. Sistem yang sudah API-ready akan ungguli kompetitor yang masih manual entry."*
5. *"PPh 23 customer Anda potong, tapi Anda lupa claim sebagai kredit pajak. 5% bocor / tahun = Rp 8jt yang Anda transfer ke negara, lalu lupa minta balik."*

---

## Feature Implications (MoSCoW)

| Feature | Priority | ROI |
|---|---|---|
| Auto invoice from completed deliveries (monthly aggregate per customer) | **MUST** | Foundation |
| PPh 23 + PPN auto-calculate per invoice | **MUST** | Compliance |
| Coretax / e-Faktur integration | **MUST** | Save manual entry 64→8 jam |
| Customer DSO dashboard + auto reminder | **MUST** | Cash flow |
| Kasbon ledger + auto-deduction | **MUST** | Recover Rp 28jt+ |
| Bukti potong PPh 23 tracker (vs e-Bupot) | **SHOULD** | Tax credit recovery |
| Bank reconciliation (auto-match deposit vs invoice) | **SHOULD** | Closing speed |
| Tax filing helper (SPT preparation) | **COULD** | Automation level 2 |
| Predictive cash flow forecast (ML) | **WONT (MVP)** | Phase 2+ |

---

## Cross-References

- Working capital trap detail → `04-unit-economics.md`
- Customer DSO + tier strategy → `06-tacit-batam-context.md` + `07-decision-frameworks.md`
- Coretax / e-Faktur API technical → sister skill `smart-fleet-architect/09-integration-ai-ml-layer.md`
- Auto invoice generation tech → sister skill `smart-fleet-architect/02-data-model-schema.md`
