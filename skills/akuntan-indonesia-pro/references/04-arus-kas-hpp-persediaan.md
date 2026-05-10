# Reference 04 — Arus Kas, HPP Manufaktur, Persediaan

## Bagian A — Laporan Arus Kas

### Tiga Aktivitas Wajib

1. **Aktivitas Operasi (Operating)** — kas dari core business
2. **Aktivitas Investasi (Investing)** — kas terkait beli/jual aset jangka panjang
3. **Aktivitas Pendanaan (Financing)** — kas terkait modal & utang jangka panjang

### Metode Penyajian

#### Metode Langsung (Direct) — Lebih informatif, jarang dipakai karena kerja keras

```
ARUS KAS DARI AKTIVITAS OPERASI
  Penerimaan kas dari pelanggan                  Rp X
  (-) Pembayaran kas ke pemasok                 (Rp X)
  (-) Pembayaran kas ke karyawan                (Rp X)
  (-) Pembayaran kas untuk beban operasional    (Rp X)
  (-) Pembayaran bunga                          (Rp X)
  (-) Pembayaran pajak penghasilan              (Rp X)
  Kas neto dari aktivitas operasi                Rp X
```

#### Metode Tidak Langsung (Indirect) — Mayoritas perusahaan pakai

```
ARUS KAS DARI AKTIVITAS OPERASI
  Laba Bersih                                    Rp X
  Penyesuaian non-cash:
    + Beban Penyusutan                           Rp X
    + Beban Amortisasi                           Rp X
    + Beban ECL / Penurunan Nilai                Rp X
    + Beban Imbalan Pascakerja                   Rp X
    - Laba penjualan aset tetap                 (Rp X)
    + Rugi penjualan aset tetap                  Rp X
  Perubahan modal kerja:
    - (Kenaikan) Piutang Usaha                  (Rp X)
    + Penurunan Piutang Usaha                    Rp X
    - (Kenaikan) Persediaan                     (Rp X)
    + Penurunan Persediaan                       Rp X
    - (Kenaikan) Beban Dibayar Dimuka           (Rp X)
    + Kenaikan Utang Usaha                       Rp X
    - (Penurunan) Utang Usaha                   (Rp X)
    + Kenaikan Utang Pajak                       Rp X
    + Kenaikan Beban Yg Msh Hrs Dibayar          Rp X
  ─────────────────────────────────────────────────────
  KAS NETO DARI AKTIVITAS OPERASI                Rp X

ARUS KAS DARI AKTIVITAS INVESTASI
  - Pembelian aset tetap                        (Rp X)
  + Penjualan aset tetap                         Rp X
  - Pembelian investasi                         (Rp X)
  + Penjualan investasi                          Rp X
  + Pendapatan bunga investasi (kalau dpt cash)  Rp X
  ─────────────────────────────────────────────────────
  KAS NETO UNTUK AKTIVITAS INVESTASI            (Rp X)

ARUS KAS DARI AKTIVITAS PENDANAAN
  + Penerimaan utang bank                        Rp X
  - Pembayaran utang bank                       (Rp X)
  + Penerimaan setoran modal                     Rp X
  + Penerimaan pinjaman pemegang saham           Rp X
  - Pembayaran dividen                          (Rp X)
  ─────────────────────────────────────────────────────
  KAS NETO DARI AKTIVITAS PENDANAAN              Rp X

KENAIKAN (PENURUNAN) NETO KAS                    Rp X
KAS DAN SETARA KAS AWAL PERIODE                  Rp X
─────────────────────────────────────────────────────
KAS DAN SETARA KAS AKHIR PERIODE                 Rp X
```

**INVARIAN:** Kas Akhir di Arus Kas **HARUS SAMA** dengan Kas + Setara Kas di Neraca per tanggal yang sama. Kalau beda 1 sen → ada error.

### Kategorisasi Khas Kasus Indonesia

| Transaksi | Kategori |
|---|---|
| Bayar gaji karyawan | Operasi |
| Bayar PPh karyawan & PPh badan | Operasi |
| Beli mesin baru | Investasi |
| Bayar leasing mesin (PSAK 73) — bagian pokok | Pendanaan |
| Bayar leasing mesin — bagian bunga | Operasi (atau pendanaan, harus konsisten) |
| Setoran modal pemegang saham | Pendanaan |
| Pinjaman dari pemegang saham | Pendanaan |
| Pendapatan bunga deposito | Operasi (bisnis non-finansial) atau Investasi |
| Beban bunga pinjaman | Operasi (bisa Pendanaan) — pilih konsisten |

### Free Cash Flow (FCF) — Bukan SAK Item, Tapi Sering Diminta

