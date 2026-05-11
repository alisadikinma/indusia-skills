# Reference 06 — Anti-Fraud-With-Dignity UX

> **Anchor — Layer: Cross-layer bridge (Owner Tionghoa directness ↔ Karyawan Indonesian face-saving).** This is the UX implementation of the workflow specified in `smart-fleet-architect/10-anti-fraud-with-dignity.md`. Pillar 1 (AI detect) + Pillar 2 (IoT sensor) feed the system; Pillar 3 (Mobile WhatsApp + Owner Dashboard) is where UX lives.
>
> **Owner Visibility Gap covered:** Gap #3 (pencurian solar) primary; pattern applies to attendance fraud, GPS tampering, container manipulation Phase 2.

---

## 1. Three Surfaces in the Workflow

| Surface | User | Pillar | Purpose |
|---|---|---|---|
| **A.** Owner dashboard alert card | owner | Mobile | Real-time anomaly visibility with status badge |
| **B.** Sopir clarification message | Sopir Rohim | Mobile (WhatsApp) | Face-saving 24h clarification chance via Asisten |
| **C.** Owner confirmation prompt | owner | Mobile (WhatsApp via Asisten) | Y/N action gate after Stage 2 unresolved |

Each surface designed for **the cultural register of its user** — Tionghoa direct for owner, Indonesian face-saving for sopir.

---

## 2. Surface A — Owner Dashboard Alert Card

```
┌─────────────────────────────────────────┐
│ ⛽ B-5678 ANOMALI SOLAR                 │
│ ───────────────────────────────────────  │
│ Sopir: Pak Rohim                        │
│ Lokasi: SPBU Mukakuning                 │
│ Waktu: 11 Mei 2026, 09:15               │
│ Selisih: 15 L (isi 50, tank +35)        │
│                                          │
│ STATUS                                   │
│ 🟡 Klarifikasi terkirim 09:17           │
│ ⏳ Menunggu sopir (sisa 21h 58m)        │
│                                          │
│ HISTORI SOPIR (30 hari)                  │
│ • 2 anomali sebelumnya:                  │
│   - 1 clarified-operational (drip)       │
│   - 1 sensor-malfunction                 │
│ • z-score baseline: 0.8 (normal)         │
│                                          │
│ [ 💬 Tanya Asisten ]                     │
│ [ 🚨 Force Confirm Fraud (skip 24h) ]    │
└─────────────────────────────────────────┘
```

### Badge State Machine

| State | Badge | When |
|---|---|---|
| Detected — clarification sent | 🟡 yellow "Klarifikasi pending" | Stage 1 fires |
| Clarified — operational | 🟢 green "Clarified — operational" | Sopir provides reasonable explanation |
| Clarified — sensor malfunction | 🔧 blue "Sensor maintenance flagged" | Sopir/mechanic confirms hardware issue |
| Awaiting owner confirmation | 🟠 orange "Awaiting Bapak review" | Stage 2 unresolved, escalated |
| Confirmed fraud | 🔴 red "Owner-confirmed fraud" | Owner Y at Stage 3 |
| Acknowledged-tolerated | ⚪ gray "Tolerated (logged)" | Owner N at Stage 3 |

### Owner Override Path

`[ 🚨 Force Confirm Fraud (skip 24h) ]` button:
- Visible always on alert card
- Tapping opens confirmation modal:

```
┌─────────────────────────────────────────┐
│ `{{owner_honorific}}`, force confirm fraud B-5678?    │
│ ───────────────────────────────────────  │
│ Ini akan:                                │
│ • Skip 24h klarifikasi sopir             │
│ • Langsung tahan invoice fuel B-5678     │
│ • Notif Pak Rohim langsung               │
│ • Catat di audit log                     │
│                                          │
│ Alasan (opsional, untuk audit):          │
│ [_____________________________________]  │
│                                          │
│ [ Batal ]  [ Force Confirm ]            │
└─────────────────────────────────────────┘
```

Use case: same sopir has 3+ confirmed-fraud history, owner sees no need for further clarification.

---

## 3. Surface B — Sopir Clarification Message (WhatsApp)

Asisten sends via WhatsApp Business API:

```
─────────────────────────────────────
Asisten owner
─────────────────────────────────────
Pak Rohim, ada catatan pengisian solar 
tadi pagi jam 9:15 di SPBU Mukakuning — 
isi 50 liter tapi tank naik 35 liter.

Mungkin ada drip / leak / penyusutan 
udara panas, atau ada yang lain? Bisa 
konfirmasi ya. Balas chat ini dalam 24 
jam (sebelum besok jam 9:15).

🎤 Bisa balas voice note juga ya Pak.

─────────────────────────────────────
                                 09:17
```

### Sopir Reply UX Branches

**Branch 1 — Text reasonable explanation:**
```
Pak Rohim (10:32):
"Pak, tadi tank-nya ada drip kayaknya, 
saya cek bawah ada bekas tetesan. Saya 
suruh mekanik cek nanti sore."

Asisten (10:33):
"Oke Pak Rohim, terima kasih. Saya 
catat 'kemungkinan drip — pending 
mekanik review'. Mekanik saya kasih 
tahu juga ya."
```

→ Status badge moves: 🟡 → 🔧 sensor maintenance / 🟢 clarified-operational pending mechanic confirm

**Branch 2 — Voice note reply:**
```
Pak Rohim (10:32):
🎤 0:23
[Asisten STT processes, attaches both 
voice + transcript to log]

Asisten (10:33):
"Oke Pak Rohim, saya dengar tadi soal 
drip dan mekanik. Saya catat ya. 
Mekanik saya kasih tahu juga."
```

**Branch 3 — Sopir admits alternative use:**
```
Pak Rohim (10:32):
"Pak, jujur saya kasih supir cadangan 
adik saya 10 liter, dia mau pulang 
kampung. Saya ganti di bulan ini."

Asisten (10:33):
"Saya catat ya Pak Rohim. Hal ini akan 
saya forward ke owner untuk review 
— Bapak akan dihubungi langsung."

[Backend: Stage 3 escalate immediately, 
admission text attached to owner alert]
```

**Branch 4 — No response after 24h:**
```
[After 24h elapsed, no sopir reply]

Asisten → owner (auto-push):
"Pak, B-5678 fuel anomali 15 L kemarin 
belum terklarifikasi oleh Pak Rohim 
(24h window habis). 

Saya tahan invoice fuel B-5678 sampai 
Bapak review? Y/N — atau D = detail."
```

---

## 4. Surface C — Owner Confirmation Prompt (via Asisten WhatsApp)

```
─────────────────────────────────────
Asisten owner
─────────────────────────────────────
Pak, B-5678 fuel anomali 15 L (jam 9:15 
SPBU Mukakuning) belum terklarifikasi.

Sopir Rohim balas: "Saya tidak tahu Pak, 
biasa aja kayaknya." (klarifikasi vague)

Saya tahan invoice fuel B-5678 
(Rp 850k) sampai Bapak review?

Y = tahan / N = lanjut / D = detail
─────────────────────────────────────
                                09:17+1d
```

### Owner Y Flow

```
owner (09:23+1d):
"Y"

Asisten:
"Oke `{{owner_honorific}}`, invoice ditahan. Sharah 
(accounting) saya cc. Mau saya update 
kalau ada perkembangan?"

owner: "Iya."

Asisten: "Siap ✓"

[Backend: hold_invoice(INV-5678) called, 
audit log written with permission_check
='owner-confirmed:Y']
```

### Owner N Flow

```
owner: "N"

Asisten:
"Oke `{{owner_honorific}}`, saya lanjut invoice. 
Tetap saya catat di audit log."

[Backend: alert marked owner-acknowledged-
tolerated, audit retained]
```

### Owner D Flow (Detail)

```
owner: "D"

Asisten:
"Pak, detail B-5678 anomali:
• Tank reading: 09:14 = 12 L
• SPBU isi: 09:15, 50 L
• Tank reading: 09:18 = 47 L
• Selisih: 15 L
• Histori sopir Rohim 30 hari: 2 anomali 
  (1 drip-confirmed, 1 sensor-malfunction)
• z-score baseline Joni: 0.8 (normal)
• Lokasi GPS verified SPBU Mukakuning

Foto struk SPBU dari Rohim: [link]

Tahan invoice B-5678 sekarang? Y/N"
```

---

## 5. Edge Cases UX

