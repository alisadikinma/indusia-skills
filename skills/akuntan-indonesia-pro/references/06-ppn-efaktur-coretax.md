# Reference 06 — PPN, e-Faktur, Coretax PER-11/PJ/2025

## A. PPN — Pajak Pertambahan Nilai

### Tarif (2025)

| Tahun | Tarif PPN | Catatan |
|---|---|---|
| Sebelum 1 Apr 2022 | 10% | UU PPN lama |
| 1 Apr 2022 – 31 Des 2024 | **11%** | UU HPP |
| 1 Jan 2025 dan seterusnya | **12%** (rencana, ada penyesuaian) | UU HPP — namun ada pengecualian/skema penyesuaian utk barang tertentu |

**KRITIKAL 2025:** Cek tarif yang berlaku per bulan, terutama untuk transaksi yang melintasi tahun 2024-2025. Kebijakan pemerintah bisa menyesuaikan (misal: PPN 12% hanya untuk barang mewah, sementara umum tetap 11%) — pantau update PMK terbaru.

### Mekanisme PPN

```
PPN Keluaran (Output) = PPN yang dipungut dari pembeli saat menjual barang/jasa
PPN Masukan (Input) = PPN yang dibayar saat beli barang/jasa kena PPN

PPN TERUTANG/LEBIH BAYAR = PPN Keluaran − PPN Masukan
```

**Kalau hasilnya:**
- **Positif (Keluaran > Masukan)** → setor PPN ke kas negara
- **Negatif (Masukan > Keluaran)** → **Lebih Bayar**, bisa:
  - **Kompensasi** ke masa pajak berikutnya (sederhana, no audit)
  - **Restitusi** (tarik balik cash, tapi wajib audit DJP — proses berbulan-bulan)

### Contoh Hitung PPN Bulanan

```
Penjualan PT XYZ bulan Mei 2025: Rp 800.000.000 (DPP)
PPN Keluaran (11%):              Rp  88.000.000

Pembelian:
  - Bahan baku DPP Rp 400jt → PPN Masukan Rp 44jt
  - Sewa kantor DPP Rp 30jt → PPN Masukan Rp 3.3jt
  - BBM kendaraan ops DPP Rp 20jt → PPN Masukan Rp 2.2jt
  TOTAL PPN Masukan: Rp 49.5jt

PPN Terutang Mei 2025: 88jt − 49.5jt = Rp 38.500.000

Setor: max tgl 15 Juni 2025 (atau 20 jika via Coretax)
Lapor: SPT Masa PPN max tgl 20 Juni 2025
```

### Wajib PKP (Pengusaha Kena Pajak)

- **Wajib daftar PKP** kalau peredaran bruto **> Rp 4.8 milyar/thn**
- Boleh **opt-in PKP** (di bawah threshold) kalau:
  - Customer B2B yang minta faktur pajak
  - Mau klaim PPN Masukan untuk capex besar (manfaat tax savings)

### PPN Tidak Dipungut / Dibebaskan

- Ekspor barang/jasa
- Penyerahan dalam KEK (Kawasan Ekonomi Khusus)
- Penyerahan ke Bea Cukai (impor sementara)
- Barang/jasa yang dibebaskan PPN per pasal 16B UU PPN

### Faktur Pajak — Wajib di Setiap Penyerahan PKP

**Komponen wajib faktur pajak (Pasal 13(5) UU PPN + PER-03/PJ/2022):**
1. Nama, alamat, NPWP penjual (PKP)
2. Nama, alamat, NPWP/NIK pembeli
3. Jenis barang/jasa, kuantitas, harga
4. **DPP** (Dasar Pengenaan Pajak)
5. PPN yang dipungut
6. Kode dan nomor seri faktur pajak
7. Tanggal faktur
8. Nama & tanda tangan yang berhak (atau e-signature)

**Status faktur (Coretax):**
- 01 — Penyerahan kepada selain Pemungut
- 04 — Penyerahan kepada Pemungut Lainnya (misal: PT BUMN ditunjuk pemungut)
- 06 — Penyerahan dengan Tarif Lain (PPN dipungut sendiri)
- 07 — Penyerahan PPN tidak dipungut
- 08 — Penyerahan PPN dibebaskan

---

## B. e-Faktur

### Sejarah Singkat
- 2014: e-Faktur 1.0 launch
- 2020: e-Faktur 3.0 (web-based)
- 2024-2025: **Migrasi ke Coretax** — e-Faktur diintegrasikan ke modul Faktur Pajak Coretax

### PER-11/PJ/2025 — Update Penting

