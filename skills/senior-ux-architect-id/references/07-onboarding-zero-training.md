# Reference 07 — Onboarding Zero-Training

> **Anchor — Layer: Both** (owner + karyawan onboarding flows differ). Zero-training test: would the user adopt without a 30-min coached session? If no, redesign.
>
> **Adoption strategy:** Leverage WhatsApp pattern familiarity (everyone in Indonesia uses WhatsApp) + progressive disclosure + in-context guidance instead of upfront tutorials.

---

## 1. Adoption Pattern by User

| User | Onboarding Time Budget | Channel | Success Criteria |
|---|---|---|---|
| **Owner (owner)** | **5 minutes max** | WhatsApp activation of Asisten | Owner sends 1st query within 24h of activation |
| **Sopir (Pak Rohim & 22 lain)** | **1 shift (4-8 hours)** | Driver app PWA install + 1 trial trip | Sopir completes 1 ePoD without admin help |
| **Admin (Sharah)** | **30 minutes** | Admin app web walkthrough | Admin completes daily reconciliation independently within day 3 |
| **Accountant (Phase 2)** | **30 minutes** | Web walkthrough with Coretax integration demo | Accountant generates 1 monthly report within week 1 |
| **Customer (Phase 2)** | **0 minutes** | Self-service tracking link via WhatsApp/email | Customer clicks link, sees status, no friction |

**Anti-pattern:** Multi-hour onboarding course / training PDF / scheduled call. Zero-training means materials exist for reference but adoption doesn't require them.

---

## 2. Owner Onboarding — Activating Asisten (5 min)

### Step 1 (30 sec) — Save Asisten contact

Admin/INDUSIA team adds Asisten WhatsApp Business number to owner's contacts:
- Name: "Asisten owner" (or owner-chosen name per LOCK-3 §7)
- Number: dedicated WhatsApp Business API number provisioned by Wati/360dialog

### Step 2 (1 min) — First contact + persona seed

owner sends "Halo" (or anything) to Asisten:

```
owner: "Halo"

Asisten: "Pak, saya Asisten untuk fleet IRN. 
         Saya tahu kondisi 23 truk + 90 chassis 
         Bapak real-time. Bapak bisa tanya:
         
         • Kondisi sopir / truk
         • Solar / fuel anomali  
         • Attendance karyawan
         • Posisi container / chassis / yard
         • Cashflow / AR aging
         
         Sebelum mulai — Bapak prefer dipanggil 
         '`{{owner_honorific}}`', 'Cik', 'Koh', atau 'owner'?"

owner: "`{{owner_honorific}}`"

Asisten: "Siap `{{owner_honorific}}`. Coba tanya saya satu hal — 
         mau cek apa hari ini?"
```

→ `asisten_owner_preference.greeting` seeded.

### Step 3 (2 min) — First real query

owner asks anything. Asisten responds with real ops data + offers follow-up. Owner sees the value immediately.

### Step 4 (1.5 min) — Preference probing (gradual)

Over first 7 days, Asisten gently probes preferences via natural conversation:

- "`{{owner_honorific}}`, hari ini ada Imlek — mau saya schedule quiet mode tahun depan otomatis?" → seeds `religious_calendar`
- "Bapak biasa cek pagi atau sore? Saya bisa adjust push timing." → seeds `working_hours`
- "Lebih suka jawaban pendek atau detail?" → seeds `response_length`

**No upfront form.** Preferences emerge from conversation. `source='learned'` in DB.

---

## 3. Sopir Onboarding — Driver App (1 shift)

### Step 1 (5 min) — Install + login

Admin sends WhatsApp link to sopir: "Pak Rohim, ini link aplikasi driver IRN, install dan masuk pakai nomor HP Bapak."

PWA install:
- Single-tap "Add to Home Screen" prompt
- OR Flutter APK install (Phase 1+)

Login via OTP (no password to forget):
- Sopir enters phone number
- Receives OTP via WhatsApp Business API
- Enters OTP, logged in

### Step 2 (3 min) — First-screen guided tour

Sopir lands on home screen with 3-tap-tour (skip-able):

```
[Tap 1] "Selamat datang Pak Rohim. Di sini Bapak lihat 
        trip aktif. Tinggal tap 'Saya Sudah Sampai' 
        kalau sudah sampai customer."

[Tap 2] "Kalau ada masalah — macet, kendaraan rusak, 
        container issue — tap 'Ada Masalah' atau langsung 
        rekam voice note."

[Tap 3] "Sistem auto-detect kalau Bapak sudah sampai 
        depo / customer. Tinggal konfirmasi saja. 
        Mulai sekarang yuk."
```

Tour skip-able anytime. No more upfront teaching.

### Step 3 (1 shift) — Real trip pickup

First real trip: admin assigns via dispatcher. Sopir sees:

```
Notif: "Pak Rohim, trip baru — B-1234 ke PT Batamindo. 
       Buka app untuk detail."
```

Sopir opens app, sees trip, executes. If stuck, admin shadow-supports via WhatsApp.

### Day 1 metric: sopir completes ePoD on first trip without admin call

If sopir struggles at step (e.g., ePoD foto upload), admin troubleshoots via WhatsApp screenshot/voice. After 1-2 trips, sopir independent.

---

