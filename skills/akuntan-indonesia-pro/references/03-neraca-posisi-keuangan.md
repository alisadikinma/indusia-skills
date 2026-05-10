# Reference 03 — Neraca / Laporan Posisi Keuangan

## Persamaan Dasar
```
ASET = LIABILITAS + EKUITAS
```

Neraca disajikan komparatif **2 periode** (tahun berjalan + tahun sebelumnya) sesuai SAK EP.

## Format Standard (Berdasarkan Likuiditas — SAK EP)

```
PT [NAMA]
LAPORAN POSISI KEUANGAN
Per 31 Desember 2025
────────────────────────────────────────────────────────────────────────────────

ASET
  ASET LANCAR
    Kas dan Setara Kas                                      Rp        X
    Investasi Jangka Pendek                                 Rp        X
    Piutang Usaha (neto setelah CKP)                        Rp        X
      Piutang Usaha Bruto              Rp X
      (Cadangan Kerugian Piutang)     (Rp X)
    Piutang Lain-lain                                       Rp        X
    Persediaan                                              Rp        X
      Bahan Baku                       Rp X
      Barang Dalam Proses (WIP)        Rp X
      Barang Jadi                      Rp X
    Pajak Dibayar Dimuka                                    Rp        X
    Beban Dibayar Dimuka                                    Rp        X
    ────────────────────────────────────────────────────────────────────────
    TOTAL ASET LANCAR                                       Rp        X

  ASET TIDAK LANCAR
    Investasi Jangka Panjang                                Rp        X
    Aset Tetap (neto)                                       Rp        X
      Tanah                            Rp X     (tidak disusutkan)
      Bangunan                         Rp X
      Mesin & Peralatan                Rp X
      Kendaraan                        Rp X
      Inventaris Kantor                Rp X
      (Akumulasi Penyusutan)          (Rp X)
      (Akumulasi Penurunan Nilai)     (Rp X)   ← SAK EP impairment
    Aset Tidak Berwujud (neto)                              Rp        X
      Hak paten / merk / lisensi       Rp X
      Goodwill                         Rp X
      (Akumulasi Amortisasi)          (Rp X)
    Aset Pajak Tangguhan                                    Rp        X
    Aset Lain-lain                                          Rp        X
    ────────────────────────────────────────────────────────────────────────
    TOTAL ASET TIDAK LANCAR                                 Rp        X
  ════════════════════════════════════════════════════════════════════════
  TOTAL ASET                                                Rp        X
  ════════════════════════════════════════════════════════════════════════

LIABILITAS DAN EKUITAS
  LIABILITAS JANGKA PENDEK
    Utang Usaha                                             Rp        X
    Utang Bank Jangka Pendek                                Rp        X
    Bagian Lancar Utang Jangka Panjang                      Rp        X
    Utang Pajak                                             Rp        X
      PPh 21 terutang                  Rp X
      PPh 23 terutang                  Rp X
      PPh 25 terutang                  Rp X
      PPN terutang (output - input)    Rp X
      PPh Badan terutang (PPh 29)      Rp X
    Utang Gaji & Tunjangan                                  Rp        X
    Beban yang Masih Harus Dibayar                          Rp        X
    Pendapatan Diterima Dimuka                              Rp        X
    Utang Lancar Lain-lain                                  Rp        X
    ────────────────────────────────────────────────────────────────────────
    TOTAL LIABILITAS JANGKA PENDEK                          Rp        X

  LIABILITAS JANGKA PANJANG
    Utang Bank Jangka Panjang (>1 thn)                      Rp        X
    Liabilitas Sewa (PSAK 73)                               Rp        X
    Utang Obligasi                                          Rp        X
    Liabilitas Imbalan Pascakerja (SAK EP wajib!)           Rp        X
    Liabilitas Pajak Tangguhan                              Rp        X
    Utang Pemegang Saham (PV diskontoed)                    Rp        X
    ────────────────────────────────────────────────────────────────────────
    TOTAL LIABILITAS JANGKA PANJANG                         Rp        X
  ────────────────────────────────────────────────────────────────────────
  TOTAL LIABILITAS                                          Rp        X

  EKUITAS
    Modal Disetor                                           Rp        X
      (Modal Dasar Rp X, Ditempatkan & Disetor Rp X)
    Tambahan Modal Disetor (Agio Saham)                     Rp        X
    Saldo Laba Ditahan
      Telah Ditentukan Penggunaannya (Cadangan)             Rp        X
      Belum Ditentukan Penggunaannya                        Rp        X
    Penghasilan Komprehensif Lain (OCI)                     Rp        X
    ────────────────────────────────────────────────────────────────────────
    TOTAL EKUITAS                                           Rp        X
  ════════════════════════════════════════════════════════════════════════
  TOTAL LIABILITAS DAN EKUITAS                              Rp        X
  ════════════════════════════════════════════════════════════════════════
```