1. **Deadline upload e-Faktur diperpanjang dari tgl 15 → tgl 20** bulan berikutnya
2. **Konsolidasi SPT Masa**: PPh Pasal 22, 23, 15, 4(2) digabung jadi **SPT Masa Unifikasi**
3. **OP usaha wajib jadi pemotong** untuk transaksi sewa:
   - Sewa peralatan/aset selain T&B → potong **PPh 23 (2%)**
   - Sewa tanah & bangunan → potong **PPh Final Pasal 4(2) (10%)**
4. **NPWP-NIK 16 digit integration** — semua NPWP wajib link ke NIK e-KTP

### Validasi Faktur Pajak (Syarat Formal — PER-16/PJ/2021)

**Wajib dipastikan saat validasi e-Faktur masuk:**
- Nama vendor match dengan NPWP-nya di database DJP
- NPWP/NIK pembeli (kita) tertera benar
- DPP & PPN angka match invoice fisik
- Tanggal faktur ≤ tanggal invoice (jangan post-dated)
- Kode & nomor seri faktur **match e-Faktur portal**
- Kalau **mismatched** → kredit pajak bisa ditolak DJP saat audit

### 25 Dokumen yang Disetarakan dengan Faktur Pajak (PER-16/PJ/2021)

Beberapa contoh penting:
1. **PIB (Pemberitahuan Impor Barang)** + bukti bayar PPN impor → kreditkan
2. **Tagihan PLN, PDAM, Telkom** dengan NPWP customer → kreditkan PPN-nya
3. **Tiket pesawat domestik / Airway Bill** dengan identitas → kreditkan PPN-nya
4. **Bukti pungutan pajak oleh Bea Cukai** atas penyerahan tertentu
5. **SPPB BULOG/DOLOG** untuk distribusi tepung/beras
6. Faktur penjualan kendaraan bermotor dari main dealer

### Cara Klaim PPN Masukan dari Dokumen Setara
- Wajib ada **NPWP/NIK pembeli** di dokumen
- Wajib ada **bukti bayar/setor pajak**
- Disclose di SPT Masa PPN
- Cross-check dengan e-Bupot DJP

---

## C. Coretax — Sistem Pajak Indonesia 2025+

### Apa Itu Coretax
Sistem terpadu DJP yang **menggantikan**:
- e-Faktur → Faktur Pajak Module
- e-Bupot → Withholding Tax Module
- DJP Online → Tax Account
- e-Filing SPT → Reporting Module

Pengganti komprehensif, real-time, terintegrasi dengan database BI, OJK, dan instansi lain.

### Modul Utama Coretax

1. **Tax Account** — dashboard WP, status SPT, SPMKP, restitusi
2. **Faktur Pajak Module** — generate, upload, validate e-Faktur
3. **Withholding Tax Module** — e-Bupot Unifikasi
4. **Reporting** — SPT Masa & Tahunan, lampiran
5. **Compliance Risk Management** — DJP automated risk scoring
6. **Audit & Examination** — SP2DK, pemeriksaan, sengketa

### Timeline Migrasi (Estimated)

- 2024 Q4: Pilot rollout untuk WP terpilih
- 2025 Jan: Live untuk semua WP
- 2025 H1: Periode adaptasi, fallback manual masih dimungkinkan
- 2025 H2+: Coretax full enforcement

**Reality check:** Adopsi Coretax di awal 2025 mengalami banyak hiccup teknis. Best practice: siapkan **fallback manual + double-entry tracking** sampai sistem stabil.

### Implikasi untuk Akuntan

- **Real-time fiscal reconciliation oleh DJP** — sistem automatically compare:
  - Revenue di e-Faktur vs revenue di SPT Tahunan PPh
  - Beban di SPT vs beban di neraca komparatif
  - Bukti potong yang diterima vendor vs yang disetor customer
  - Kalau tidak match → otomatis ada **Audit Flag**

- **Konsekuensi:** Akuntansi harus **konsisten antara komersial dan fiskal** dengan rekonsiliasi yang transparan. Selisih besar tanpa justifikasi → otomatis SP2DK.

---

## D. SPT Masa Unifikasi — Panduan Field

Form SPT Masa Unifikasi gabungkan PPh 22, 23, 15, 4(2) dalam satu lapor:

**Header:**
- Masa Pajak (bulan-tahun)
- Nama WP, NPWP
- Pembetulan ke berapa (0 = pertama)

**Bagian A — PPh Pasal 22**
- Daftar bukti potong yang dilakukan WP terhadap suplier (impor, BBM, pembelian dari pemerintah, dll)

