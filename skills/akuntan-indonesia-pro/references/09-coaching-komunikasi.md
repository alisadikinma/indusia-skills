# Reference 09 — Coaching Junior & Komunikasi Profesional

## A. Top 20 Kesalahan Junior Accountant + Jurnal Pembetulan

### 1. Salah Klasifikasi Capex vs Opex

**Kasus:** Renovasi kantor Rp 85 juta dicatat sebagai "Beban Pemeliharaan" → langsung di-charge ke P&L.

**Kenapa salah:** Renovasi yang **memperpanjang masa manfaat** atau **menambah kapasitas/nilai** harus DIKAPITALISASI sebagai aset tetap, bukan opex.

**Test untuk capitalize:**
- Apakah memperpanjang useful life? (≥1 thn tambahan)
- Apakah meningkatkan kapasitas/output?
- Apakah meningkatkan kualitas materially?
- Threshold materialitas (perusahaan biasanya set Rp 5-10jt)

**Jurnal salah (yang dipakai junior):**
```
Db. Beban Pemeliharaan                Rp 85.000.000
    Kr. Bank                              Rp 85.000.000
```

**Jurnal pembetulan:**
```
Db. Bangunan / Renovasi Aset Tetap    Rp 85.000.000
    Kr. Beban Pemeliharaan                Rp 85.000.000
```

Lalu setup penyusutan baru (misalnya 8 thn = Rp 10.625.000/thn):
```
Db. Beban Penyusutan                   Rp 10.625.000
    Kr. Akumulasi Penyusutan Bangunan      Rp 10.625.000
```

**Konsekuensi pajak kalau tidak dikoreksi:**
- Laba komersial understated Rp 85jt
- PPh Badan kurang bayar Rp 18.7jt (22%)
- Kalau ketahuan audit: bayar pokok + bunga 2.2%/bulan + kenaikan 50%

---

### 2. Lupa Koreksi Fiskal

**Kasus:** Akuntan input ECL Rp 45jt di P&L sebagai "Beban Kerugian Piutang", lalu kurangi laba untuk hitung PPh.

**Kenapa salah:** ECL bukan beban yang deductible per UU PPh Pasal 6(1)h. Hanya piutang yang **benar-benar tidak tertagih dengan kriteria spesifik** (debitur bangkrut, putusan pengadilan, dll) yang deductible.

**Pembetulan:** Tambahkan **koreksi positif Rp 45jt** di Lampiran I SPT 1771 → PKP naik Rp 45jt → PPh tambahan Rp 9.9jt.

Lihat reference 07 bagian Rekonsiliasi Fiskal untuk daftar lengkap koreksi.

---

### 3. Tarif PPh Salah karena Lupa Cek NPWP Vendor

**Kasus:** Bayar jasa konsultasi Rp 50jt, junior potong PPh 23 normal 2% = Rp 1jt. Setelah audit, ternyata konsultan **tidak punya NPWP**.

**Kenapa salah:** Pasal 23(1a) UU PPh: tanpa NPWP, tarif **DOUBLE** (jadi 4%). Seharusnya potong Rp 2jt.

**Implikasi:** Selisih Rp 1jt → kekurangan setor → company kena beban tambahan + bunga.

**Workflow benar:**
1. Sebelum bayar, validasi NPWP vendor di portal e-Bupot DJP
2. Kalau tidak ada NPWP → tarif double, dan vendor tidak bisa klaim kredit
3. Selalu minta vendor sediakan NPWP/NIK 16 digit

---

### 4. Double Posting

**Kasus:** Invoice yang sama diposting 2x karena copy fisik diberikan ke 2 staff berbeda.

**Pembetulan:**
```
Db. Utang Usaha (atau Bank)            Rp X
    Kr. Beban (akun yang double-posted)    Rp X
```

**Pencegahan:**
- Stamp "POSTED [tgl] [initial]" pada bukti fisik begitu posted
- Numbering invoice/voucher tertib (no-skip, no-duplicate)
- Sistem yang reject duplicate reference number

---

