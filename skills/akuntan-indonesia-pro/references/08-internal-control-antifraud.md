# Reference 08 — Internal Control & Anti-Fraud

## A. Filosofi Dasar — "Trust But Verify, Verify Twice for Cash"

Akuntan profesional tidak bicara fraud sebagai "kemungkinan kecil". Bicara sebagai **inevitability** kalau control lemah. Statistik ACFE: ~5% revenue annually hilang karena occupational fraud, median loss USD 117k per kasus. Di Indonesia, kasus pidana akuntansi rutin terjadi — banyak terkait kasir + akses kas + judol.

**Iron rules:**
1. **Segregation of Duties** — orang yang pegang kas ≠ orang yang catat ≠ orang yang approve
2. **Dual Custody / 4-Eyes Principle** — minimum 2 orang setiap transaksi material
3. **Audit Trail** — setiap transaksi harus traceable ke supporting document
4. **Surprise Audit** — minimum quarterly, tidak dijadwalkan
5. **Job Rotation & Mandatory Vacation** — kasir/bendahara wajib cuti minimum 2 minggu/thn supaya backup bisa detect anomaly

---

## B. Segregation of Duties — Matrix Mandatory

| Fungsi | Wajib Beda Orang? |
|---|---|
| Authorization (approve transaksi) | YES — manajer/direksi |
| Custody (pegang fisik aset/kas) | YES — kasir |
| Recording (catat di buku) | YES — accounting |
| Reconciliation (cek match) | YES — controller / internal audit |

**Kalau tim cuma 2 orang** (UMKM): owner WAJIB jadi 1 dari 4 fungsi (typically Authorization + Reconciliation), staff pegang Custody + Recording. **Owner tidak boleh "pasrahkan semua" ke 1 staff** — ini fatal.

---

## C. Daily / Weekly / Monthly Anti-Fraud Routine

### Daily
- **Cash count fisik** akhir hari, ditandatangani 2 orang (kasir + saksi)
- **Pos kas masuk** wajib ada **bukti penerimaan** (kuitansi nomor seri tertib, bukan loose paper)
- **Setoran ke bank** harus dilakukan **harian** kalau saldo > threshold tertentu (mis. Rp 5jt)
- **Mutasi bank** dicek vs bukti pendukung

### Weekly
- **Spot check** kas kecil (petty cash) tanpa pemberitahuan
- **Cash advance review** (kasbon karyawan) — flag yang > 30 hari
- **Approval flow audit** — sample 10 transaksi, cek apakah authorization chain dipatuhi

### Monthly
- **Bank reconciliation** semua rekening (lihat reference 07)
- **Stock opname** untuk persediaan high-value
- **Vendor payment review** — sample 5-10 pembayaran, cek bukti & legitimacy
- **Payroll review** — cek nama karyawan match dengan database HR (cegah ghost employee)

### Quarterly / Annual
- **Surprise full cash count** di semua cabang
- **Vendor confirmation** — kirim konfirmasi saldo ke top 20 vendor
- **Customer confirmation** — kirim konfirmasi saldo ke top 20 customer
- **Background check** karyawan baru di posisi sensitive

---

## D. Red Flag Indicators — Pattern yang Akuntan Senior Wajib Hapal

### Cash & Bank
| Red Flag | Apa yang Diindikasikan |
|---|---|
| **Vault balance growing** tanpa setor bank | Cash skimming (kasus PT Asta Rp 730jt) |
| **Setoran bank < seharusnya** dari collection report | Manipulasi setoran (kasus PT Cipta Niaga Rp 90jt) |
| **Frequent bank reconciliation discrepancies** | Posting curang / theft + cover-up attempt |
| **Outstanding cheques** > 60 hari, tidak ditindaklanjuti | Cek lupa / cek fiktif untuk vendor fiktif |
| **Cash advance** > 30 hari tidak di-settle | Personal liquidity issue / judol risk |
| **Saldo kas** abnormally high di akhir hari | Kasir nahan untuk pakai sementara |

### Sales / AR
| Red Flag | Apa yang Diindikasikan |
|---|---|
| **Round number journals** akhir bulan | Estimasi fabrikasi / fictitious revenue |
| **Sales spike akhir bulan** tanpa cause yang jelas | Channel stuffing / hit target manipulasi |
| **Faktur penjualan tanpa surat jalan match** | Faktur fiktif / revenue palsu |
| **Customer baru tanpa due diligence** dengan kredit besar | Risiko credit + potensial fraud terkoordinasi |
| **Piutang aging tidak match** dengan pengakuan revenue | Sales tapi tidak collected = mungkin fictitious |
| **Discount/return spike** awal bulan berikutnya | Pembalik fiktif sales akhir bulan sebelumnya |

### Purchase / AP
| Red Flag | Apa yang Diindikasikan |
|---|---|
| **Vendor baru tanpa supporting documentation** (NPWP, SIUP, address verifikasi) | Vendor fiktif dimiliki insider |
| **Vendor address sama dengan alamat karyawan** | Konflik kepentingan / vendor fiktif |
| **Pembayaran ke vendor tanpa PO** | Bypass control |
| **Pembayaran round number** ke vendor non-rutin | Kickback / fraud |
| **Vendor concentrating purchase** ke 1-2 vendor saja | Risiko collusion (kickback dari vendor) |