**INVARIAN MUTLAK:** TOTAL ASET = TOTAL LIABILITAS + EKUITAS. Kalau tidak balance → ada error posting/jurnal yang harus dicari sampai ketemu. **Tidak ada toleransi 1 sen pun**.

---

## Klasifikasi Aset Lancar vs Tidak Lancar

**Aset Lancar** = diharapkan bisa direalisasi/dipakai dalam **satu siklus operasi normal** ATAU dalam **12 bulan** setelah tanggal neraca, mana yang lebih panjang.

**Aset Tidak Lancar** = sisanya.

Urut dari **paling likuid** ke kurang likuid (di neraca format Indonesia).

---

## Klasifikasi Liabilitas Jangka Pendek vs Panjang

**Liabilitas Jangka Pendek** = diperkirakan diselesaikan dalam **siklus operasi normal** ATAU **jatuh tempo dalam 12 bulan**.

**Liabilitas Jangka Panjang** = sisanya.

**Catatan:** Bagian utang jangka panjang yang akan jatuh tempo dalam 12 bulan ke depan (current portion of long-term debt) → reklasifikasi ke jangka pendek.

---

## Akun Neraca Standard (Chart of Accounts)

### ASET — Akun 1xxx
**Kas & Bank (11xx):**
- 1110 Kas Besar
- 1120 Kas Kecil (petty cash)
- 1130 Bank [Nama Bank A]
- 1140 Bank [Nama Bank B]
- 1150 Setara Kas (deposito ≤3 bulan)

**Investasi Jangka Pendek (12xx):**
- 1210 Deposito Berjangka (3-12 bulan)
- 1220 Surat Berharga

**Piutang (13xx):**
- 1310 Piutang Usaha
- 1315 (Cadangan Kerugian Piutang Usaha)
- 1320 Piutang Karyawan / Cash Advance
- 1330 Piutang Pemegang Saham
- 1340 Piutang Lain-lain

**Persediaan (14xx):**
- 1410 Persediaan Bahan Baku
- 1420 Persediaan Barang Dalam Proses (WIP)
- 1430 Persediaan Barang Jadi
- 1440 Persediaan Bahan Pembantu / Spare Part

**Pembayaran Dimuka (15xx):**
- 1510 Pajak Dibayar Dimuka — PPh 22, 23, 25 (kredit pajak)
- 1520 PPN Masukan
- 1530 Sewa Dibayar Dimuka
- 1540 Asuransi Dibayar Dimuka

**Aset Tetap (16xx):**
- 1610 Tanah (no depreciation)
- 1620 Bangunan
- 1625 (Akumulasi Penyusutan Bangunan)
- 1630 Mesin & Peralatan Pabrik
- 1635 (Akumulasi Penyusutan Mesin)
- 1640 Kendaraan
- 1645 (Akumulasi Penyusutan Kendaraan)
- 1650 Inventaris Kantor
- 1655 (Akumulasi Penyusutan Inventaris)
- 1690 (Akumulasi Penurunan Nilai Aset Tetap) — SAK EP

**Aset Tidak Berwujud (17xx):**
- 1710 Hak Paten
- 1720 Merk Dagang
- 1730 Lisensi Software
- 1740 Goodwill
- 1790 (Akumulasi Amortisasi)

**Aset Lainnya (18xx):**
- 1810 Aset Pajak Tangguhan
- 1820 Jaminan / Deposit
- 1830 Aset Lain

### LIABILITAS — Akun 2xxx
**Liabilitas Jangka Pendek (21xx):**
- 2110 Utang Usaha
- 2120 Utang Bank Jangka Pendek
- 2125 Bagian Lancar Utang Jangka Panjang
- 2130 Utang Pajak
  - 2131 Utang PPh 21
  - 2132 Utang PPh 22
  - 2133 Utang PPh 23
  - 2134 Utang PPh 25
  - 2135 Utang PPh 26
  - 2136 Utang PPN Keluaran
  - 2137 Utang PPh 4(2) Final
  - 2138 Utang PPh 29 (PPh Badan kurang bayar)
- 2140 Utang Gaji
- 2145 Utang BPJS
- 2150 Beban yang Masih Harus Dibayar
- 2160 Pendapatan Diterima Dimuka
- 2190 Utang Lancar Lain-lain

**Liabilitas Jangka Panjang (22xx):**
- 2210 Utang Bank Jangka Panjang
- 2220 Utang Obligasi
- 2230 Liabilitas Sewa (PSAK 73)
- 2240 Liabilitas Imbalan Pascakerja
- 2250 Liabilitas Pajak Tangguhan
- 2260 Utang Pemegang Saham (Long-term)

