# Reference 02 — Laporan Laba Rugi (P&L) Indonesia

## Format Single-Step vs Multi-Step

### Single-Step (sederhana — biasanya UMKM / SAK EMKM)
```
Pendapatan                                          Rp X
Beban (semua dijumlah)                             (Rp X)
─────────────────────────────────────────────────────────
Laba Sebelum Pajak                                  Rp X
PPh                                                (Rp X)
─────────────────────────────────────────────────────────
LABA BERSIH                                         Rp X
```

### Multi-Step (DIPILIH untuk SAK EP — visibility margin per layer)

```
PENDAPATAN
  Penjualan Bruto                                   Rp X
  (-) Retur Penjualan                              (Rp X)
  (-) Potongan Penjualan                           (Rp X)
  ─────────────────────────────────────────────────────────
  PENJUALAN NETO                                    Rp X

(-) HARGA POKOK PENJUALAN (HPP / COGS)             (Rp X)
  ─────────────────────────────────────────────────────────
  LABA KOTOR (Gross Profit)                         Rp X

(-) BEBAN OPERASIONAL
  Beban Penjualan
    Gaji & komisi sales                             Rp X
    Iklan & promosi                                 Rp X
    Beban distribusi & pengiriman                   Rp X
    Beban penyusutan kendaraan                      Rp X
  Beban Umum & Administrasi
    Gaji staff kantor                               Rp X
    Sewa kantor                                     Rp X
    Listrik, air, internet                          Rp X
    Beban penyusutan peralatan kantor               Rp X
    Beban kantor lain                               Rp X
  ─────────────────────────────────────────────────────────
  TOTAL BEBAN OPERASIONAL                          (Rp X)
  ─────────────────────────────────────────────────────────
  LABA OPERASIONAL (Operating Profit / EBIT)        Rp X

(+/-) PENDAPATAN/BEBAN LAIN-LAIN
  Pendapatan bunga                                  Rp X
  Pendapatan jasa giro                              Rp X
  (-) Beban bunga pinjaman                         (Rp X)
  (-) Rugi selisih kurs                            (Rp X)
  (+) Laba penjualan aset tetap                     Rp X
  ─────────────────────────────────────────────────────────
  LABA SEBELUM PAJAK (Profit Before Tax / EBT)      Rp X

(-) BEBAN PAJAK PENGHASILAN
  Beban pajak kini                                 (Rp X)
  Beban/manfaat pajak tangguhan                    (Rp X)
  ─────────────────────────────────────────────────────────
  LABA BERSIH (Net Profit / EAT)                    Rp X
```

---

## Definisi Margin Penting

| Margin | Formula | Industri Benchmark |
|---|---|---|
| **Gross Profit Margin** | Laba Kotor / Penjualan Neto | Trading 15-25%, Manufaktur 20-40%, Jasa 50-70% |
| **Operating Profit Margin (EBIT)** | Laba Operasional / Penjualan Neto | Trading 3-8%, Manufaktur 8-15%, Jasa 15-30% |
| **EBITDA Margin** | (EBIT + Depresiasi + Amortisasi) / Penjualan | Manufaktur 15-25%, Jasa 25-40% |
| **Net Profit Margin** | Laba Bersih / Penjualan Neto | Trading 2-6%, Manufaktur 5-12%, Jasa 12-25% |
| **Return on Assets (ROA)** | Laba Bersih / Total Aset | 5-15% sehat |
| **Return on Equity (ROE)** | Laba Bersih / Ekuitas | 10-25% sehat |

**Catatan industri:**
- Jasa konsultasi / IT / SaaS: net margin bisa 25%+ karena no inventory burden
- Trading FMCG: net margin tipis 1-3% — model volume
- Manufaktur padat modal (steel, semen): gross margin tinggi tapi EBITDA tertekan beban penyusutan

---

## EBITDA — Cara Hitung

```
EBITDA = Laba Operasional (EBIT)
       + Beban Penyusutan
       + Beban Amortisasi
```

**ATAU dari net profit (build-up):**
```
EBITDA = Laba Bersih (EAT)
       + Beban Pajak
       + Beban Bunga
       + Beban Penyusutan
       + Beban Amortisasi
```