### Inventory
| Red Flag | Apa yang Diindikasikan |
|---|---|
| **Stock opname selalu defisit** di lokasi yang sama | Theft (mungkin oleh staff/manajer gudang) |
| **Stock loss "broken/expired" >5%** value | Cover-up theft |
| **Inventory turnover anjlok** padahal sales naik | Persediaan fiktif (cooked books) |
| **Manajer gudang menolak surprise audit** | Strong fraud indicator |

### Payroll
| Red Flag | Apa yang Diindikasikan |
|---|---|
| **Ghost employees** — nama tanpa face check | Pencurian payroll |
| **Overtime spike** untuk karyawan tertentu | Overtime palsu / collusion supervisor |
| **Bonus / komisi** tanpa supporting calc | Manipulasi |
| **Pegawai resign tapi masih dapat gaji** | Lupa offboarding atau intentional fraud |

### General / Behavioral
| Red Flag | Apa yang Diindikasikan |
|---|---|
| **Staff tidak pernah ambil cuti** | Tidak mau backup detect anomaly (klasik fraud signal) |
| **Lifestyle staff > pendapatan resmi** | Sumber dana mencurigakan (terutama kasir) |
| **Staff tertekan / butuh uang mendadak** (medical, judol confession) | Pressure + opportunity = fraud risk tinggi |
| **Manager menolak job rotation** | Mau pertahankan akses untuk fraud |
| **Manager terlalu controlling** atas tim akuntansi | Mungkin cover-up |

---

## E. Real Case Studies (dari riset NotebookLM — semua sudah vonis pengadilan)

### Case 1: PT Asta International — Cabang Sinjai (2024)

**Pelaku:** Putri Ayu Lestari Ilham (kasir)
**Modus:** Embezzlement Rp 730.000.000 dari cash collection sales motor + workshop payment selama Oktober-Desember 2024.

**Cara fraud:**
- Pelanggan bayar cash di cabang
- Putri terima dan bukukan (sebagian)
- Sebagian kas tidak dibukukan, juga tidak disetor ke bank
- Vault balance "menumpuk" di brankas kantor

**Detection:**
- Finance Operational Supervisor notice cash-on-hand report **terus naik tanpa setoran bank** selama 3 bulan
- Investigation → confess

**Vonis:** **3 tahun 6 bulan penjara** (Pengadilan Negeri Sinjai No. 26/Pid.b/2025/PN Snj). Putusan menyebut "Penggelapan dalam hubungan kerja yang dilakukan secara berlanjut".

**Penggunaan dana:** Online gambling (judol).

**Lesson:** Vault balance growth tanpa setor adalah **single most reliable red flag** untuk cash skimming. Wajib daily setor bank kalau saldo > threshold.

### Case 2: PT Cipta Niaga Semesta (CNS)

**Pelaku:** Tia Agustina (kasir)
**Modus:** Embezzlement Rp 90.000.000 via manipulasi setoran bank.

**Cara fraud:**
- Audit menunjukkan setoran wajib ~Rp 394 juta
- Tia hanya setor ~Rp 304 juta ke bank
- Selisih Rp 90 juta dibawa pulang

**Penggunaan dana:** Online gambling + personal use.

**Vonis:** **2 tahun penjara**.

**Lesson:** Setoran bank harus di-cross-check dengan **collection report sales / AR receivable**, bukan cuma trust setoran kasir.

### Common Denominator (Both Cases)

1. **Akses langsung ke fisik kas** + **akses langsung ke recording** → no segregation
2. **No regular surprise cash count** by independent party
3. **Online gambling** sebagai trigger pressure (modern Indonesia phenomenon)
4. **Manager tidak audit cash report** secara rutin

---

## F. Internal Control Framework — COSO Lite (Praktis untuk SME)

### 1. Control Environment
- Kode etik tertulis & ditandatangani semua karyawan
- Kebijakan whistle-blower (anonymous channel ke owner)
- Background check karyawan posisi sensitive
- Policy anti-conflict-of-interest

### 2. Risk Assessment
- Identifikasi risiko fraud per departemen tahunan
- Update saat ada perubahan organisasi/sistem
- Risk register: probability × impact → prioritize control

### 3. Control Activities
- **Authorization controls** — approval matrix (Rp 0-5jt staff, 5-50jt manager, >50jt direktur)
- **Physical controls** — vault dengan dual key, inventory di gudang terkunci
- **Segregation of duties** — matrix dipajang
- **Information controls** — system access limited per role
- **Performance reviews** — KPI review bulanan

### 4. Information & Communication
- Reporting line yang jelas
- Whistle-blower channel
- Regular control update training
- Audit findings dikomunikasikan ke board

### 5. Monitoring
- Internal audit (kalau besar) atau financial controller (kalau medium)
- External audit tahunan
- Continuous monitoring via system alerts

