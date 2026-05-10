# Reference 01 — PSAK / SAK EP Fundamentals

## TL;DR Standar Akuntansi yang Berlaku di Indonesia (2025)

| Standar | Untuk Siapa | Status 2025 |
|---|---|---|
| **PSAK (full IFRS)** | PT Tbk (publik), perusahaan dengan akuntabilitas publik signifikan | Berlaku |
| **SAK EP (Entitas Privat)** | PT/CV swasta non-publik | **Mandatory mulai 1 Jan 2025** (ganti SAK ETAP) |
| **SAK EMKM** | UMKM mikro & kecil (omzet <Rp 4.8M/thn) | Berlaku, opsional naik ke SAK EP |
| **SAK ETAP** | Entitas tanpa akuntabilitas publik (pre-2025) | **DICABUT 1 Jan 2025** — sudah obsolete |
| **SAK Syariah** | LKS / pembiayaan syariah | Berlaku (paralel) |

**KRITIKAL:** Kalau klien masih bilang "kami pakai SAK ETAP" — beri tahu mereka SAK ETAP **sudah tidak berlaku** per 1 Januari 2025. Wajib transisi ke SAK EP. Early adoption diperbolehkan sejak 1 Januari 2022.

---

## Perbedaan Substantif SAK ETAP → SAK EP

### A. Pengakuan Pendapatan
- **SAK ETAP**: Estimasi umum penyelesaian (sederhana)
- **SAK EP**: **Five-Step Model** (mirip IFRS 15 / PSAK 72):
  1. Identifikasi kontrak dengan pelanggan
  2. Identifikasi kewajiban pelaksanaan (performance obligations)
  3. Tentukan harga transaksi
  4. Alokasikan harga transaksi ke setiap kewajiban pelaksanaan
  5. Akui pendapatan ketika (atau seiring) kewajiban pelaksanaan terpenuhi

**Implikasi praktis:** Untuk kontrak multi-deliverable (mis. paket perangkat keras + jasa instalasi + maintenance 1 tahun), revenue tidak boleh full diakui di awal. Harus dialokasi per komponen. Banyak PT IT, konstruksi, SaaS yang harus restate revenue rekognisinya saat transisi.

### B. Piutang & Cadangan Kerugian (ECL)
- **SAK ETAP**: Penghapusan langsung saat jelas tidak tertagih (write-off method)
- **SAK EP**: **Expected Credit Loss (ECL)** — wajib aging schedule + estimasi kerugian historis

**Aging Schedule Standard:**
| Bucket Umur | % ECL Default (industri trading-jasa) |
|---|---|
| 0–30 hari | 0.5% |
| 31–60 hari | 1.5% |
| 61–90 hari | 5% |
| 91–180 hari | 15% |
| 181–365 hari | 50% |
| >365 hari | 100% (full impairment) |

(Catatan: % ini benchmark — wajib disesuaikan dengan data historis 3-5 tahun perusahaan)

**Jurnal ECL setiap akhir bulan:**
```
Db. Beban Kerugian Penurunan Nilai Piutang     Rp X
    Kr. Cadangan Kerugian Piutang (CKP)            Rp X
```

**Saat real write-off (debitur bangkrut, terbukti tdk tertagih):**
```
Db. Cadangan Kerugian Piutang                  Rp X
    Kr. Piutang Usaha                              Rp X
```

**KRITIKAL untuk pajak:** Beban ECL **NON-DEDUCTIBLE** untuk PPh (Pasal 6(1)h UU PPh hanya bolehkan piutang yang BENAR-BENAR tidak tertagih dengan kriteria spesifik). → Wajib **koreksi positif** di SPT Tahunan PPh Badan.

### C. Imbalan Pasca Kerja (Post-Employment Benefits)
- **SAK ETAP**: Opsional / cash basis
- **SAK EP**: **Wajib** estimasi & pengakuan liabilitas — metode aktuarial atau penyederhanaan

**Formula penyederhanaan SAK EP:**
```
Liabilitas Imbalan Pascakerja = Gaji Bulanan × Tenor (bulan) × PV Factor
```

(Aktuarial: pakai PSAK 24 / IAS 19 metodologi — biasanya butuh aktuaris untuk perusahaan >100 karyawan)

**Implikasi pajak:** Estimasi imbalan **NON-DEDUCTIBLE** sampai **dibayarkan / disetorkan ke dana pensiun** → koreksi positif.