```
FCF = Kas Operasi − Capex
    = Kas Operasi − (Pembelian Aset Tetap)
```

FCF positif konsisten = bisnis sehat, bisa bayar dividen / lunasi utang / ekspansi.

---

## Bagian B — HPP Manufaktur Detail

### Struktur Biaya Produksi

```
BIAYA PRODUKSI = BAHAN BAKU LANGSUNG (DM)
              + TENAGA KERJA LANGSUNG (DL)
              + OVERHEAD PABRIK (FOH)

PRIME COST = DM + DL
CONVERSION COST = DL + FOH
```

### Komponen FOH (Overhead Pabrik)

**Indirect Materials:**
- Bahan pembantu (lem, paku, oli mesin, sarung tangan)
- Sparepart kecil

**Indirect Labor:**
- Mandor / supervisor pabrik
- Mekanik & maintenance
- Quality control
- Cleaning service pabrik
- Security pabrik

**FOH Lainnya:**
- Listrik & air pabrik
- Gas / bahan bakar mesin
- Sewa pabrik
- Penyusutan mesin & gedung pabrik
- Asuransi pabrik & mesin
- Pemeliharaan mesin
- PBB pabrik

### Skema Roll-Forward Persediaan Manufaktur

```
PERSEDIAAN BAHAN BAKU
  Saldo Awal                              Rp X
  + Pembelian Bahan Baku                  Rp X
  + Beban Angkut Pembelian                Rp X
  - Retur Pembelian                      (Rp X)
  - Saldo Akhir                          (Rp X)
  ────────────────────────────────────────────
  BAHAN BAKU DIPAKAI                      Rp X    ──┐
                                                    │
PERSEDIAAN BARANG DALAM PROSES (WIP)               │
  Saldo Awal                              Rp X     │
  + Bahan Baku Dipakai                    ─────────┘
  + Tenaga Kerja Langsung                 Rp X
  + Overhead Pabrik                       Rp X
  - Saldo Akhir                          (Rp X)
  ────────────────────────────────────────────
  HARGA POKOK PRODUKSI (HPProd)           Rp X    ──┐
                                                    │
PERSEDIAAN BARANG JADI                              │
  Saldo Awal                              Rp X     │
  + Harga Pokok Produksi                  ─────────┘
  - Saldo Akhir                          (Rp X)
  ────────────────────────────────────────────
  HARGA POKOK PENJUALAN (HPP)             Rp X
```

### Contoh Hitungan Lengkap PT Manufaktur

**PT Aneka Karya — Periode 2025**

```
PERSEDIAAN BAHAN BAKU
  Saldo Awal (1 Jan 2025)                 Rp    180.000.000
  + Pembelian                             Rp  1.250.000.000
  + Beban Angkut Pembelian                Rp     35.000.000
  - Retur Pembelian                      (Rp     20.000.000)
  - Saldo Akhir (31 Des 2025)            (Rp    220.000.000)
  ───────────────────────────────────────────────────────
  BAHAN BAKU DIPAKAI                      Rp  1.225.000.000

PERSEDIAAN WIP
  Saldo Awal                              Rp     65.000.000
  + Bahan Baku Dipakai                    Rp  1.225.000.000
  + Tenaga Kerja Langsung                 Rp    480.000.000
  + Overhead Pabrik                       Rp    320.000.000
    - Indirect material              90jt
    - Indirect labor                 75jt
    - Listrik & air pabrik           45jt
    - Penyusutan mesin               60jt
    - Sewa pabrik                    30jt
    - Pemeliharaan & lain            20jt
  - Saldo Akhir                          (Rp     80.000.000)
  ───────────────────────────────────────────────────────
  HARGA POKOK PRODUKSI                    Rp  2.010.000.000

PERSEDIAAN BARANG JADI
  Saldo Awal                              Rp    240.000.000
  + Harga Pokok Produksi                  Rp  2.010.000.000
  - Saldo Akhir                          (Rp    280.000.000)
  ───────────────────────────────────────────────────────
  HARGA POKOK PENJUALAN                   Rp  1.970.000.000
```

### Markup Internal (Laba Produksi) — Optional Practice

Beberapa perusahaan internal markup saat transfer dari pabrik ke gudang penjualan untuk visibilitas margin produksi vs margin trading. **Catatan:** ini akun internal, **harus dieliminasi** saat konsolidasi/laporan keuangan akhir supaya net profit benar.

**Jurnal markup saat transfer:**
```
Db. Persediaan Barang Jadi               Rp X (cost + markup)
    Kr. Manufacturing Account                Rp X (cost)
    Kr. Laba Produksi (P/L)                  Rp X (markup)
```

**Eliminasi saat konsolidasi:**
```
Db. Laba Produksi                        Rp X
    Kr. HPP                                  Rp X
```

