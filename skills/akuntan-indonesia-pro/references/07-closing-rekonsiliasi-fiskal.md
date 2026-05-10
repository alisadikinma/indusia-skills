# Reference 07 — Closing Bulanan, Yearly Close, Rekonsiliasi Fiskal

## A. Daily Closing Routine

Sebelum monthly close lancar, daily routine harus terjaga:

**D.1 Cash Count Fisik (per hari)**
- Reconcile saldo kas besar + kas kecil dengan catatan
- Kalau ada selisih, **investigasi sebelum end of day**
- Bukti: Cash Count Sheet ditandatangani 2 orang

**D.2 Bank Mutation Review**
- Pull rekening koran / e-statement
- Match setiap mutasi outgoing dengan voucher pembayaran
- Match setiap mutasi incoming dengan invoice / setoran customer
- **Red flag:** mutasi tanpa supporting → tag for investigation

**D.3 Real-time Journal Posting**
- Posting jurnal dari kuitansi/nota/faktur dalam **24 jam**
- Stamp "POSTED + tanggal + initial" pada bukti fisik untuk hindari double posting

**D.4 Document Validation**
- Validasi syarat formal: nama, NPWP/NIK, alamat, DPP, PPN
- Khusus e-Faktur masukan: cross-check dengan portal Coretax

---

## B. Weekly Closing Routine

**W.1 AR/AP Subledger Reconciliation**
- Total AR subledger = saldo Piutang Usaha di GL
- Total AP subledger = saldo Utang Usaha di GL
- Kalau beda → cari root cause minggu itu juga (jangan tunggu monthly)

**W.2 Return Verification**
- Audit semua Nota Kredit (sales return) dan Nota Debet (purchase return)
- Cross-check dengan slip terima fisik gudang
- Pastikan tidak ada return fiktif

**W.3 Cash Advance Aging**
- Review semua cash advance karyawan (kasbon)
- **Red flag: > 30 hari belum settled** — escalate ke supervisor karyawan
- Pattern berulang → indikasi personal financial distress / judol risk

**W.4 e-Faktur Preparation**
- Compile data PPN dari sales seminggu
- Validasi DPP & PPN untuk persiapan upload bulanan

**W.5 Weekly Cash Position Report**
- Generate single-step cash flow report untuk owner
- Net Cash = Total Cash In − Total Cash Out (minggu ini)
- Forecast cash position 4 minggu ke depan (untuk supplier payment + payroll)

---

## C. Monthly Closing — 12-Step Procedure (SAK EP Era)

### M.1 Bank Reconciliation (Setiap Rekening)

**Format Standard:**
```
Saldo Bank per Rekening Koran (akhir bulan)        Rp X
+ Setoran dalam Perjalanan (Deposits in Transit)   Rp X
- Cek/Giro Beredar (Outstanding Cheques)          (Rp X)
+/- Koreksi bank lainnya (jasa giro, biaya)        Rp X
─────────────────────────────────────────────────────────
Saldo Bank yang Disesuaikan                        Rp X    ┐
                                                            │ HARUS SAMA
Saldo Bank Menurut Pembukuan (akhir bulan)         Rp X    ┘
```

Setiap selisih → **wajib trace** sampai ketemu jurnal yang salah / mutasi yang belum dicatat.

### M.2 AR Aging & ECL Calculation

**Aging Schedule:**
```
Bucket               Saldo Piutang     %ECL    ECL Required
0-30 hari            Rp 800.000.000    0.5%    Rp  4.000.000
31-60 hari           Rp 350.000.000    1.5%    Rp  5.250.000
61-90 hari           Rp 150.000.000    5%      Rp  7.500.000
91-180 hari          Rp  80.000.000   15%      Rp 12.000.000
181-365 hari         Rp  40.000.000   50%      Rp 20.000.000
> 365 hari           Rp  20.000.000  100%      Rp 20.000.000
─────────────────────────────────────────────────────────────
TOTAL                                          Rp 68.750.000

Saldo CKP saat ini                             Rp 50.000.000
Tambahan ECL bulan ini (jurnal)                Rp 18.750.000
```

**Jurnal:**
```
Db. Beban Kerugian Penurunan Nilai Piutang     Rp 18.750.000
    Kr. Cadangan Kerugian Piutang                 Rp 18.750.000
```

### M.3 AP Aging & Loan Amortization

**Untuk pinjaman bank/leasing**, pakai EIR method (SAK EP):