| Case | UX Handling |
|---|---|
| Sopir cuti / sakit saat anomaly | Stage 2 message delayed until sopir on-duty + 24h. Owner card shows "Klarifikasi tertunda — Pak Rohim cuti, akan dikirim 13 Mei" |
| Sopir resigned | Stage 2 skipped. Owner card: "Sopir tidak lagi karyawan — auto-escalate ke Bapak" |
| Multiple anomaly same sopir same shift | Batched into single Asisten message: "Pak Rohim, ada 2 catatan pengisian solar tadi (jam 9:15 dan 14:32). Konfirmasi keduanya ya Pak." |
| Religious holiday during 24h window | Window extends. Owner card: "Klarifikasi diperpanjang sampai post-Imlek + 24h" |
| Sopir admits with mitigating context (sakit anak, etc.) | Stage 3 prompt includes full admission context. Owner action space includes "kasbon top-up + counseling" custom path |
| False positive (sensor confirmed broken) | Sopir + mechanic both confirm hardware. Alert → 🔧 sensor maintenance, no fraud action. Audit retains |

---

## 6. Metrics Dashboard (Owner-facing)

Owner can view weekly anti-fraud summary:

```
┌─────────────────────────────────────────┐
│ ANTI-FRAUD MINGGU INI                   │
│ ───────────────────────────────────────  │
│ Anomali terdeteksi: 7                    │
│   ✓ Clarified — operational: 4 (57%)    │
│   ✓ Sensor maintenance: 1 (14%)         │
│   ⚠ Confirmed fraud: 1 (14%)            │
│   ⚪ Tolerated: 1 (14%)                  │
│                                          │
│ False positive rate: 71% ⚠️ Tinggi      │
│ (target <15%) — review threshold?       │
│                                          │
│ Rata-rata respon sopir: 6.2h            │
│ Rata-rata respon Bapak: 2.3h            │
│                                          │
│ Total potensial loss avoided: Rp 850k   │
│                                          │
│ [ Tune Threshold ] [ Review Each ]      │
└─────────────────────────────────────────┘
```

**False positive rate flagging:** >15% → suggest threshold tuning OR sensor calibration check. This protects sopir from over-detection AND owner from data-noise fatigue.

---

## 7. Sopir-Side Settings (Driver App)

Sopir can view in driver app:
- Their own anomaly history (only theirs — not other sopir)
- Sensor health for their assigned vehicle
- Filed clarifications + outcomes ("Ini yang Bapak klarifikasi minggu lalu — sudah resolved")

```
┌─────────────────────────────────────────┐
│ RIWAYAT KLARIFIKASI ANDA                │
│ ───────────────────────────────────────  │
│ 11 Mei 2026 — B-5678 fuel              │
│ Status: 🟢 Clarified (drip — sensor mtc)│
│                                          │
│ 4 Apr 2026 — B-5678 fuel               │
│ Status: 🔧 Sensor malfunction (ganti)   │
│                                          │
│ 22 Mar 2026 — B-1122 fuel              │
│ Status: 🟢 Clarified (operational)      │
└─────────────────────────────────────────┘
```

**Why:** Sopir sees system is FAIR — most cases resolve in their favor. Reduces "diawasi seperti maling" feeling. Trust loop.

---

## 8. Anti-Patterns

❌ **Owner-only visibility without sopir clarification stage** — Approach A fail mode (file 10 §1)
❌ **Sopir clarification without owner full data** — Approach B fail mode
❌ **Public-broadcast anomaly** — only sopir + owner + supervisor (if escalated) see
❌ **No voice-note option in sopir clarification** — LOCK-5 violation
❌ **Aggressive sopir message framing** ("Anda mencuri solar?") — face-saving violated
❌ **Auto-execute hold invoice without owner Y/N** — LOCK-3 principle violation
❌ **No false positive rate metric** — owner over-detection fatigue + sopir resentment

---

## 9. Cross-References

- Workflow spec → `smart-fleet-architect/10-anti-fraud-with-dignity.md`
- Detection tech → `smart-fleet-architect/05-anti-fraud-tech-stack.md`
- Asisten conversation architecture → `smart-fleet-architect/11-ceo-ai-assistant-architecture.md`
- Driver app fuel capture screen → `02-driver-app-ux-deep.md` Section 4
- Owner dashboard alert card → `03-owner-dashboard-ux.md` Section 7 (Anomaly Detail)
- Cultural register rules → `04-conversational-ux-canon.md` + `09-tionghoa-batam-owner-overlay.md`
