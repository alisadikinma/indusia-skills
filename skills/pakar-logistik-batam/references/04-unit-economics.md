# Pillar 04 — Unit Economics: Per-Trip P&L, Working Capital Trap, Yield Management

## Pain Story — "Paper Profit, Empty Wallet"

Pak Indra IRN tutup buku November. **Revenue Rp 1.4 milyar, profit on paper Rp 220jt (16% margin)**. Bagus kan? Tapi tanggal 5 Desember dia tidak bisa transfer gaji 35 sopir.

Kenapa? **Receivable outstanding**:
- SITC: invoice November Rp 480jt — payment terms 60 hari → cair pertengahan Januari
- PT Yixin (shipyard Tg Uncang): Rp 320jt — payment 90 hari → cair akhir Februari
- Customer kecil: Rp 180jt — average 45 hari mix

Total cash trapped di receivable: **Rp 980jt**. Sementara cash demand harian: BBM Rp 28jt/hari, gaji+kasbon Rp 18jt/minggu, sewa depo + utilitas Rp 25jt/bulan.

Pak Indra terpaksa: pinjam ke saudara, gadai mobil pribadi, atau worst case — kasbon dari supplier solar. Dia merasa "gagal" padahal bisnisnya untung di atas kertas.

**This is the working capital trap.** Industry-wide pain di Indonesian logistics. Yang membedakan owner survive vs bangkrut: **manajemen cash flow, bukan profit pursuit**.

---

## Per-Trip P&L (Trucking Container)

### Anatomy of 1 Trip

```
Tarif customer (Hamin 2 → Tanjung Uncang, 40ft)        Rp 1,200,000
                                                        ───────────
Variable cost:
  BBM solar (rata 22 km/L, jarak 30km PP, harga Rp 7,500/L)  Rp   340,000
  Tol (rata-rata 2 gerbang × Rp 25k)                          Rp    50,000
  Operator/parking liar (cas preman pos jaga)                 Rp    20,000
  Driver komisi (25% dari (tarif-BBM-tol-misc))               Rp   197,500
                                                        ───────────
                                                        Rp   607,500
Variable contribution                                   Rp   592,500

Allocated cost (per trip):
  Chassis idle/depreciation (Rp 200k/hari × 1 hari avg)        Rp   200,000
  Vehicle depreciation + maintenance (Rp 25jt/bulan / 22 trip)  Rp 1,136,000 →
    actually allocate per km basis: Rp 4,500/km × 30 km =      Rp   135,000
  Overhead admin (Rp 15jt/bulan / 22 trip avg)                Rp    68,000
  Insurance + STNK (alocated)                                  Rp    20,000
                                                        ───────────
                                                        Rp   423,000

NET CONTRIBUTION per trip                               Rp   169,500
                                                        ═══════════
NET MARGIN                                              ~14%
```

**Key insights:**
1. Driver komisi 25% adalah **variable cost** — bukan gaji tetap
2. Allocated cost (chassis idle, vehicle depr, overhead) = "silent killer" — banyak owner lupa hitung
3. Margin healthy trucking Batam = **10-18%** net. Below 8% = risky. Above 20% = lucky atau under-priced cost
4. Tarif Hamin → Tanjung Uncang range Rp 800k-1.4jt tergantung deal & timing

### Cost Yang Sering Dilupakan Owner Kecil

| Cost | Per Bulan (50-unit fleet) | Sering Dilupakan? |
|---|---|---|
| Vehicle depreciation | Rp 80-120jt | YA — owner lihat profit cash, lupa amortize |
| Chassis idle cost | Rp 30-60jt | YA — chassis nganggur tetap "kerja" memakan modal |
| Insurance fleet | Rp 8-15jt | sometimes |
| KIR + STNK + uji emisi | Rp 5-10jt | YA — sporadic |
| Office overhead (admin, listrik, sewa, internet) | Rp 25-50jt | sometimes |
| Penalty Bea Cukai / cas accidental | Rp 5-15jt | YA — eat into margin |
| Driver turnover (recruit + training) | Rp 3-10jt | YA — banyak yang "free" tapi sebenarnya bukan |

---

## Per-Job P&L (Crane)

### Mobile Crane 25T, 1 Day Job di Shipyard

