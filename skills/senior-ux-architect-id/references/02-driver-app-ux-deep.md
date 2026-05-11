# Reference 02 — Driver App UX Deep (Sopir-Facing)

> **Anchor — Layer: Karyawan (Indonesian general).** Pillar 3 (Mobile) data-capture surface that feeds Pillar 2 (IoT)-equivalent owner visibility. Sopir UX is **constraint optimization for owner-data-feed**, not driver convenience first. But this does NOT mean degrading dignity — face-saving framing + voice-note alternative + offline-first all mandatory.
>
> **Owner Visibility Gap covered:** 1 (kondisi lapangan), 2 (ETA), 3 (solar capture point), 4 (attendance), 5 (container position).

---

## 1. Design Principles (Sopir-Specific)

1. **1-tap completion for status update** — no multi-step forms during driving/idling
2. **Voice-note alternative for every text input** — issue reporting, comment, exception flag
3. **Geofence auto-prompt** — system pushes status request when sopir enters known location radius (depo, customer, SPBU)
4. **Face-saving error messages** — "Coba lagi ya Pak" / "Foto kurang jelas, bisa ulang?" — never accusation
5. **Offline buffer 5-30 min** — RAN congestion peak-hour, never block sopir at "no connection"
6. **Battery-aware** — foreground service mandatory but disclosed prominently (Android requires it anyway)
7. **Bahasa Indonesia + Batam-specific terms** — "tarik container", "bongkar", "ujung-belakang", "SP2 keluar", "depo Pelita" — preserve local vocabulary
8. **Multi-ethnic safe** — Melayu, Jawa, Batak, Minang, Bugis sopir mix; icons + naming conventions must work across all

---

## 2. Core Screens

### Screen A — Home (1 active trip default)

```
╔═══════════════════════════════════════╗
║ 👤 Pak Rohim — B-1234                  ║
║ ─────────────────────────────────────  ║
║ TRIP AKTIF                             ║
║                                        ║
║   📦 Container EISU456789              ║
║   ↘ Customer: PT Batamindo             ║
║   🕐 ETA: 15:30 (12 min lagi)         ║
║                                        ║
║   [ 📍 Saya Sudah Sampai ] ← 1-tap     ║
║   [ ⚠️ Ada Masalah ] (voice note)      ║
║                                        ║
║ ─────────────────────────────────────  ║
║ TRIP BERIKUTNYA (16:00)                ║
║   📦 TGHU445566 → PT Sinar Sejahtera   ║
║                                        ║
║ ─────────────────────────────────────  ║
║ ⛽ Solar: 45 L  📡 GPS: ✓  🔋 78%      ║
╚═══════════════════════════════════════╝
```

**Key behaviors:**
- "Saya Sudah Sampai" → triggers ePoD capture (foto + signature + auto-GPS), 1-tap to next screen
- "Ada Masalah" → opens voice-note record sheet (3-tap max workflow)
- Top status bar shows sopir context — solar reading, GPS health, battery (transparency builds trust)

### Screen B — Arrival ePoD Capture

```
╔═══════════════════════════════════════╗
║ ← Kembali           [Skip — Lapor]    ║
║ ─────────────────────────────────────  ║
║ KONFIRMASI SAMPAI                      ║
║ Container EISU456789                   ║
║ PT Batamindo                           ║
║                                        ║
║   [ 📷 Foto Container ] (mandatory)    ║
║   [   tapped — preview shown       ]   ║
║                                        ║
║   [ ✍️ Tanda Tangan Penerima ]         ║
║                                        ║
║   [ 🎤 Voice Note (optional) ]         ║
║                                        ║
║   📍 Auto-detect: PT Batamindo Gate 3  ║
║   (TGL 11-05-2026, 15:28)              ║
║                                        ║
║   [ ✓ KIRIM ] (large button)           ║
╚═══════════════════════════════════════╝
```