### 5. Beban Entertainment Tanpa Daftar Nominatif

**Kasus:** Beban entertainment Rp 30jt (jamuan klien) charge full ke beban.

**Kenapa salah:** PMK 02/2010 + Pasal 6(1) UU PPh: entertainment hanya **50% deductible** kalau ada **daftar nominatif** lengkap (nama tamu, jabatan, perusahaan, lokasi, tujuan, tanggal).

**Tanpa daftar nominatif:** **0% deductible** → koreksi positif penuh Rp 30jt.

**Best practice:**
- Setiap voucher entertainment lampirkan daftar nominatif
- Total kumulatif tahunan reasonable (max 0.5-1% revenue)

---

### 6. Persediaan Akhir Tidak Adjust Stock Opname

**Kasus:** Stock card menunjukkan Rp 2.1M, tapi opname fisik hanya Rp 1.95M. Junior tidak buat adjustment, lapor neraca pakai Rp 2.1M.

**Kenapa salah:** Persediaan overstated Rp 150jt → HPP understated → laba overstated → PPh overstated. Selain itu, audit catch = qualified opinion.

**Jurnal pembetulan:**
```
Db. HPP (atau Beban Kerugian Persediaan)  Rp 150.000.000
    Kr. Persediaan                            Rp 150.000.000
```

---

### 7. Salah Hitung HPP Manufaktur (Lupa WIP Roll-Forward)

**Kasus:** Junior pakai formula trading (SA + Pembelian − SAk = HPP) untuk perusahaan manufaktur.

**Pembetulan:** Pakai full skema 3-tier (Bahan Baku → WIP → Barang Jadi). Lihat reference 04.

---

### 8. Beda Penyusutan Komersial vs Fiskal Tidak Diakui

**Kasus:** Komersial pakai garis lurus 8 thn (12.5%/thn), fiskal aturan PMK 96/2009 untuk kategori sama 4 thn (25%/thn).

**Pembetulan:** Akui **temporary difference** dan jurnal **deferred tax**.

```
Misal harga aset Rp 100jt:
- Susut komersial thn 1: Rp 12.5jt
- Susut fiskal thn 1: Rp 25jt
- Beda: Rp 12.5jt (fiskal lebih besar) → koreksi NEGATIF SPT 1771

Liabilitas Pajak Tangguhan: 12.5jt × 22% = Rp 2.75jt
(karena nanti saat fiskal sudah habis susut, komersial masih ada beban → laba fiskal > komersial → bayar pajak)

Db. Beban Pajak Tangguhan (P/L)        Rp 2.750.000
    Kr. Liabilitas Pajak Tangguhan         Rp 2.750.000
```

---

### 9. Pendapatan Diakui Penuh Padahal Performance Obligation Belum Selesai

**Kasus:** PT Aplikasi terima Rp 240jt kontrak tahunan SaaS, langsung diakui sebagai revenue full di Januari.

**Kenapa salah:** SAK EP 5-step model (sama dengan PSAK 72): revenue diakui **seiring** performance obligation terpenuhi. SaaS = service over time → revenue diakui pro-rata Rp 20jt/bulan.

**Pembetulan:**
```
Saat terima kas Januari (yg salah dicatat full revenue):
Db. Pendapatan                         Rp 220.000.000   (reverse 11 bulan yang belum diakui)
    Kr. Pendapatan Diterima Dimuka        Rp 220.000.000

Setiap bulan Feb-Des:
Db. Pendapatan Diterima Dimuka         Rp 20.000.000
    Kr. Pendapatan                        Rp 20.000.000
```

---

### 10. Kasbon Karyawan Dicatat Sebagai Beban

**Kasus:** Karyawan ambil kasbon Rp 5jt untuk biaya operasional, junior catat:
```
Db. Beban Operasional               Rp 5.000.000
    Kr. Kas                            Rp 5.000.000
```

**Kenapa salah:** Kasbon = **piutang karyawan** sampai ada bukti penggunaan / settlement. Jangan langsung ke beban.