```
Customer pricing:
  Sewa unit 8 jam                          Rp 4,500,000
  Mob (10km dari depo)                     Rp 1,500,000
  Demob                                    Rp 1,500,000
  Operator + 1 helper                      Rp   500,000
                                           ───────────
                                           Rp 8,000,000

Variable cost:
  BBM crane (60L × Rp 7,500)               Rp   450,000
  BBM truck angkut (kalau perlu mob)       Rp   150,000
  Operator gaji (project rate)             Rp   300,000
  Helper                                   Rp   150,000
  Tol + parking                            Rp   100,000
                                           ───────────
                                           Rp 1,150,000

Allocated cost:
  Crane depr (Rp 4.5M / 60 bulan effective / 22 hari)
                                           Rp   341,000
  Insurance (3% × Rp 4.5M / 365 hari)       Rp   370,000
  Cert + maintenance reserve                Rp   200,000
  Overhead                                  Rp   150,000
                                           ───────────
                                           Rp 1,061,000

NET PROFIT per day-job                     Rp 5,789,000
                                           ═══════════
NET MARGIN                                 ~72%
```

**Catatan:** ini optimistic — assume crane utilized 22 hari/bulan. Real average di Batam: 15-18 hari (utilization 60-75%).

Adjusted realistic margin: **40-50% net** for a well-run crane operation.

---

## Working Capital Trap — Mengapa Profit ≠ Cash

### Cash Cycle Indonesian Logistics

```
Deliver service today
        ↓
Bill customer (end of month, +0 day)
        ↓
Customer terima invoice (ada delay 5-15 hari kalau tax invoicing)
        ↓
Customer payment terms: 30 / 60 / 90 hari
        ↓
Cash hits IRN bank account: average 70-95 hari setelah service
```

Sementara cash OUT:
- BBM: harian
- Gaji + kasbon driver: mingguan
- Sewa, listrik: bulanan
- Maintenance: bulanan / sporadic

**Result:** untuk fleet 50 unit dengan revenue Rp 1.4M/bulan, working capital requirement = ~**Rp 2-3 milyar** baru bisnis berjalan smooth tanpa cash crunch.

### Customer Tier vs DSO (Days Sales Outstanding)

| Customer Tier | DSO Real | Risk |
|---|---|---|
| Shipping line MNC (SITC, Maersk) | 60-75 hari | Low (always pays) |
| EPC oil & gas big | 75-90 hari | Medium (paperwork heavy) |
| Shipyard Tanjung Uncang | 75-120 hari | **HIGH** (often delays, but volume tinggi) |
| Manufaktur Batamindo | 30-45 hari | Low (faster pay culture) |
| Customer kecil / personal | 0-30 hari | Variable — better COD |

**Rule of thumb:** customer dengan DSO > 90 hari sebaiknya **margin premium 15-20%** untuk compensate working capital cost.

---

## Container Imbalance — The "Empty Repositioning" Cost

Batam **import > export** (rough ratio 1.5-2 : 1). Artinya:
- Banyak kontainer impor masuk → banyak empty container nganggur di Batam
- Setelah customer bongkar, IRN tarik empty → simpan di depo
- Kontainer empty harus dipulangkan ke Singapore (transshipment hub) untuk recycle
- **Empty reposition** = cost untuk shipping line, sometimes pass-through ke trucking

Untuk IRN: reposition trip biaya operasional sama dengan trip impor (BBM, sopir komisi), tapi tarif-nya 50-70% impor karena empty. **Margin per empty trip = thin atau bahkan negatif**.

**Strategi:**
1. Combo trip — 1 sopir tarik empty + ambil load ekspor di trip sama (1 day, 2 leg, double revenue)
2. Negosiasi reposition rate dengan shipping line (block deal)
3. Hold empty di depo sampai ada batch besar

---

## Yield Management — Trip Mana Diambil, Mana Ditolak

### Decision Framework Per Trip

```
IF tarif - variable cost < Rp 200k contribution
   → REJECT (kecuali untuk fill chassis idle)

IF tarif - variable cost - allocated cost < Rp 0 (loss-leader)
   → REJECT KECUALI:
     - Customer strategic (long-term volume)
     - Slot lain idle
     - Empty reposition combo

IF tarif - variable - allocated cost > Rp 200k AND customer DSO < 90
   → ACCEPT
   
IF customer DSO > 120
   → NEGOSIASI: tarif +15% atau down payment
```

### Yield Maximization Tactics