**Key behaviors:**
- Foto required; voice note optional; GPS auto-stamped (sopir doesn't have to enter)
- "KIRIM" button only enabled when foto + signature complete
- After tap, optimistic UI: returns to home with snackbar "Trip selesai, terima kasih Pak Rohim ✓"
- Backend sync in background; if fail, retry queue

### Screen C — Issue Report (Voice-Note-First)

```
╔═══════════════════════════════════════╗
║ ← Kembali                              ║
║ ─────────────────────────────────────  ║
║ LAPOR MASALAH                          ║
║                                        ║
║   Trip: B-1234 → PT Batamindo          ║
║                                        ║
║   Pilih jenis (atau langsung voice):   ║
║                                        ║
║   [ 🚧 Macet di Jalan ]                ║
║   [ ⛽ Solar Bermasalah ]              ║
║   [ 🔧 Kendaraan Rusak ]               ║
║   [ 📦 Container Issue ]               ║
║   [ ❓ Lainnya ]                       ║
║                                        ║
║   ─────── ATAU LANGSUNG ───────        ║
║                                        ║
║   [ 🎤 Tahan untuk Rekam Pesan ]       ║
║   (rekam max 60 detik)                 ║
║                                        ║
╚═══════════════════════════════════════╝
```

**Key behaviors:**
- Category buttons → quick context tag, then optional voice-note for detail
- Direct voice-note path for emergency/complex
- Auto-attach: trip context, GPS, timestamp, vehicle state (solar, battery, network)
- Dispatcher sees voice note + auto-context in dispatcher console (cross-skill `smart-fleet-architect/04-dispatcher-ai-autoassign.md`)

---

## 3. Geofence Auto-Prompt Pattern

When sopir enters known radius (depo, customer, SPBU):

```
┌─────────────────────────────────────┐
│  📍 Kelihatannya sudah sampai di    │
│     PT Batamindo. Sudah selesai?     │
│                                      │
│  [ Iya, Sudah Sampai ] [ Belum ]    │
└─────────────────────────────────────┘
```

- Non-blocking notification, dismissable
- "Belum" delays prompt 5 min (sopir maybe still navigating gate)
- Reduces sopir manual "I arrived" tap by 60-70% (Gojek pattern proven)

**Don't:** Auto-mark arrival without confirmation — sopir might still be queueing at gate.

---

## 4. Solar Fuel Capture (Anti-Fraud Touchpoint)

When sopir refuels:

```
╔═══════════════════════════════════════╗
║ KONFIRMASI ISI SOLAR                   ║
║                                        ║
║   📍 SPBU Mukakuning (auto-detect)     ║
║   🕐 11-05-2026, 09:15                 ║
║                                        ║
║   Liter yang diisi:                    ║
║   ┌────────┐                          ║
║   │  50    │ liter (keypad numerik)   ║
║   └────────┘                          ║
║                                        ║
║   [ 📷 Foto Struk SPBU ] (mandatory)   ║
║                                        ║
║   [ ✓ KONFIRMASI ]                     ║
║                                        ║
║   ℹ️ Sistem akan baca sensor tank.    ║
║      Kalau ada selisih, kita konfirmasi║
║      ke Pak Rohim ya.                  ║
╚═══════════════════════════════════════╝
```

**Note the disclosure footer:** "Sistem akan baca sensor tank. Kalau ada selisih, kita konfirmasi ke Pak Rohim ya." — transparency about anti-fraud loop, framed as **process not surveillance**. Sopir knows up-front this is how it works, no surprise. See `06-anti-fraud-with-dignity-ux.md` for the full clarification UX downstream.

---

## 5. Offline Behavior

| State | UI Indicator | User Action Allowed |
|---|---|---|
| Online + synced | (no indicator, or subtle ✓) | All actions |
| Online + syncing | small spinner top bar | All actions, queue grows |
| Offline + queue <5 | "📡 Offline — disimpan lokal" snackbar | All write actions queued |
| Offline + queue >5 | persistent banner "5 status menunggu sync" | Continue working, warn at 10+ |
| Offline >30 min | red banner "Cek koneksi Pak — data tertahan" | Warn but don't block |

**Voice notes specifically:** record locally (max 60 sec each), queue uploads, compress to 32kbps Opus.

---

## 6. Honorific + Greeting Logic

- Default address: **"Pak [first name]"** for male sopir, **"Mas [first name]"** for younger sopir (under ~35), **"Mbak"** for female admin/sopir (rare in trucking)
- Owner addressed in pull-up "Lapor ke `{{owner_honorific}}`" button — uses owner-side honorific config from `project-variables.md`
- Greeting per time-of-day:
  - 04:00-10:00: "Selamat pagi Pak"
  - 10:00-15:00: "Selamat siang Pak"
  - 15:00-18:00: "Selamat sore Pak"
  - 18:00-04:00: "Selamat malam Pak"

---

## 7. Error Messages (Face-Saving Library)

| Scenario | Bad Copy | Good Copy |
|---|---|---|
| Photo blurry | "Error: image quality too low" | "Foto kurang jelas Pak, bisa ulang?" |
| GPS fail | "GPS unavailable" | "GPS belum dapat sinyal, coba pindah tempat sebentar Pak" |
| Network fail | "Network error" | "Internet lagi lemot, kita simpan dulu — sync nanti otomatis" |
| Invalid amount | "Invalid input" | "Cek lagi angkanya ya Pak — sepertinya kebanyakan nol" |
| Login fail | "Authentication failed" | "Password belum cocok Pak — coba lagi atau hubungi admin" |
| Battery low | "Battery low — service may stop" | "Baterai mau habis Pak — colok charger biar GPS tetap nyala" |

**Pattern:** Always close with **next action** the sopir can take. Never blame, never accuse.

---

## 8. Multi-Ethnic Safety Notes

- **Avatar icon library** must include diverse skin tones, head coverings (peci, hijab, no covering)
- **Greeting variants** should support Malay/Javanese/Batak naming conventions in copy
- **Religious holiday widgets** in driver app: Eid + Ramadhan reminders (sopir-layer calendar)
- **Anti-pattern:** Default avatar = single ethnicity stereotype — use neutral silhouette or photo upload

---

## 9. Open Questions (TBD — pending WS7 + persona)

- Sopir demographic actual ethnic mix at IRN (Melayu / Jawa / Batak / Minang / Bugis percentage) — affects naming/greeting subtleties
- Sopir average phone tier (entry / mid / flagship) at IRN — affects asset weight budget
- Voice note adoption rate Indonesia trucking workforce — calibrate prominence vs text input default
- Whether to support multi-language UI Phase 2 (Bahasa Indonesia + Hokkien transliteration for Tionghoa-owned operations) — likely deferred Phase 2