**Penggunaan EBITDA:**
- Valuation (DCF, EV/EBITDA multiples) — biasanya investor pakai EV/EBITDA 5-15x untuk PT swasta
- Cash-generating capacity proxy (sebelum capex non-cash items)
- Cross-company / cross-country comparable (eliminate beda struktur modal & tax regime)

**Hati-hati:** EBITDA BUKAN cash flow. EBITDA mengabaikan:
- Capex aktual (cash out untuk beli aset)
- Working capital changes (piutang, persediaan, utang)
- Pembayaran bunga & pajak (yang cash)

---

## HPP (COGS) — Berdasarkan Sektor

### A. Trading (Perdagangan)
```
HPP = Persediaan Awal + Pembelian Bersih − Persediaan Akhir
```
- Pembelian Bersih = Pembelian Bruto + Beban Angkut Pembelian − Retur Pembelian − Potongan Pembelian

### B. Manufaktur
```
HPP = Persediaan Barang Jadi Awal
    + Harga Pokok Produksi (HPProd)
    − Persediaan Barang Jadi Akhir
```

**HPProd dihitung dari roll-forward WIP:**
```
HPProd = Persediaan Barang Dalam Proses (WIP) Awal
       + Bahan Baku Langsung Dipakai
       + Tenaga Kerja Langsung
       + Overhead Pabrik (FOH)
       − Persediaan Barang Dalam Proses (WIP) Akhir
```

**Bahan Baku Dipakai:**
```
Bahan Baku Dipakai = Persediaan Bahan Baku Awal
                   + Pembelian Bahan Baku
                   − Persediaan Bahan Baku Akhir
```

### C. Jasa
- HPP biasanya tidak ada / minimal
- Pengganti: **Beban Pokok Jasa** (jika user mau separasi)
  - Gaji staff yang langsung deliver service
  - Bahan habis pakai untuk service
  - Subkontraktor

---

## Akun-Akun Standar P&L (Chart of Accounts)

### PENDAPATAN (Revenue) — Akun 4xxx
- 4100 Penjualan Barang
- 4200 Pendapatan Jasa
- 4300 Pendapatan Sewa
- 4400 Pendapatan Komisi
- 4900 Pendapatan Lain-lain
- 4950 (Retur Penjualan) [contra revenue]
- 4960 (Potongan Penjualan) [contra revenue]

### HPP — Akun 5xxx
- 5100 Persediaan Awal
- 5200 Pembelian
- 5210 Beban Angkut Pembelian
- 5220 (Retur Pembelian)
- 5230 (Potongan Pembelian)
- 5300 (Persediaan Akhir)
- 5400 Bahan Baku Dipakai (manufaktur)
- 5500 Tenaga Kerja Langsung (manufaktur)
- 5600 Overhead Pabrik (manufaktur)
  - 5610 Listrik pabrik
  - 5620 Sewa pabrik
  - 5630 Penyusutan mesin
  - 5640 Penyusutan gedung pabrik
  - 5650 Pemeliharaan mesin
  - 5660 Bahan pembantu

### BEBAN OPERASIONAL — Akun 6xxx
**Beban Penjualan (61xx):**
- 6110 Gaji & komisi sales
- 6120 Iklan & promosi
- 6130 Beban distribusi
- 6140 Penyusutan kendaraan ops
- 6150 BBM kendaraan ops

**Beban Umum & Administrasi (62xx):**
- 6210 Gaji staff kantor
- 6220 Tunjangan & BPJS
- 6230 Sewa kantor
- 6240 Listrik & air kantor
- 6250 Telepon & internet
- 6260 ATK & supplies
- 6270 Penyusutan peralatan kantor
- 6280 Beban kantor lain
- 6290 Beban kerugian piutang (ECL — koreksi positif!)

### PENDAPATAN/BEBAN LAIN-LAIN — Akun 7xxx, 8xxx
- 7100 Pendapatan bunga
- 7200 Pendapatan jasa giro
- 7300 Laba penjualan aset tetap
- 7400 Pendapatan kurs
- 8100 Beban bunga
- 8200 Rugi penjualan aset tetap
- 8300 Beban kurs

### PAJAK — Akun 9xxx
- 9100 Beban PPh kini
- 9200 Beban pajak tangguhan
- 9300 (Manfaat pajak tangguhan) [contra]

---

## Contoh Lengkap: P&L PT Trading Skala Menengah (2025)