1. **Combo route** — pickup port A + drop customer B + pickup empty customer C + drop port D dalam 1 hari (vs 4 hari single trips)
2. **Time-of-day pricing** — trip jam padat (8-11 pagi) tarif premium karena semua trucking sibuk
3. **Customer tier stratification** — Premium tier (95% SLA committed), Standard, Spot
4. **Idle prevention** — better take low-margin trip vs chassis nganggur 1 hari = Rp 200k lost

---

## Wrong Solution / Gimmick Trap

❌ **"Naikkan tarif semua customer 20%"** — beberapa customer akan switch, lose volume = lose absorption ratio.

❌ **"Optimasi rute pakai AI"** — bagus tapi setelah data 6 bulan tersedia. MVP: rule-based dispatcher dulu.

❌ **"Beli chassis baru karena kapasitas penuh"** — cek dulu utilization. Banyak owner beli unit baru saat utilization 50% — Rp 800jt buang.

❌ **"Akusisi customer baru via diskon agresif"** — race-to-bottom. Better: kuota slot premium tier yang lock-in 6 bulan kontrak.

---

## Right Solution by OUTCOME

| Solusi | Outcome | Effort |
|---|---|---|
| **Per-trip P&L tracking real-time** (auto-calc dari delivery + fuel + cost data) | Owner sadar margin per trip, bisa reject loss-makers — recover ~5% margin = Rp 70jt/bulan | 2 minggu dev |
| **Customer DSO dashboard** + auto-flagging | Reduce average DSO 5-10 hari = unlock cash Rp 100-200jt | 1 minggu dev |
| **Idle alert** (chassis idle >10 hari → suggestion) | Reduce empty reposition cost ~20% = Rp 6-12jt/bulan | 1 minggu dev |
| **Combo route suggestion** (AI atau rule-based) | Increase asset utilization 10-20% = Rp 50-100jt/bulan revenue | 1-2 bulan dev (rule-based) / 4+ bulan AI |
| **Customer tier pricing** dengan SLA-locked premium | Lock revenue, reduce yield erosion | Process change + 2 minggu dev |

---

## Customer Archetype

**"Pak Indra" — Owner 25-50 Unit Fleet**
- Pegang akuntansi sendiri / kasih ke ipar
- Lihat angka mingguan di buku kas, bukan dashboard
- Stress-driver: tanggal 25 belum ada cair customer — gimana bayar gaji
- **Pain emosional:** "Saya untung tapi miskin cash"
- **Reaksi positif:** "Ini pertama kali saya tahu margin per trip dengan jelas. Trip Tg Uncang ternyata loss kalau hari Sabtu."

---

## Quotable Hooks untuk Video Script

1. *"Profit on paper, kosong di rekening. Itulah Indonesian logistics. Yang survive bukan yang margin tinggi — yang manage cash."*
2. *"DSO 90 hari plus operasional harian. Anda butuh Rp 2 milyar working capital sebelum ke-50 trip."*
3. *"Setiap trip Anda ambil tanpa cek margin, mungkin Anda subsidize customer Anda — pakai uang Anda."*
4. *"Container imbalance Batam = 1.5 dibanding 1. Kalau Anda gak combo trip, 1 dari 3 trip Anda buang BBM."*
5. *"Owner pintar gak fight DSO. Mereka kasih premium 15% ke customer 90-hari, sambil senyum."*

---

## Feature Implications (MoSCoW)

| Feature | Priority | ROI |
|---|---|---|
| Per-trip P&L auto-calc | **MUST** | Save Rp 70jt/bulan margin |
| Customer DSO dashboard + aging | **MUST** | Cash unlock Rp 100-200jt |
| Cash flow forecast (rolling 30/60/90) | **MUST** | Prevent payday panic |
| Idle chassis/vehicle alert | **MUST** | Save Rp 6-12jt/bulan |
| Auto reject low-margin trip suggestion | **SHOULD** | Discipline owner decisions |
| Combo route rule-based suggestion | **SHOULD** | Revenue +10-20% |
| Customer tier with SLA premium pricing | **SHOULD** | Lock revenue, premium yield |
| AI-based yield optimization | **WONT (MVP)** | Phase 2+ |
| Working capital financing integration (factoring) | **COULD** | Cash management option |

---

## Cross-References

- Trip accept/reject framework → `07-decision-frameworks.md`
- Customer credit risk profiling → `06-tacit-batam-context.md`
- Tax + invoicing reconciliation → `08-tax-finance-invoicing.md`
- Crane unit economics detail → `03-crane-operations.md`
- Cash flow / DSO tech implementation → sister skill `smart-fleet-architect/02-data-model-schema.md`