---

## G. Specific Controls — Kas, Inventory, Payroll, AR/AP

### Kas (Cash)
1. **Setor bank harian** kalau saldo > Rp 5jt (atau threshold sesuai size)
2. **Petty cash imprest fund system** — saldo tetap (mis. Rp 2jt), replenish dari kuitansi
3. **Voucher system** untuk semua pengeluaran kas (pre-numbered)
4. **Daily cash count sheet** ditandatangani 2 orang
5. **No personal use cash** — pinjaman karyawan via kasbon formal saja

### Inventory
1. **Cycle count** — sample harian, full opname tahunan minimum
2. **Restricted access** ke gudang
3. **Shipping & receiving terpisah** (orang berbeda)
4. **Damaged/obsolete inventory** wajib disposisi formal (approval direktur)
5. **Stock movement** auto-logged di system, manual entry exceptional

### Payroll
1. **HR maintain master list** karyawan dengan foto, NIK, NPWP
2. **Time tracking** elektronik (fingerprint/face recognition)
3. **Dual approval** payroll: HR + Finance
4. **Direct transfer ke rekening karyawan** (no cash payroll)
5. **Quarterly headcount audit** match HR list vs payroll vs face check

### AR (Receivables)
1. **Credit policy** tertulis dengan tier (CTC, COD, NET 30, NET 60)
2. **Credit committee approval** untuk customer baru atau limit increase
3. **Aging report** dibahas mingguan
4. **Collection escalation matrix**: 30 hari follow-up sales, 60 hari escalate manager, 90 hari legal/collection
5. **Dispute resolution** wajib dokumentasi

### AP (Payables)
1. **3-way match**: PO, Receiving Report, Vendor Invoice — semua harus match
2. **Vendor master file** dengan KYC (NPWP, akta, bank account verified)
3. **Payment batch approval** mingguan oleh direktur
4. **Pending payments aging** review weekly
5. **Vendor performance review** quarterly (cek kalau ada anomali)

---

## H. Investigation Procedure (Saat Red Flag Detected)

1. **Don't tip off** — jangan langsung confront pelaku, bisa hilangkan evidence
2. **Document discreetly** — screenshot, copy file, save audit trail
3. **Engage limited team** — direktur + financial controller + HR (kalau perlu)
4. **Preserve evidence** — backup data, lock relevant accounts
5. **Interview** — formal, witness, recorded (kalau policy allow)
6. **Quantify loss** — rupiah loss, periode, supporting docs
7. **Decision matrix:**
   - Loss < Rp 5jt + first offense → warning + recovery
   - Loss Rp 5-50jt → terminate + file police report
   - Loss > Rp 50jt OR repeat → terminate + criminal proceeding + civil claim
8. **External advisor** — kalau besar atau kompleks: hire forensic accountant + legal counsel
9. **Disclosure** — kalau public company / loan covenant trigger: disclose ke regulator/bank

---

## I. Komunikasi Red Flag ke Owner — Format Wajib

**Format: Fact → Impact → Recommendation**

**Contoh script:**

> **Bapak/Ibu [Owner],**
>
> **Fact:** Saya temukan saldo kas brankas kantor cabang Surabaya per 30 November 2025 adalah Rp 84.500.000. Padahal mutasi setor bank cabang tsb terakhir tanggal 11 November dengan saldo akhir bulan sebelumnya Rp 12.000.000. Artinya selama 19 hari **tidak ada setoran bank** padahal collection harian rata-rata Rp 8jt = ~Rp 152jt yang seharusnya sudah disetor.
>
> **Impact:** Pattern ini identik dengan kasus PT Asta International (Rp 730jt, vonis 3.5 tahun penjara untuk kasir) dan PT Cipta Niaga Semesta (Rp 90jt, vonis 2 tahun). Kalau ini fraud aktif, exposure kita bisa sampai Rp 152jt+ saat ini, dan bisa naik tiap minggu.
>
> **Recommendation:**
> 1. **Hari ini juga**: surprise cash count di cabang Surabaya oleh tim independen (bukan kasir cabang & bukan supervisor cabang)
> 2. **Hari ini juga**: instruksi setor bank semua kas brankas, screenshot bukti
> 3. **Minggu depan**: review CCTV brankas + log akses brankas (siapa & kapan)
> 4. **2 minggu lagi**: implementasikan policy setor bank harian (max H+1) untuk semua cabang, dengan auto-alert ke saya kalau ada saldo > Rp 10jt overnight
> 5. **Kalau ditemukan fraud**: jangan tip-off pelaku; engage HR + legal counsel sebelum interview
>
> Saya siap dampingi proses investigasi. Mohon arahan jadwal cash count hari ini.

**Catatan:** Jangan pakai bahasa accusatory ("saya curiga si A nyolong"). Pakai data, pattern, precedent kasus pidana. Owner bisa decide tindakan, tapi akuntan profesional **wajib report** — kalau diam = kena conduct breach + bisa kena pidana ikut.
