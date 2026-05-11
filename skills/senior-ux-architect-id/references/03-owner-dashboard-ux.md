# Reference 03 — Owner Dashboard UX (HP-First, owner Layer)

> **Anchor — Layer: Owner (Tionghoa Batam default).** Glance-able + signal-over-noise + Tionghoa practical register (data-dense allowed, but actionable). Owner accesses primarily via **HP** (not desktop) — design mobile-first.
>
> **Owner Visibility Gap covered:** ALL 7. Owner dashboard is the visual unifier; Asisten chat is the conversational unifier. Two complementary surfaces, not duplicates.

---

## 1. Design Principles (Owner-Specific)

1. **HP-first** — owner mostly checks via phone, not laptop. Desktop dashboard nice-to-have.
2. **Glance-able < 5 sec** — Owner opens app, immediately sees fleet health, any anomaly, day's revenue trajectory.
3. **Signal > noise** — Only surface what requires owner attention. Background ops stay in Asisten / detailed views.
4. **Tionghoa practical register** — Data-dense allowed (Tionghoa owner is comfortable with numerical density), BUT every number must answer "so what do I do?"
5. **Religious calendar awareness** — Imlek / Cap Go Meh / Qingming mode shifts to quiet operation (proactive alerts only critical).
6. **Cross-pillar surfaces** — One screen surfaces AI insight (anomaly) + IoT data (sensor reading) + Mobile context (dispatcher state) — owner doesn't think in pillars, just outcomes.
7. **Direct address owner** — copy uses "Bapak" / "`{{owner_honorific}}`" (per LOCK-4 default), not "User" or "Anda generic"

---

## 2. Home Screen (Owner HP) — Wireframe

```
╔═══════════════════════════════════════╗
║ Selamat siang `{{owner_honorific}}` ✱               ║
║ Senin, 11 Mei 2026 — 14:32             ║
║ ─────────────────────────────────────  ║
║                                        ║
║ ⚠️ 1 ALERT — Perlu Review             ║
║ ┌─────────────────────────────────┐   ║
║ │ ⛽ B-5678 anomali solar 15 L     │   ║
║ │ Sopir Rohim — Mukakuning 09:15  │   ║
║ │ [ Tanya Asisten → ]              │   ║
║ └─────────────────────────────────┘   ║
║                                        ║
║ FLEET HARI INI                         ║
║   🚛 23 / 23 aktif                    ║
║   📦 47 container in-transit          ║
║   🅿️ 12 container di yard slot         ║
║   ⏱ 18 trip done · 5 in-progress     ║
║                                        ║
║ KEUANGAN HARI INI                      ║
║   💰 Revenue est. Rp 24,5 jt          ║
║   📊 Margin trend: +3,2% vs minggu lalu║
║   ⏳ AR aging 60+: Rp 145 jt (Sinar S.)║
║                                        ║
║ ─────────────────────────────────────  ║
║ [ 💬 Tanya Asisten ] (FAB equivalent)  ║
╚═══════════════════════════════════════╝
```