Contoh: Pinjaman Rp 1M, bunga flat 12%/thn, 5 thn cicilan equal.
- Total bunga flat: 1M × 12% × 5 = Rp 600jt
- Total cicilan: 1.6M / 60 bln = Rp 26.67jt/bln

**EIR effective rate** ≈ 21.5% (lebih tinggi dari 12% flat karena pokok turun)

**Jurnal cicilan bulanan (EIR method, contoh bulan ke-1):**
```
Db. Utang Bank (pokok berdasarkan EIR)         Rp 8.730.000
Db. Beban Bunga (1M × 21.5% / 12 = ~17.9jt)    Rp 17.940.000
    Kr. Bank                                       Rp 26.670.000
```

(Pokok yang dikurangi naik bertahap setiap bulan; bunga turun)

### M.4 Stock Opname & Adjustment

- Stock opname fisik akhir bulan (atau quarterly minimum)
- Bandingkan opname vs stock card
- Adjustment jurnal kalau ada selisih

```
[Selisih kurang — fisik < pembukuan]
Db. HPP / Kerugian Persediaan                 Rp X
    Kr. Persediaan                                Rp X

[Selisih lebih — fisik > pembukuan]
Db. Persediaan                                Rp X
    Kr. HPP / Pendapatan Lain                     Rp X
```

**Manufacturing only — Markup transfer:**
```
Db. Persediaan Barang Jadi                    Rp 110jt (cost + 10% markup)
    Kr. Manufacturing Account                    Rp 100jt (cost)
    Kr. Laba Produksi (P/L)                      Rp  10jt (markup)
```

### M.5 Fixed Asset Impairment Test

**Cek setiap aset tetap:**
- Ada indikasi penurunan nilai? (kerusakan, obsolesi, kondisi pasar)
- Hitung **Recoverable Amount** = max(Fair Value − Cost to Sell, Value in Use)
- Bandingkan dengan **Carrying Amount** (nilai buku)

```
Kalau Carrying > Recoverable:
Db. Beban Penurunan Nilai Aset                Rp X
    Kr. Akumulasi Penurunan Nilai Aset            Rp X
```

### M.6 Related Party Loan PV Discounting

Contoh: Pinjaman pemegang saham Rp 3M, 3 thn, no interest. Market rate 8%.

```
PV = Rp 3M / (1.08)³ = Rp 2.381.500.000
Diskonto: Rp 618.500.000

Saat awal (transition):
Db. Laba Ditahan                              Rp 618.500.000
    Kr. Piutang Pemegang Saham (Diskonto)        Rp 618.500.000

Setiap akhir bulan, amortisasi diskonto sebagai beban bunga implicit:
Db. Beban Bunga Implisit                      Rp X
    Kr. Diskonto Piutang Pemegang Saham           Rp X
```

### M.7 Revenue Recognition Review (5-Step Model SAK EP)

Untuk kontrak multi-deliverable:
1. Identifikasi kontrak
2. Identifikasi performance obligations
3. Determine transaction price
4. Allocate price ke obligations
5. Recognize saat obligation fulfilled

**Defer revenue** kalau performance obligation belum terpenuhi:
```
Db. Pendapatan Diakui Sebelumnya              Rp X
    Kr. Pendapatan Diterima Dimuka                Rp X
(reclass dari Revenue ke Liability)
```

### M.8 Post-Employment Benefits Estimation

**Penyederhanaan SAK EP:**
```
Liabilitas IPK = Gaji × Tenor × PV Factor
```

Contoh: 50 karyawan rata-rata gaji Rp 7jt, tenor avg 4 thn.
- Cumulative liability ≈ 50 × 7jt × 4 = Rp 1.4M (rough estimate)
- PSAK 24 menggunakan metode aktuarial untuk hitung lebih presisi

**Jurnal akrual bulanan:**
```
Db. Beban Imbalan Pascakerja                  Rp X
    Kr. Liabilitas Imbalan Pascakerja             Rp X
```

### M.9 Tax Calculation (PPh & PPN)

- **PPh 21** karyawan: TER bulanan (PMK 168/2023)
- **PPh 23** atas pemotongan jasa, sewa, dividen
- **PPh 25** angsuran (PPh terutang thn lalu / 12)
- **PPh 4(2)** Final atas sewa T&B yang dibayar
- **PPN** Output − Input

Lihat reference 05 dan 06 untuk detail.