**Pembetulan:**
```
Db. Piutang Karyawan / Cash Advance    Rp 5.000.000
    Kr. Kas                                Rp 5.000.000
```

Saat karyawan settle dengan bukti:
```
Db. Beban Operasional (sesuai bukti)   Rp 4.700.000
Db. Kas (sisa dikembalikan)            Rp   300.000
    Kr. Piutang Karyawan                   Rp 5.000.000
```

---

### 11. PPN Masukan Diklaim untuk Item Non-Bisnis / Non-Deductible

**Kasus:** Klaim PPN Masukan atas pembelian sedan/jeep (mobil dinas direksi).

**Kenapa salah:** PMK 02/2010: 50% PPN Masukan untuk sedan/jeep yang dipakai pemegang saham/direksi **tidak dapat dikreditkan**.

**Pembetulan:** Reverse PPN Masukan yang tidak boleh, tambah ke harga perolehan aset.

---

### 12. Faktur Pajak Tidak Match e-Faktur Portal

**Kasus:** Vendor kirim faktur fisik dengan nomor seri X, tapi di portal Coretax tidak ditemukan.

**Konsekuensi:** Saat audit, kredit PPN ditolak. Bisa jadi **vendor tidak setor PPN** (vendor fraud).

**Best practice:** Setiap masuk faktur masukan, **validasi langsung di portal Coretax** sebelum kreditkan. Kalau tidak match → tahan pembayaran ke vendor sampai clear.

---

### 13. Setor PPh 25 Berdasarkan Estimasi Bulanan, Bukan Aturan

**Kasus:** Junior hitung PPh 25 = laba bulan berjalan × 22%. SALAH.

**Aturan benar:** PPh 25 = (PPh terutang **tahun sebelumnya** − kredit pajak tahun sebelumnya) / 12. Angka yang **tetap** sepanjang tahun.

**Pengecualian:** Pertama kali berdiri → PPh 25 = 0. Atau perubahan signifikan business → minta pembetulan ke DJP.

---

### 14. Lupa Sanksi Bunga di Hitungan SPT Pembetulan

**Kasus:** Pembetulan SPT yang menyebabkan kurang bayar tambahan, junior cuma setor pokok tanpa bunga.

**Aturan:** Pembetulan kurang bayar wajib disertai **bunga 2.2%/bulan** (UU HPP) sejak deadline original sampai tanggal setor.

---

### 15. Modal Disetor di Neraca Tidak Match Akta

**Kasus:** Akta perubahan terakhir modal disetor Rp 5M, di neraca masih Rp 2M.

**Kenapa salah:** Pelanggaran UU PT — neraca harus match akta + SK Kemenkumham. Bisa fatal saat M&A, IPO, due diligence.

**Pembetulan:** Pastikan:
- Akta perubahan + SK Kemenkumham terbaru
- Bukti setor modal (transfer ke rekening perusahaan)
- Update jurnal:
```
Db. Bank                               Rp 3.000.000.000
    Kr. Modal Disetor                      Rp 3.000.000.000
```

---

### 16. Aset Tetap Dijual Tanpa Eliminasi Akumulasi Penyusutan

**Kasus:** Jual mobil yang sudah disusutkan 4 tahun. Junior cuma:
```
Db. Bank                               Rp 80.000.000
    Kr. Kendaraan                          Rp 80.000.000
```

**Pembetulan benar:**
- Harga perolehan: Rp 200jt
- Akumulasi penyusutan 4 thn (50%): Rp 100jt
- Nilai buku: Rp 100jt
- Harga jual: Rp 80jt → rugi Rp 20jt

```
Db. Bank                               Rp  80.000.000
Db. Akumulasi Penyusutan Kendaraan     Rp 100.000.000
Db. Rugi Penjualan Aset Tetap          Rp  20.000.000
    Kr. Kendaraan                          Rp 200.000.000
```

---

### 17. Piutang Pemegang Saham Tidak Di-PV

**Kasus:** Pinjaman pemegang saham Rp 3M, no interest, 3 thn. Dicatat di nilai nominal Rp 3M.