**Bagian B — PPh Pasal 23**
- Daftar bukti potong jasa, sewa (selain T&B), dividen, royalti, bunga
- DPP, tarif, PPh dipotong, NPWP/NIK penerima

**Bagian C — PPh Pasal 15**
- Pelayaran, penerbangan, sewa kapal/pesawat asing

**Bagian D — PPh Pasal 4(2)**
- Sewa T&B, bunga deposito, hadiah, PPh Final UMKM, jasa konstruksi
- Tarif final yang dipotong

**Lampiran:**
- File e-Bupot (digital, ter-link otomatis di Coretax)
- Bukti setor (SSP) untuk setiap pasal

---

## E. Restitusi PPN Lebih Bayar

### Kapan Restitusi Layak Diajukan
- Lebih bayar > Rp 100 juta (di bawah ini biasanya kompensasi saja, restitusi rugi waktu)
- Bisnis ekspor (PPN Keluaran 0%, PPN Masukan tetap kreditkan)
- Tahun pertama operasi dengan capex besar

### Prosedur Restitusi
1. Lapor SPT Masa PPN dengan box "Lebih Bayar Direstitusikan" tercentang
2. DJP wajib audit (bukan opsional) — tim audit datang dalam ~3 bulan
3. Audit periode 12 bulan (bisa diperpanjang)
4. Hasil:
   - SKPLB → cair ke rekening WP
   - SKPN → tidak ada lebih bayar (sebagian/semua tidak diakui)
   - SKPKB → ternyata kurang bayar (ada koreksi yang menutupi lebih bayar)

**Risk-based reality:** Restitusi 100% selalu trigger audit. Akuntan harus **siap dengan dokumentasi sempurna**:
- Faktur pajak masukan original + e-Faktur portal print
- Invoice + bukti bayar
- Surat jalan / DO untuk pembelian
- Stock card untuk persediaan yang masuk
- Kontrak untuk jasa
- Dll.

### Restitusi PPh
- Mekanisme mirip: lapor SPT Tahunan dengan lebih bayar, request restitusi
- Audit lebih lama, periode lebih panjang
- Layak hanya kalau lebih bayar signifikan

---

## F. Common Errors PPN — Junior Accountant

1. **Lupa pisahkan PPN dari nilai jual** → DPP & PPN dijumlah di akun yang sama → tidak bisa hitung benar
2. **Klaim PPN Masukan untuk transaksi non-bisnis** → entertainment, mobil mewah pribadi → ditolak audit
3. **Faktur masukan dari vendor non-PKP** → tidak boleh kreditkan
4. **Faktur dengan nomor seri terlewat / duplicate** → otomatis ditolak Coretax
5. **Lupa retur penjualan / pembelian** sehingga PPN tidak dikoreksi
6. **Telat input ke e-Faktur portal** sehingga deadline 20 terlewati → bunga
7. **Mismatch tanggal**: faktur tertanggal Mei tapi diinput di Juni → bisa ditolak

---

## G. PPnBM (Pajak Penjualan atas Barang Mewah) — Sekilas

Tarif: 10% – 95% tergantung klasifikasi barang.

**Subjek umum:**
- Mobil mewah (engine size > tertentu)
- Kapal pesiar, pesawat pribadi
- Hunian super mewah
- Senjata api
- Perhiasan tertentu

**Mekanisme:** PPnBM dipungut **hanya sekali** di tingkat produsen / impor. Bukan PPN — tidak bisa dikreditkan oleh pembeli.

Akuntan jarang berurusan dengan PPnBM kecuali bisnis di sektor barang mewah.

---

## H. Praktis: Workflow Bulanan PPN

| Tanggal | Aktivitas |
|---|---|
| Tgl 1-25 bulan berjalan | Issue faktur pajak setiap penyerahan |
| Tgl 25 bulan berjalan | Cut-off invoice & data |
| Tgl 26-31 | Compile data PPN Keluaran + Masukan |
| Tgl 1-5 bulan berikutnya | Validasi e-Faktur masukan vs database DJP |
| Tgl 5-10 | Hitung PPN terutang, siapkan SSP |
| Tgl 10-15 | **Setor PPN** kalau ada |
| Tgl 15-20 | **Lapor SPT Masa PPN** |
| Tgl 20 (deadline final) | Last day upload e-Faktur (PER-11/PJ/2025) |

**Best practice:** Selesaikan tgl 15, jangan tunggu deadline 20. Kalau ada error system, masih ada buffer waktu.