### M.10 Fiscal Reconciliation (Komersial → Fiskal)

**Langkah:**
1. Mulai dari **Laba Komersial Sebelum Pajak** (dari P&L)
2. Tambahkan **koreksi positif** (item yang tidak boleh deductible per pajak)
3. Kurangi **koreksi negatif** (item yang sudah dipajak final / tidak diakui pajak sebagai pendapatan)
4. Hasil = **PKP (Penghasilan Kena Pajak)**

**Koreksi Positif (item komersial deductible, fiskal NON-deductible):**

| Item | Alasan |
|---|---|
| Beban Kerugian Penurunan Nilai Piutang (ECL) | Pasal 6(1)h UU PPh: hanya piutang yang BENAR tidak tertagih dengan kriteria spesifik yang deductible |
| Beban Penurunan Nilai Aset (impairment) | Estimasi tidak diakui pajak |
| Beban Imbalan Pascakerja (estimasi) | Hanya deductible saat dibayar / disetor ke dana pensiun |
| Beban Entertainment tanpa daftar nominatif | 50% non-deductible (PSL 6(1)) |
| Beban Sumbangan (kecuali yang spesifik UU) | Non-deductible |
| PPh yang ditanggung perusahaan untuk karyawan | Non-deductible (alternatif: gross-up) |
| Sanksi pajak (denda, bunga) | Non-deductible |
| Pajak Masukan PPN yang tidak dapat dikreditkan | Non-deductible kalau di-charge ke beban tanpa kriteria |
| Premi asuransi pribadi pemilik | Non-deductible |
| Mobil sedan/jeep yang dipakai pemegang saham | 50% non-deductible (PMK 02/2010) |
| Beban yang tidak ada bukti supporting | Non-deductible |
| Beda susut komersial vs fiskal (yang lebih besar di komersial) | Selisih dikoreksi positif |

**Koreksi Negatif (item komersial sudah dimasukkan ke laba, fiskal tidak masuk):**

| Item | Alasan |
|---|---|
| Pendapatan bunga deposito (sudah PPh Final 20%) | Sudah final, tidak digabung |
| Pendapatan sewa T&B (sudah PPh Final 10%) | Sudah final |
| Hibah dari pihak afiliasi (yang memenuhi syarat) | Bukan objek pajak |
| Penghasilan UMKM yang sudah PPh Final 0.5% | Sudah final, tidak digabung |
| Beda susut komersial vs fiskal (yang lebih besar di fiskal) | Selisih dikoreksi negatif |

**Format Rekonsiliasi (untuk SPT 1771 Lampiran I):**

```
LABA KOMERSIAL SEBELUM PAJAK              Rp 801.000.000

KOREKSI POSITIF
  ECL piutang                              Rp  45.000.000
  Beban impairment aset                    Rp  20.000.000
  Beban imbalan kerja (akrual)             Rp  80.000.000
  Beban entertainment 50%                  Rp  12.000.000
  Beban sumbangan                          Rp   5.000.000
  Beda susut komersial > fiskal            Rp  15.000.000
  Sanksi pajak                             Rp   2.000.000
  TOTAL KOREKSI POSITIF                    Rp 179.000.000  (+)

KOREKSI NEGATIF
  Pendapatan jasa giro (PPh Final)        (Rp   8.000.000)
  Pendapatan sewa T&B (PPh Final)         (Rp  24.000.000)
  TOTAL KOREKSI NEGATIF                   (Rp  32.000.000) (−)

────────────────────────────────────────────────────────
PENGHASILAN KENA PAJAK (PKP)              Rp 948.000.000

PPh Badan 22%                             Rp 208.560.000
(atau pakai fasilitas Pasal 31E kalau peredaran ≤50M)

Kredit Pajak:
  PPh 22 (impor)                          (Rp   3.000.000)
  PPh 23 (jasa diterima)                  (Rp  18.000.000)
  PPh 25 (angsuran 12 bulan)              (Rp 145.000.000)
  TOTAL KREDIT                            (Rp 166.000.000)

PPh PASAL 29 (KURANG BAYAR)               Rp  42.560.000  ← setor sebelum lapor SPT
```

### M.11 Deferred Tax Recognition

Temporary differences (yang akan reverse di masa depan) → akui Aset/Liabilitas Pajak Tangguhan.