**SAK EP wajib:** Hitung PV pakai market rate (≈8%) → PV = Rp 2.38M. Lihat reference 01.

---

### 18. Lupa Pendapatan Bunga Final Tetap Disclose

**Kasus:** Pendapatan jasa giro Rp 8jt sudah dipotong PPh Final 20% (Rp 1.6jt) oleh bank. Junior tidak masukkan ke pembukuan karena "sudah final".

**Kenapa salah:** Tetap harus dicatat di P&L (full Rp 8jt sebagai pendapatan + akui Rp 1.6jt sebagai pajak final), lalu di rekonsiliasi fiskal **koreksi negatif** Rp 8jt karena sudah final.

```
Db. Bank                               Rp 6.400.000
Db. Beban PPh Final 4(2)               Rp 1.600.000
    Kr. Pendapatan Jasa Giro               Rp 8.000.000
```

---

### 19. Faktur Pajak Out of Sequence (Lompat Nomor)

**Kasus:** Faktur seri 010-001-25-12345678 lalu lompat ke 010-001-25-12345680 (tanpa 79).

**Konsekuensi:** Coretax akan **flag**. DJP akan tanya ke mana faktur 79 — kalau tidak bisa dijelaskan = potensi **faktur fiktif** untuk pihak ketiga.

**Best practice:** Sequence wajib continuous. Kalau ada cancel, dokumentasikan dengan **Berita Acara Pembatalan** + simpan faktur batal di file untuk audit.

---

### 20. Tidak Buat CALK / Buat CALK Minim

**Kasus:** Lap keuangan SAK EP cuma punya 4 statement (Neraca, P&L, Arus Kas, Perubahan Ekuitas) tanpa CALK substantial.

**Kenapa salah:** SAK EP **wajib CALK detail** disclose:
- Kebijakan akuntansi material
- Estimasi & judgment kunci
- Analisis maturitas utang
- Pihak berelasi & transaksi mereka
- Komitmen & kontijensi
- Subsequent events

Tanpa CALK → audit qualified atau adverse opinion.

---

## B. Komunikasi Profesional ke Owner / Direksi

### Format Komunikasi: Fact → Impact → Recommendation

Selalu pakai struktur ini untuk red flag, finding, atau usulan strategis.

### Script Library

#### Script 1: Tolak Tax Evasion Owner

**Situasi:** Owner mau "atur" pendapatan supaya PPh kurang.

> "Pak, saya paham mau tekan beban pajak — itu objektif yang sah. Tapi yang Bapak usulkan (tidak laporkan revenue tunai / pakai faktur fiktif) ini masuk **tax evasion** — UU KUP Pasal 39: pidana 6 tahun penjara + denda 4x kurang bayar.
>
> Untuk skala kasus, lihat PT Cipta Niaga Semesta — kasir gelapkan Rp 90 juta, vonis 2 tahun penjara. Itu cuma karyawan, dan amount-nya cuma Rp 90 juta. Kalau pemilik PT yang dipidana evasion, sentencing biasanya lebih berat.
>
> Yang bisa saya tawarkan adalah **tax planning legal**:
> 1. Pakai PPh Final UMKM 0.5% kalau eligible (kita perlu cek omzet < 4.8M & batas waktu PT 4 tahun)
> 2. Optimasi metode penyusutan — pakai PMK 96/2009 yang aturannya lebih cepat dari komersial → koreksi negatif → PKP turun
> 3. Maksimalkan beban deductible legal: BPJS, training, tunjangan kesehatan, gaji bagi keluarga yang memang kerja
>
> Estimasi penghematan dari opsi legal: ~Rp X juta/tahun. Lebih kecil dari yang Bapak usul, tapi 0% risiko penjara. Saya tidak bisa sign-off pendekatan tax evasion — ini bagian dari kode etik akuntan + tanggung jawab pidana saya juga."

#### Script 2: Sampaikan Red Flag Fraud Indikatif

**Situasi:** Pattern vault balance growing tanpa setor (klasik PT Asta).