### EKUITAS — Akun 3xxx
- 3110 Modal Saham (Modal Disetor)
- 3120 Tambahan Modal Disetor (Agio)
- 3130 Cadangan Umum (Laba Ditahan dialokasi)
- 3140 Saldo Laba Belum Ditentukan
- 3150 Penghasilan Komprehensif Lain (OCI)
- 3160 (Saham Treasuri)

---

## Rasio Keuangan dari Neraca + P&L

### Likuiditas
- **Current Ratio** = Aset Lancar / Liabilitas Jangka Pendek
  - Sehat: 1.5–3.0
  - <1.0 = bahaya likuiditas
- **Quick Ratio (Acid Test)** = (Aset Lancar − Persediaan) / Liabilitas Jangka Pendek
  - Sehat: ≥1.0
- **Cash Ratio** = (Kas + Setara Kas) / Liabilitas Jangka Pendek
  - Sehat: 0.2–0.5

### Solvabilitas / Leverage
- **Debt to Asset Ratio** = Total Liabilitas / Total Aset
  - Konservatif: <50%
- **Debt to Equity Ratio (DER)** = Total Liabilitas / Total Ekuitas
  - Konservatif: <1.0; Manufaktur padat modal: bisa 1.0-2.0; >3.0 = highly leveraged
- **Interest Coverage** = EBIT / Beban Bunga
  - Sehat: >3.0

### Aktivitas
- **Inventory Turnover** = HPP / Rata-rata Persediaan
  - Trading FMCG: 8-12x/thn; manufaktur: 4-8x/thn
- **Days Inventory Outstanding (DIO)** = 365 / Inventory Turnover
- **Days Sales Outstanding (DSO)** = (Piutang × 365) / Penjualan
  - Trading B2B: 45-90 hari normal; >120 hari = perhatikan ECL
- **Days Payable Outstanding (DPO)** = (Utang Usaha × 365) / Pembelian
- **Cash Conversion Cycle** = DIO + DSO − DPO
  - Lower = better cash position

### Profitabilitas (terkait neraca)
- **ROA** = Laba Bersih / Total Aset
- **ROE** = Laba Bersih / Ekuitas
- **Asset Turnover** = Penjualan / Total Aset

---

## Common Pitfalls

1. **Salah klasifikasi current vs non-current**
   - Utang bank 5 thn yang sisa 6 bulan jatuh tempo → reklasifikasi ke jangka pendek
   - Deposito >12 bulan masuk Investasi Jangka Panjang, BUKAN Setara Kas

2. **Lupa neto piutang dengan CKP**
   - Yang tampil di muka neraca: Piutang Bruto − CKP = Piutang Neto
   - Disclosure breakdown di CALK

3. **Aset tetap tidak akumulasi penyusutan**
   - Salah: pakai harga perolehan saja
   - Benar: harga perolehan − akumulasi penyusutan − akumulasi penurunan nilai = nilai buku

4. **Pajak tangguhan tidak diakui**
   - SAK EP wajib akui temporary differences (ECL, imbalan kerja, beda susut komersial-fiskal)
   - Aset/Liabilitas Pajak Tangguhan harus muncul di neraca

5. **Modal saham tidak match akta notaris**
   - Modal disetor di neraca harus match akta + SK Kemenkumham
   - Kalau ada penambahan modal → cek bukti setor, akta perubahan, SK Kemenkumham

6. **Liabilitas imbalan pascakerja diabaikan**
   - SAK EP **wajib** akui (sebelumnya opsional di SAK ETAP)
   - Untuk perusahaan >100 karyawan → wajib pakai aktuaris
   - <100 karyawan → boleh metode penyederhanaan

7. **Kas kecil vs kas besar tidak dipisah**
   - Akun terpisah untuk audit trail
   - Kas kecil pakai imprest fund system (saldo tetap, replenish berkala)

---

## Tanda Bahaya (Red Flag) Saat Baca Neraca Klien

| Tanda | Indikasi |
|---|---|
| Piutang naik > revenue (DSO memanjang) | Customer macet / ada faktur fiktif |
| Persediaan naik > pembelian (DIO memanjang) | Slow-moving / obsolete stock |
| Kas turun signifikan tapi laba positif | Earnings quality buruk (laba on paper, no cash) |
| Utang afiliasi besar tanpa dokumen | Risiko transfer pricing & disclosure SAK EP |
| Modal disetor tidak match akta | Pelanggaran UU PT — bisa fatal saat IPO/M&A |
| Tidak ada liabilitas imbalan kerja sama sekali | Pasti undisclosed (pelanggaran SAK EP) |
| Aset Lain-lain besar tanpa rincian | Sembunyikan transaksi mencurigakan |
| Goodwill terus naik tanpa impairment | Kemungkinan tidak uji impairment (overstated aset) |