### D. Instrumen Keuangan
- **SAK ETAP**: Nilai nominal / historical cost
- **SAK EP**: **Amortized Cost** menggunakan **Effective Interest Rate (EIR)** untuk utang bank, leasing, obligasi

**Contoh transisi:** Pinjaman bank 5 thn @ flat interest → wajib hitung ulang dengan EIR (effective interest). Selisihnya jadi diskonto/premium yang diamortisasi.

### E. Aset Tetap & Penurunan Nilai
- **SAK ETAP**: Cost - akumulasi penyusutan
- **SAK EP**: + **Wajib uji penurunan nilai (impairment test)** kalau ada indikasi (kerusakan fisik, obsolesi ekonomi, perubahan teknologi)

**Indikasi impairment test wajib:**
- Penurunan nilai pasar signifikan
- Perubahan negatif kondisi pasar / teknologi
- Suku bunga pasar naik tinggi (mempengaruhi recoverable amount)
- Kerugian operasional berkelanjutan terkait aset

**Jurnal impairment loss:**
```
Db. Beban Penurunan Nilai Aset Tetap           Rp X
    Kr. Akumulasi Penurunan Nilai Aset Tetap       Rp X
```

→ Koreksi positif untuk PPh (impairment estimasi tidak diakui pajak).

### F. Investasi pada Entitas Asosiasi
- **SAK ETAP**: Historical cost
- **SAK EP**: **Equity method** (untuk pengaruh signifikan ≥20% kepemilikan) + uji impairment tahunan

### G. Pinjaman Pemegang Saham Tanpa Bunga
- **SAK ETAP**: Cukup catat di nilai nominal
- **SAK EP**: **Wajib hitung Present Value (PV)** pakai market interest rate

**Contoh kasus dari riset (relevan banyak PT keluarga):**
- Pinjaman dari pemegang saham: Rp 3.000.000.000
- Tenor: 3 tahun, tanpa bunga
- Market rate: 8%
- PV = Rp 3M / (1.08)³ = **Rp 2.381.500.000**
- Selisih Rp 618.500.000 → diskonto

**Jurnal transisi (saat awal pengakuan SAK EP):**
```
Db. Laba Ditahan                               Rp 618.500.000
    Kr. Piutang Pemegang Saham (Diskonto)         Rp 618.500.000
```

Kemudian setiap tahun amortisasi diskonto sebagai beban bunga implicit.

---

## Format Laporan Keuangan SAK EP (Wajib Disajikan)

1. **Laporan Posisi Keuangan** (Neraca) — komparatif 2 tahun
2. **Laporan Laba Rugi Komprehensif** (single statement) atau Laporan Laba Rugi + Laporan Penghasilan Komprehensif Lain (two-statement)
3. **Laporan Perubahan Ekuitas**
4. **Laporan Arus Kas** (langsung atau tidak langsung)
5. **Catatan Atas Laporan Keuangan (CALK)** — substantial, ungkap policies + estimasi + risiko

**SAK EP butuh CALK lebih tebal dari SAK ETAP** — harus disclose:
- Kebijakan akuntansi material
- Estimasi & judgment kunci (ECL %, useful life aset, imbalan kerja)
- Analisis maturitas utang
- Kontijensi & komitmen
- Pihak berelasi & transaksi dengan mereka

---

## Persamaan Dasar Akuntansi (Wajib Hapal)

```
ASET = LIABILITAS + EKUITAS

EKUITAS = Modal Disetor + Laba Ditahan + Komponen OCI Lainnya

LABA BERSIH = Pendapatan − Beban (Operasional + Non-Operasional + Pajak)

LABA DITAHAN AKHIR = Laba Ditahan Awal + Laba Bersih − Dividen − Cadangan
```

---

## Jurnal Penyesuaian (Adjusting Entries) Akhir Periode — Wajib

### 1. Penyusutan Aset Tetap
```
Db. Beban Penyusutan                           Rp X
    Kr. Akumulasi Penyusutan                       Rp X
```

### 2. Amortisasi Aset Tidak Berwujud
```
Db. Beban Amortisasi                           Rp X
    Kr. Akumulasi Amortisasi                       Rp X
```

### 3. Pendapatan Diterima Dimuka → Diakui Sebagian
```
Db. Pendapatan Diterima Dimuka                 Rp X
    Kr. Pendapatan                                 Rp X
```