> "Pak, ada pattern di cabang Surabaya yang harus saya report sekarang juga.
>
> **Fact:** Saldo brankas cabang Surabaya per 30 November Rp 84.5 juta. Mutasi setor bank cabang terakhir 11 November dengan saldo akhir Oktober Rp 12 juta. 19 hari tanpa setoran padahal collection harian rata-rata Rp 8 juta = ~Rp 152 juta yang seharusnya sudah di bank.
>
> **Impact:** Pattern ini identik dengan kasus PT Asta International (Sinjai 2024). Kasir gelapkan Rp 730 juta dengan modus sama → vonis 3.5 tahun penjara. Kalau ini fraud aktif di cabang kita, exposure Rp 152 juta+, dan akan terus naik kalau tidak action sekarang.
>
> **Recommendation:**
> 1. Hari ini juga: surprise cash count cabang oleh tim independen (bukan kasir cabang & bukan supervisor cabang)
> 2. Hari ini juga: instruksi setor seluruh kas brankas, screenshot bukti setor
> 3. Minggu ini: review CCTV brankas + log akses
> 4. 2 minggu lagi: implementasi policy setor harian + auto-alert kalau saldo > Rp 10jt overnight
> 5. Kalau ditemukan fraud: jangan tip-off pelaku; engage HR + legal counsel sebelum interview
>
> Saya siap dampingi proses. Mohon arahan jadwal cash count hari ini — paling lambat besok pagi."

#### Script 3: Argue Owner yang Tidak Paham SAK EP

**Situasi:** Owner berkata "kenapa harus repot ECL? selama ini SAK ETAP fine kok."

> "Pak, SAK ETAP **sudah dicabut** per 1 Januari 2025. Mandatory transisi ke SAK EP. Bukan opsional.
>
> Konsekuensi tidak adopt SAK EP:
> 1. **Audit qualified/adverse opinion** kalau perusahaan diaudit (bank loan covenant biasanya butuh unqualified)
> 2. **Disclosure problem** saat M&A / due diligence — buyer akan diskon valuasi karena lap keuangan tidak comply
> 3. **DJP saat audit** akan tetap pakai pendekatan substansi → tidak ada exception untuk SAK ETAP
>
> ECL terlihat ribet, tapi sebenarnya simple:
> 1. Setiap akhir bulan, kelompokkan piutang by aging (0-30, 31-60, 61-90, dst)
> 2. Apply persentase loss historis
> 3. Buat 1 jurnal akrual cadangan
>
> Manfaatnya:
> - Lap keuangan **lebih konservatif** (more honest tentang risiko piutang)
> - Bank kasih kredit lebih mudah (lap keuangan trustworthy)
> - **Tidak ada surprise** kalau customer tiba-tiba bangkrut — kerugian sudah ter-akrual bertahap
>
> Saya bisa setup template ECL + train tim seminggu. Bagaimana, Pak?"

#### Script 4: Coaching Junior Setelah Catch Error

**Situasi:** Anda baru catch junior salah klasifikasi capex Rp 85jt sebagai opex.

> "[Nama], saya temukan satu hal yang perlu kita bahas sebentar. Renovasi kantor Rp 85 juta yang kemarin kamu posting ke 'Beban Pemeliharaan' — ini sebenarnya harus dikapitalisasi, bukan opex.
>
> Test cepat untuk capitalize: apakah memperpanjang umur ekonomis aset >1 tahun, atau menambah kapasitas/value materially? Kalau ya → capex. Renovasi kantor (cat baru, perbaiki struktur) extends useful life building → capex.
>
> Konsekuensi kalau tidak dikoreksi:
> - Laba komersial understated Rp 85jt
> - PPh kurang bayar Rp 18.7 juta
> - Kalau audit: pokok + bunga 2.2%/bulan + kenaikan 50%
>
> Pembetulannya:
> [explain jurnal pembetulan]
>
> Ini common error, bukan masalah ability — saya juga pernah salah pas junior, sampai di-catch auditor. Setelah ini, sebelum kamu post item > Rp 5jt yang bukan rutin, ping saya dulu untuk validate klasifikasi. Threshold materialitas kita Rp 5 juta. OK?"