**Contoh: Beban ECL Rp 45jt non-deductible sekarang, tapi kalau real write-off jadi deductible nanti.**
- Temporary difference: Rp 45jt (deductible future)
- Aset Pajak Tangguhan: Rp 45jt × 22% = Rp 9.9jt

```
Db. Aset Pajak Tangguhan                      Rp  9.900.000
    Kr. Manfaat Pajak Tangguhan (P/L)            Rp  9.900.000
```

**Contoh: Beda susut komersial < fiskal Rp 15jt (fiskal pakai lebih cepat).**
- Temporary difference: Rp 15jt (taxable future — saat fiskal sudah habis susut, komersial masih ada beban susut yang lebih kecil → laba fiskal > komersial)
- Liabilitas Pajak Tangguhan: Rp 15jt × 22% = Rp 3.3jt

```
Db. Beban Pajak Tangguhan (P/L)               Rp  3.300.000
    Kr. Liabilitas Pajak Tangguhan                Rp  3.300.000
```

### M.12 SPT Masa Generation

- Generate SPT Masa PPh 21 (untuk karyawan)
- Generate SPT Masa Unifikasi (PPh 22, 23, 15, 4(2))
- Generate SPT Masa PPN
- Setor & lapor sesuai deadline (lihat reference 05 dan 06)

---

## D. Yearly Closing & SPT Tahunan

### Tambahan dari Monthly Close

**Y.1 Stock Opname Fisik Lengkap (wajib akhir tahun)**
- Tim independen, cut-off ketat, hitung 2x oleh 2 orang berbeda
- Adjustment di akhir tahun
- Konfirmasi dengan auditor (kalau ada audit)

**Y.2 Aktiva Tetap — Catatan Lengkap**
- Daftar aset tetap dengan nomor inventaris
- Cocokkan fisik vs kartu inventaris
- Update useful life, residual value kalau ada perubahan
- Hitung penyusutan komersial vs fiskal — siapkan untuk koreksi

**Y.3 Reklasifikasi Bagian Lancar Utang Jangka Panjang**
- Cek semua utang LT, identifikasi cicilan 12 bulan ke depan
- Reklasifikasi ke Liabilitas Jangka Pendek di neraca

**Y.4 Imbalan Pascakerja Aktuarial (kalau >100 karyawan)**
- Hire aktuaris untuk hitung PSAK 24-compliant liability
- Update jurnal akrual

**Y.5 Konfirmasi Saldo (Confirmation Letters)**
- Kirim konfirmasi ke major customers (top 10 piutang)
- Kirim konfirmasi ke major suppliers (top 10 utang)
- Kirim konfirmasi ke bank (saldo per akhir tahun)
- Hasil discrepancy → adjustment

**Y.6 SPT Tahunan PPh Badan (Form 1771) — Lampiran**
- **Lampiran I**: Rekonsiliasi Fiskal
- **Lampiran II**: Daftar Penghapusan Piutang
- **Lampiran III**: Daftar Penyusutan & Amortisasi (komersial vs fiskal)
- **Lampiran IV**: Penghasilan dari Luar Negeri & PPh 24
- **Lampiran V**: Daftar Pemegang Saham (≥25%) & Pengurus
- **Lampiran VI**: Daftar Penyertaan pada Perusahaan Afiliasi
- **Lampiran khusus** (Coretax 2025+): TP Doc untuk transaksi afiliasi

**Y.7 Sign-off & Filing**
- Direksi sign-off SPT Tahunan
- Lapor sebelum deadline (30 April Badan / 31 Maret OP)
- Setor PPh 29 (kurang bayar) sebelum lapor

---

## E. Common Pitfalls Closing

1. **Pakai tarif susut SAK ETAP padahal sudah SAK EP** — beberapa kategori aset boleh metode lain
2. **Lupa hitung pajak tangguhan** dari koreksi fiskal → lap keuangan tidak SAK EP-compliant
3. **Akrual beban tidak konsisten** (kadang akrual, kadang cash) → distorsi laba bulanan
4. **Stock opname tidak proper** → adjusting entry di akhir tahun besar → red flag audit
5. **Reklasifikasi utang LT tidak dilakukan** → current ratio neraca menyesatkan
6. **Bank rekon dengan saldo "approximate"** — INGAT: 0 toleransi, harus exact
7. **Sign-off SPT tanpa rekonsiliasi fiskal proper** → SP2DK pasti datang
8. **CALK kosong / minimal** → pelanggaran SAK EP, audit catch