### 4. Beban Dibayar Dimuka → Habis Masa Manfaat Sebagian
```
Db. Beban (Sewa/Asuransi/dll)                  Rp X
    Kr. Beban Dibayar Dimuka                       Rp X
```

### 5. Pendapatan yang Masih Harus Diterima (Accrued Revenue)
```
Db. Piutang / Pendapatan yg Msh Hrs Diterima   Rp X
    Kr. Pendapatan                                 Rp X
```

### 6. Beban yang Masih Harus Dibayar (Accrued Expense)
```
Db. Beban (Listrik/Gaji/Bunga/dll)             Rp X
    Kr. Utang (Listrik/Gaji/Bunga/dll)             Rp X
```

### 7. Cadangan Kerugian Piutang (ECL — SAK EP)
```
Db. Beban Kerugian Penurunan Nilai             Rp X
    Kr. Cadangan Kerugian Piutang                  Rp X
```

### 8. Persediaan (Stock Opname Adjustment)
```
Db. HPP                                        Rp X      (kalau opname < pembukuan = ada loss)
    Kr. Persediaan                                 Rp X
```
ATAU
```
Db. Persediaan                                 Rp X      (kalau opname > pembukuan = ada gain)
    Kr. HPP                                        Rp X
```

### 9. Pajak Tangguhan (Deferred Tax)
```
Db. Aset Pajak Tangguhan                       Rp X
    Kr. Manfaat Pajak Tangguhan (P/L)              Rp X
```
ATAU
```
Db. Beban Pajak Tangguhan (P/L)                Rp X
    Kr. Liabilitas Pajak Tangguhan                 Rp X
```

---

## Jurnal Pembalik (Reversing Entries) — Awal Periode Berikutnya

Untuk efisiensi, beberapa adjusting entries di-balik di awal periode berikutnya:
- Accrued revenue & expense (yang sudah dicatat di akhir periode lalu)
- Pendapatan/beban diterima/dibayar dimuka YANG dicatat sebagai pendapatan/beban penuh saat penerimaan/pembayaran

**TIDAK PERLU dibalik:** Penyusutan, amortisasi, ECL, pajak tangguhan (ini standing balances).

---

## Tantangan Implementasi SAK EP (Berdasarkan Riset)

1. **Literasi akuntansi rendah** — banyak staff perusahaan privat tidak terbiasa dengan ECL, EIR, fair value
2. **Data historis terbatas** — sulit hitung ECL kalau tidak ada record loss histori 3-5 thn
3. **HR competency gap** — kompetensi IFRS-based principles langka
4. **Cost transition** — software upgrade, training staff, konsultan untuk gap analysis

**Rekomendasi praktis untuk klien yang baru transisi:**
1. **Lakukan gap analysis** akun per akun: piutang, kontrak revenue, imbalan kerja, pinjaman
2. **Implementasi ECL model bertahap** — mulai dari aging schedule sederhana, refinement berikutnya
3. **Adopsi multi-step P&L** untuk visibilitas margin
4. **Setup deferred tax tracking** sejak awal — temporary differences SAK EP → fiskal akan banyak

---

## PSAK Spesifik yang Sering Dipakai (Untuk PT Tbk / Full IFRS)

| PSAK | Topic | Padanan IFRS |
|---|---|---|
| PSAK 1 | Penyajian Laporan Keuangan | IAS 1 |
| PSAK 2 | Laporan Arus Kas | IAS 7 |
| PSAK 14 | Persediaan | IAS 2 |
| PSAK 16 | Aset Tetap | IAS 16 |
| PSAK 19 | Aset Tidak Berwujud | IAS 38 |
| PSAK 24 | Imbalan Kerja | IAS 19 |
| PSAK 46 | Pajak Penghasilan (Tangguhan) | IAS 12 |
| PSAK 71 | Instrumen Keuangan | IFRS 9 (ECL) |
| PSAK 72 | Pendapatan dari Kontrak dgn Pelanggan | IFRS 15 (Five-Step Model) |
| PSAK 73 | Sewa | IFRS 16 (lessee on-balance) |

**Catatan:** PT Tbk (publik) wajib full PSAK. PT swasta cukup SAK EP (versi simplified IFRS for SMEs). UMKM cukup SAK EMKM (cash basis OK).