---

## C. Etika Profesi Akuntan Indonesia

### Kode Etik IAI (Ikatan Akuntan Indonesia)

5 prinsip dasar wajib:
1. **Integritas** — jujur & lurus dalam semua hubungan profesional
2. **Objektivitas** — tidak boleh bias / konflik kepentingan
3. **Kompetensi & Kehati-hatian Profesional** — terus update knowledge
4. **Kerahasiaan** — info klien tidak boleh disclose tanpa otorisasi
5. **Perilaku Profesional** — comply dengan UU & jaga reputasi profesi

### Saat Kode Etik Berbenturan dengan Permintaan Owner

**Skala respons:**
1. **Education** — explain why permintaan tidak comply
2. **Alternative** — tawarkan solusi legal
3. **Document** — minta instruksi tertulis kalau owner tetap minta
4. **Escalate** — kalau di firma/group: lapor ke senior partner / komisaris
5. **Resign** — kalau permintaan masuk pidana (evasion, money laundering) dan owner refuse alternative → resign formal + simpan dokumentasi pribadi (untuk pembelaan kalau ada investigasi)

**Iron rule:** Akuntan profesional **tidak akan terlibat** dalam tax evasion, money laundering, atau fraud — bahkan kalau owner perintahkan. Tanggung jawab pidana akuntan **personal**, tidak hilang dengan alasan "saya cuma karyawan".

---

## D. Dokumentasi & Audit Trail

### Aturan Emas

1. **Setiap rupiah ada bukti pendukung** — kuitansi, invoice, surat jalan, bukti transfer
2. **Bukti supportive disimpan minimum 10 tahun** (UU KUP Pasal 28)
3. **Backup digital** wajib (cloud + local)
4. **Pelabelan jelas** per bulan/tahun untuk retrieval cepat
5. **Authorization trail** terlihat di setiap voucher (siapa request, siapa approve, siapa execute)

### Dokumen yang Wajib Disimpan ≥10 Tahun

- Bukti penerimaan kas (kuitansi, invoice penjualan, struk)
- Bukti pengeluaran kas (voucher, kuitansi vendor, invoice supplier)
- Faktur Pajak (PPN keluaran & masukan)
- Bukti potong PPh (Pasal 21, 22, 23, 26, 4(2))
- Rekening koran semua bank
- Buku besar & buku pembantu
- Laporan keuangan & SPT
- Akta + perubahan akta + SK Kemenkumham
- Kontrak dengan vendor & customer (yang material)
- Aktiva tetap: bukti perolehan + serah terima

### Kalau Hilang / Rusak

- Buat **Berita Acara Hilang/Rusak** ditandatangani direksi + saksi
- Lapor polisi kalau hilang karena tindakan kriminal
- Coba rekonstruksi dari pihak ketiga (bank statement, vendor copy, e-Faktur portal)
- Disclose ke auditor & DJP saat pemeriksaan

---

## E. Daily Habits Akuntan Senior 20 Tahun

1. **Mulai pagi**: review cash position semua bank + email overnight
2. **9-11 AM**: sign-off voucher kemarin, review pending approval
3. **11-12**: 1-on-1 dengan staff (rotasi), coach common issues
4. **Setelah lunch**: posting/reconcile hari berjalan
5. **3-5 PM**: deep work (closing, SPT, analisis ad-hoc)
6. **5 PM**: cash count akhir hari, sign-off
7. **Sebelum pulang**: 5-min review besok prioritas + email balasan

**Mindset:**
- "Setiap entry adalah kesaksian legal"
- "Tidak ada toleransi 1 sen"
- "Trust but verify; verify twice for cash"
- "Kalau ragu — escalate, jangan diam"
- "Owner butuh truth, bukan good news"

Akuntan senior **menjaga kredibilitas** sebagai aset utama. Kompromi sekali = lifetime risk.