**Key behaviors:**
- Alert card at top — only if there is one. If zero, hide the section (don't show "0 alerts" badge clutter).
- Quick numbers row — fleet activity + container + revenue. Tap each to drill in.
- AR aging only surfaced if 60+ days bucket has material amount.
- Persistent "Tanya Asisten" button — Asisten chat is **one tap away from any screen**.

---

## 3. 7 Owner Visibility Gaps Surfacing

Each gap has a **glance card** + drill-in detail:

| Gap | Glance Card | Drill-in Screen |
|---|---|---|
| 1. Kondisi lapangan | Fleet activity row "23/23 aktif · 5 in-progress" | Live map dengan vehicle pin + sopir + ETA |
| 2. ETA driver | (within Gap 1 drill-in) per-trip predicted vs actual | Timeline view |
| 3. Pencurian solar | Alert card top (if anomaly) | Anomaly detail + clarification status badge |
| 4. Attendance | Card "Karyawan hadir 22/25 hari ini (3 telat)" | RFID gate log per-employee |
| 5. Container jalan | Card "47 in-transit" | Asset map dengan container pin |
| 6. Chassis | Card "90 chassis · 67 di yard / 23 in-trip" | Chassis map + slot occupancy |
| 7. Container gudang | Card "Yard slot 12/200 occupied" | Yard heatmap (slot grid B5C3 dst) |

**Rule:** Each card has a state — calm (default), attention (yellow), alert (red). Owner glance pattern: scan top-down 2 seconds, eyes catch any non-calm color, drill in.

---

## 4. Data Density Calibration (Tionghoa Practical Register)

Owner is comfortable with **3-4 data points per card** as long as each is glance-able. Example:

```
┌───────────────────────────────────────┐
│ FLEET HARI INI                        │
│ 🚛 23/23  📦 47 in-transit  🅿️ 12      │
│ ⏱ 18 done · 5 in-progress             │
│ 💡 Trip rata-rata 2h 18m (-12m vs avg)│
└───────────────────────────────────────┘
```

4 data points + 1 actionable insight (the -12m trend). Owner reads this in <3 sec and knows: fleet utilization OK, trip duration trending better.

**Anti-pattern:** Corporate big-data dashboard with 50 KPIs on one screen — Eddi-style competitor approach. Owner gets overwhelmed, ignores.

**Sweet spot:** 4-6 cards per screen, each card 3-4 data points + 1 actionable insight. Below the fold = detailed sections, drilled in on demand.

---

## 5. Religious Calendar Mode

Owner can pre-set religious-calendar holidays (Imlek, Cap Go Meh, Qingming) via Asisten preference or Settings:

```
┌───────────────────────────────────────┐
│ 🏮 SELAMAT IMLEK `{{owner_honorific}}`              │
│ Operasi mode-libur aktif              │
│ 5 sopir on-call (B-1234, B-5678, …)   │
│ Asisten kirim alert hanya kalau kritis│
│ ─────────────────────────────────────  │
│ [ Tetap Lihat Detail Hari Ini ]       │
└───────────────────────────────────────┘
```

- During Imlek week: dashboard shows muted color (red+gold subtle accent — auspicious), background ops summary only, alerts filtered to critical-only
- Same for Eid mode (karyawan layer perspective, kalau owner pilih ikut libur full)
- Owner can manually exit mode anytime

---

## 6. Asisten Integration (Pull + Push)

The dashboard and Asisten chat are complementary — they share state:

| User flow | Surface |
|---|---|
| Owner glances dashboard, sees anomaly card | Dashboard (visual) |
| Owner taps "Tanya Asisten" on card | Opens Asisten chat with pre-filled context "Cerita tentang anomali B-5678" |
| Asisten replies with detail + suggested action | Asisten chat |
| Owner says "Tahan invoice" | Permission-escalation Y/N in chat |
| Action executed | Snackbar on dashboard "Invoice ditahan ✓", dashboard alert card → status update |

**Anti-pattern:** Asisten chat as separate silo — owner switches context. Instead, Asisten is the **conversational layer over dashboard**; both surface same underlying ops state.

---

## 7. Drill-in Detail Patterns

### Live Fleet Map (Gap 1, 5)

```
[Mapbox tiles]
  📍 B-1234 ← Rohim — PT Batamindo ETA 15:30
  📍 B-5678 ← Joni  — Mukakuning  ⚠️ anomali
  📍 B-9012 ← Hamdani — Batu Ampar QUEUE
  📍 [ + 20 vehicle ]
  
  Filter: [ Aktif ] [ Idle ] [ Maintenance ]
  Search: [ container_id / vehicle / sopir ]
```

### Anomaly Detail (Gap 3)

```
B-5678 Fuel Anomaly
─────────────────
🕐 11 Mei 2026, 09:15
📍 SPBU Mukakuning (auto-detect)
👤 Sopir: Pak Rohim

Reported: 50 L isi
Tank sensor naik: 35 L
Selisih: 15 L (anomali threshold ≥10 L)

Status: 🟡 Klarifikasi terkirim 09:17
        ⏳ Menunggu balasan sopir (sisa 21h 58m)

Histori sopir 30 hari:
  - 2 anomali sebelumnya (1 clarified-operational, 1 sensor-malfunction)
  - Fuel consumption baseline z-score: 0.8 (normal)

ACTIONS
  [ 💬 Tanya Asisten ]
  [ 🚨 Force Confirm Fraud (override 24h window) ]
  [ 🔧 Flag Sensor Maintenance ]
```

### AR Aging (Finance Gap)

```
AR AGING SUMMARY
─────────────────
Total piutang: Rp 487 jt

Bucket:
  ▰▰▰▰▰▰▰▰  0-30 hari: Rp 240 jt (49%)
  ▰▰▰▰▰     31-60 hari: Rp 100 jt (21%)
  ▰▰▰      61-90 hari: Rp  87 jt (18%)
  ▰▰       91+ hari:   Rp  60 jt (12%)

Top 3 aging 60+:
  1. PT Sinar Sejahtera   Rp 145 jt (73 hari)
  2. CV Mitra Lestari     Rp  68 jt (62 hari)
  3. PT Karya Mandiri     Rp  34 jt (65 hari)

[ 💬 Tanya Asisten — strategi collection ]
```

---

## 8. Performance + Refresh

- Home screen loads in <2 sec on 4G
- Pull-to-refresh manual; auto-refresh every 60 sec while screen active
- Background sync every 5 min while app backgrounded (battery-aware)
- Live map updates every 15-30 sec (vehicle positions)
- Asisten alert push: real-time WhatsApp delivery (separate from dashboard sync)

---

## 9. Open Questions (TBD — pending persona validation)

- owner preferred data density actual (4-6 cards default OK? Or prefer fewer + bigger?)
- Currency display preference (Rp 24,5 jt shorthand OK? Or full Rp 24.500.000?) — Q11 persona
- Religious calendar set — confirm Imlek + Cap Go Meh + Qingming (Q9 persona)
- Honorific actual (`{{owner_honorific}}` vs owner vs Bapak vs other) — Q4 persona
- Working-hours window for quiet mode (subuh start? evening cap?) — Q8 persona