---

## Bagian C — Persediaan: Metode Penilaian

### Metode FIFO (First-In First-Out)
- Persediaan keluar = harga **lawas** (paling awal masuk) keluar duluan
- Persediaan akhir di neraca = harga **terbaru** (closer to fair value)
- **Saat inflasi:** HPP rendah → laba tinggi → pajak tinggi

### Metode Weighted Average (Rata-rata Tertimbang)
- Hitung **average cost per unit** setiap kali ada pembelian baru
- Persediaan keluar = harga rata-rata
- **Saat inflasi:** HPP moderate → laba moderate → pajak moderate

### Metode LIFO (Last-In First-Out)
- **DILARANG** di Indonesia (PSAK 14 / SAK EP) sejak 2008 — selaras dengan IFRS
- (LIFO masih boleh di US GAAP — bukan di Indonesia)

### Metode Identifikasi Khusus (Specific Identification)
- Untuk barang unik / bernilai tinggi (perhiasan, kendaraan, real estate)
- Tracking per unit

### Sistem Pencatatan: Periodik vs Perpetual

| Periodik | Perpetual |
|---|---|
| Saldo persediaan dihitung **akhir periode** via stock opname | Saldo persediaan **real-time** ter-update setiap transaksi |
| HPP dihitung dengan formula: SA + Pembelian − SAk | HPP dihitung saat penjualan terjadi |
| Murah, manual, cocok UMKM | Butuh sistem ERP, lebih akurat |
| Kelemahan: stock loss baru ketahuan di akhir periode | Stock loss bisa langsung detect |

### Stock Opname (Mandatory Akhir Periode)

**Wajib akhir tahun** untuk audit & SPT Tahunan. Best practice juga di akhir bulan.

**Prosedur stock opname yang benar:**
1. **Cut-off**: stop semua transaksi 1 hari penuh (atau hitung saat tutup malam)
2. **Tim independen**: bukan staff gudang sendirian — minimum 2 orang (gudang + accounting)
3. **Hitung fisik**: tag/label setiap unit, hitung 2x oleh 2 orang berbeda
4. **Bandingkan**: hasil fisik vs catatan sistem/pembukuan
5. **Investigasi selisih**: cari root cause (lost, broken, theft, miss-record)
6. **Adjusting entry**:

```
[Kalau opname < pembukuan = ada loss]
Db. HPP / Beban Kerugian Persediaan      Rp X
    Kr. Persediaan                            Rp X

[Kalau opname > pembukuan = ada gain]
Db. Persediaan                            Rp X
    Kr. HPP / Pendapatan Persediaan Lebih     Rp X
```

**Red Flag stock opname:**
- Selisih konsisten di gudang yang sama → indikasi **theft** (controller/supervisor melakukan)
- Selisih hanya pada item bernilai tinggi → indikasi **selective theft**
- Manajer gudang **menolak** ada stock opname mendadak (sidak) → indikasi **fraud**
- Stock yang katanya "broken/expired" lebih dari 5% nilai → wajib investigasi

---

## Persediaan Lambat / Obsolete (Slow-Moving / Obsolete Inventory)

**SAK EP wajib:** Persediaan dinilai pada **mana yang lebih rendah** antara:
- Cost (harga perolehan), atau
- Net Realizable Value (NRV — harga jual estimasi − biaya jual)

**Kalau NRV < Cost → akui penurunan nilai persediaan:**

```
Db. Beban Penurunan Nilai Persediaan     Rp X
    Kr. Cadangan Penurunan Nilai Persediaan   Rp X
```

**Konsekuensi pajak:** Beban penurunan nilai estimasi **NON-DEDUCTIBLE** sampai persediaan benar-benar dijual rugi atau di-write off → koreksi positif PPh Tahunan.

**Indikasi obsolete:**
- Tidak ada penjualan >180 hari
- Model lama yang sudah ada generasi baru
- Expired (untuk consumable / FMCG)
- Damaged tidak bisa direpair

---

## Common Pitfalls Persediaan & HPP

1. **Stock opname tanpa cut-off proper** → masuk barang yang baru diterima tapi belum tercatat
2. **Tidak hitung beban angkut pembelian** masuk persediaan → cost understated, HPP saat dijual understated
3. **Salah formula HPP manufaktur** — sering lupa WIP roll-forward → angka HPP salah
4. **Charge bahan baku ke beban langsung** (tanpa lewat persediaan dulu) → P&L distorted
5. **Tidak akui obsolete inventory** → aset overstated → audit catch
6. **FOH allocation arbitrary** → manipulasi laba antar produk
7. **Lupa sinkronkan kartu stock dengan saldo neraca** → discrepancy yang sulit dilacak