## 4. Admin Onboarding — Admin App (30 min walkthrough)

### Session structure (30 min)

1. **5 min** — App tour: dispatch view, vehicle list, customer list, invoice queue
2. **10 min** — Walk through 1 real workflow: receive customer order via WhatsApp parser → review draft delivery → assign sopir → confirm
3. **10 min** — Walk through end-of-day reconciliation: review trip log, photo audit, dispute resolution flow
4. **5 min** — Q&A + reference card handed out (1 page, laminated)

### Reference card (1-page laminated)

```
ADMIN IRN — Daily Workflow
─────────────────────────
PAGI (07:00-09:00)
• Cek dispatcher view — semua sopir assigned?
• Review draft delivery from WhatsApp parser
• Update jadwal trip ke-2/3

SIANG (12:00-13:00)
• Cek trip in-progress, photo audit kalau ada complaint
• Process customer call (urgent flag)

SORE (16:00-18:00)
• Recap trip done
• Generate surat jalan PDF untuk customer
• Submit invoice draft

REKONSILIASI (akhir hari)
• Cek attendance dari RFID gate
• Reconcile cashflow (kasbon vs komisi)

KONTAK
• owner: [WhatsApp]
• Asisten: [number] — tanya kapan saja
• INDUSIA support: [number]
```

### Post-walkthrough: shadow-support 3 days

INDUSIA team available via WhatsApp for any question first 3 days. After that, admin independent.

---

## 5. Customer Onboarding — Zero (Self-Service)

Customer doesn't onboard at all. They receive:

```
Customer WhatsApp from PT IRN admin:
"Pak/Bu [name], container TGHU445566 untuk pengiriman 
besok pagi sudah scheduled. Tracking link: [URL]

Bisa juga reply 'cek' di chat ini kapan saja, kami kabari 
status real-time."
```

Customer clicks link, sees tracking page (no login required):

```
┌─────────────────────────────────────┐
│ TRACKING — TGHU445566               │
│ ─────────────────────────────────  │
│ 🚛 B-1234, Sopir Rohim              │
│ Berangkat: 11 Mei 06:30              │
│ Sampai: ETA 11 Mei 08:45             │
│                                      │
│ STATUS                               │
│ ✓ Di depo, loaded                    │
│ ✓ Berangkat                          │
│ ✓ Di jalan — Mukakuning              │
│ ⏳ ETA customer                       │
│                                      │
│ [ Lihat Peta ]                       │
└─────────────────────────────────────┘
```

Zero friction. Bookmark optional.

---

## 6. Progressive Disclosure Pattern

In-app guidance appears **only when context demands it**, not upfront:

| Trigger | Tooltip / Coachmark |
|---|---|
| First time owner sees anomaly card | "Tap 'Tanya Asisten' untuk detail + action. Atau force confirm fraud kalau Bapak sudah yakin." |
| First time sopir taps "Ada Masalah" | "Pilih kategori atau langsung voice note — Bapak bisa juga combine keduanya." |
| First voice note from sopir | "Voice note tersimpan ✓ — kalau koneksi lemot, kita sync nanti otomatis." |
| First time admin sees AR aging 60+ alert | "Ini piutang tertua 60+ hari. Klik untuk drill-in collection strategy." |

Each tooltip dismissable, doesn't repeat after dismissal.

---

## 7. Help Channels (Fallback for Stuck Users)

Layered help:

1. **In-app contextual tooltip** (progressive disclosure)
2. **Asisten WhatsApp chat** — owner/admin can ask anytime ("Pak, gimana cara X?")
3. **WhatsApp support to INDUSIA team** — for technical issues sopir/admin can't resolve
4. **Phone call INDUSIA team** — last resort, urgent only

**Anti-pattern:** Help center webpage with 50 articles. Users won't read. Conversational help via Asisten + WhatsApp.

---

## 8. Adoption Metrics

Track per cohort (sopir batch, admin batch, owner activation):

| Metric | Target |
|---|---|
| Owner: time to first query | <24h post-activation |
| Owner: queries per week (steady state, post-first-month) | 10-30 |
| Sopir: ePoD completion rate (first trip) | >80% without admin help |
| Sopir: voice note adoption | >40% of issue reports use voice note |
| Admin: time to independent operation | <3 days |
| Customer: tracking link CTR | >60% |
| Customer: inbound "where is my container" calls | <30% of orders (down from baseline 100%) |

---

## 9. Anti-Patterns

❌ **Mandatory tutorial video** — skip-able yes, mandatory no
❌ **30-min training session prerequisite** — adoption gate friction
❌ **Login + password + email confirm + 2FA enroll** — OTP via WhatsApp single-step
❌ **English UI defaults** — Bahasa Indonesia primary
❌ **Hidden critical buttons** — primary actions visible without scroll
❌ **No fallback Asisten help** — users get stuck → abandon
❌ **Force feature discovery upfront** — progressive disclosure only

---

## 10. Open Questions (TBD — pending pilot test)

- Sopir actual time-to-first-ePoD-success (validate target 1 shift)
- Owner actual query frequency steady-state (sizing Asisten capacity)
- Admin pain points in walkthrough (run pilot 1 admin, observe)
- Customer tracking link CTR baseline (depends on customer profile)
- INDUSIA support load Phase 1 — staffing implications