**PT Sentosa Niaga — Periode Jan-Des 2025**
```
PENJUALAN
  Penjualan Bruto                              Rp 12.500.000.000
  (-) Retur Penjualan                              (Rp 250.000.000)
  (-) Potongan Penjualan                           (Rp 100.000.000)
  PENJUALAN NETO                               Rp 12.150.000.000

HPP
  Persediaan Awal (1 Jan 2025)                  Rp  1.800.000.000
  + Pembelian                                   Rp  9.500.000.000
  + Beban Angkut Pembelian                      Rp     180.000.000
  - Retur Pembelian                             (Rp    150.000.000)
  - Persediaan Akhir (31 Des 2025)              (Rp 2.100.000.000)
  TOTAL HPP                                    (Rp  9.230.000.000)

LABA KOTOR                                     Rp  2.920.000.000   (24.0% margin)

BEBAN OPERASIONAL
  Gaji staff (sales + adm)                      Rp     960.000.000
  Sewa toko & gudang                            Rp     420.000.000
  Listrik, air, internet                        Rp      85.000.000
  Iklan & promosi                               Rp     150.000.000
  Penyusutan kendaraan & peralatan              Rp     180.000.000
  ECL piutang (1.2% × piutang akhir)            Rp      45.000.000   (← koreksi positif!)
  Beban operasional lain                        Rp     120.000.000
  TOTAL BEBAN OPERASIONAL                       (Rp  1.960.000.000)

LABA OPERASIONAL (EBIT)                        Rp     960.000.000   (7.9% margin)

PENDAPATAN/BEBAN LAIN-LAIN
  Pendapatan jasa giro                          Rp       8.000.000
  Beban bunga pinjaman bank                    (Rp     145.000.000)
  Rugi selisih kurs                            (Rp      22.000.000)
  TOTAL LAIN-LAIN                              (Rp     159.000.000)

LABA SEBELUM PAJAK                             Rp     801.000.000

PPh Badan
  Tarif normal 22% × Rp 801jt                   Rp     176.220.000
  Pajak tangguhan (atas ECL)                   (Rp       9.900.000)  (45jt × 22%)
  TOTAL BEBAN PAJAK                            (Rp     166.320.000)

LABA BERSIH                                    Rp     634.680.000   (5.2% margin)
```

**EBITDA = EBIT + Penyusutan = 960 + 180 = Rp 1.140.000.000 (9.4% margin)**

**Komentar akuntan senior:**
- Net margin 5.2% wajar untuk trading skala menengah
- Beban bunga 145jt → cek kalau masih reasonable vs revenue (1.2% — OK)
- Pajak tangguhan muncul karena ECL non-deductible (koreksi +) — temporary difference
- Cek di SPT Tahunan 1771: laba fiskal akan = 801 + 45 (ECL) = Rp 846jt → PPh terutang Rp 186.12jt → kredit angsuran PPh 25 → kurang/lebih bayar di PPh 29

---

## Format Penyajian Komparatif (Wajib SAK EP)

P&L wajib disajikan **komparatif 2 tahun**:

```
                                  2025              2024            % Change
PENJUALAN NETO              Rp 12.150.000.000  Rp 10.800.000.000     +12.5%
HPP                         (Rp  9.230.000.000) (Rp  8.400.000.000)   +9.9%
LABA KOTOR                  Rp  2.920.000.000   Rp  2.400.000.000     +21.7%
...
```

Komentar tahun-on-tahun di **Catatan Atas Laporan Keuangan (CALK)**.

---

## Common Pitfalls Junior Accountant

1. **Lupa pisahkan retur penjualan dari beban** → masuk ke Beban Operasional → laba kotor overstated
2. **Charge biaya entertainment ke "Beban Promosi"** → wajib disclosure di CALK + koreksi positif (50% non-deductible jika tidak ada daftar nominatif lengkap)
3. **Gabungkan HPP dengan Beban Operasional** → lose visibility margin kotor
4. **Posting penjualan saat invoice issued tapi performance obligation belum dipenuhi** (PSAK 72 / SAK EP 5-step) → revenue overstated
5. **Lupa adjust persediaan akhir** → HPP salah, laba salah, pajak salah
6. **Pakai metode penyusutan komersial untuk pajak** tanpa cek tarif fiskal → koreksi fiskal terlewat
