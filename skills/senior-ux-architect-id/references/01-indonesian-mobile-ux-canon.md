# Reference 01 — Indonesian Mobile UX Canon

> **Anchor — Layer: Both (owner + karyawan).** Indonesian mobile reality is the floor that ALL surfaces inherit. Override per-layer (owner Tionghoa register, karyawan face-saving) on top of this baseline.

---

## 1. Device Reality Baseline

| Constraint | Value | Design Implication |
|---|---|---|
| Screen size | **6-7 inch Android** dominant | Single-column layout, vertical-scroll-first, tap targets ≥44pt |
| RAM | **2-3 GB common** (budget Android) | Lean assets, avoid heavy animations, paginate long lists |
| Storage | **32-64 GB shared with WhatsApp + photos** | App install size <50MB target |
| Network | **4G Telkomsel >97%** Batam, peak-hour congestion 17:00-19:00 | Offline-first for write-heavy actions, optimistic UI, retry with backoff |
| OS distribution | Android 8-13 (long tail), iOS 5-10% B2B SME | Test Android 8 minimum, iOS as nice-to-have |
| Browser (PWA) | Chrome Android dominant; Safari for owner iPhone minority | PWA installable, but Flutter migration Phase 1 — already locked CLAUDE.md |

---

## 2. Interaction Pattern Defaults

### Touch + Gesture
- **Tap targets ≥44×44pt** (Android Material spec, but reinforced for older users)
- **Swipe-back gesture** common — don't hijack left-edge swipe
- **Long-press** for context actions (familiar from WhatsApp)
- **Pull-to-refresh** universally understood

### Modals + Sheets
- **Bottom sheet > center modal** for action confirmation (one-handed phone use, thumb reach)
- **Bottom sheet = 60-80% viewport height**, scroll within if more
- **Snackbar** for transient confirmations (3-5 sec)
- **Full-screen modal** only for multi-step input (e.g., onboarding)

### Lists + Cards
- **Card-based list** for owner dashboard signals — each card = 1 actionable insight
- **Density allowed** for Tionghoa owner (practical, data-prefer) — but EVERY data point must be glance-able
- **Empty state** must offer next action, not just "No data yet"

### Forms
- **Keyboard type matching** — numeric keypad for amount, decimal for liter/km, NOT default text
- **Inline validation** with face-saving framing ("Coba lagi ya Pak", "Format yang benar: 1.250.000")
- **Auto-format** as user types — Rp separator, phone number, etc.
- **Default values** wherever possible — reduce typing

---

## 3. WhatsApp-Adjacent UI Pattern

Indonesian users (especially blue-collar + SME) treat **WhatsApp as the baseline of UI familiarity**. Any app must feel WhatsApp-adjacent to clear the zero-training bar.

| WhatsApp Pattern | Apply to App |
|---|---|
| **Bubble-style chat thread** | Use for Asisten chat + sopir clarification + admin comm |
| **Voice note 1-tap record-and-send** | Mandatory in driver app for issue reporting |
| **Status indicator** (online / typing / last seen) | Show in dispatcher view for sopir activity |
| **Reply-to-message quoting** | Use in Asisten — quote previous turn for context |
| **Forwarded label** | Show source in dispatcher when surat jalan auto-forwarded |
| **Star / pin message** | Owner can pin Asisten high-value insights for later reference |
| **Search bar prominent** | Owner dashboard has top search across container_id, vehicle_id, sopir name |
| **Profile pic + name avatar** | Each entity (sopir, customer, container) has visual avatar |

**Anti-pattern:** Material Design FAB (floating action button) — alien to WhatsApp register. Use bottom-bar action or bottom-sheet trigger instead.

---

## 4. Color + Typography Baseline

### Color
- **Default light mode** — Indonesian Android users prefer light (outdoor sunlight) for blue-collar contexts
- **Dark mode optional** for owner (premium feel) but tested with sunlight legibility
- **Accent: Indonesian-safe color** — avoid pure yellow (mourning in some traditions), avoid red-only (luck/danger ambiguous)
- **Semantic colors:**
  - 🟢 OK / safe / clarified-operational
  - 🟡 attention / awaiting-clarification / scheduled
  - 🟠 anomaly-detected (soft alert, not urgent)
  - 🔴 confirmed-anomaly / urgent-alert (use sparingly)
  - ⚪ neutral / system / draft
- **Tionghoa overlay:** owner-facing surfaces can use **red as auspicious accent** (Imlek mode), gold as premium accent — see `09-tionghoa-batam-owner-overlay.md`

### Typography
- **System font** (Roboto Android / SF Pro iOS) — performance > brand-custom on budget devices
- **Sizes:**
  - Body: 14-16pt
  - Caption: 12pt
  - Heading: 18-22pt
  - Owner dashboard data-dense: 13pt allowed if high-contrast
- **Line height: 1.4-1.5** — Indonesian copy tends longer than English
- **Bahasa Indonesia copy** averages **20-30% longer than English** — design layout slack accordingly

---

## 5. Bahasa Indonesia Copy Rules

- **Honorific first** — "Pak", "Cik", "Koh", "Bapak", "Mas", "Mbak" — NEVER strip to plain second-person
- **Active voice preferred** but allow passive for face-saving ("Catatan: pengisian solar tadi pagi" — passive observation, not accusation)
- **Concrete > abstract** — `{{client_fleet_size}}` truk Anda" > "fleet Anda"
- **Numerical specificity** — "15 liter selisih" > "ada selisih"
- **Indonesian punctuation:**
  - Decimal: comma (Rp 1.250.000,50)
  - Thousand: period (Rp 1.250.000)
  - Time: 24-hour for fleet ops (14:30), AM/PM optional consumer
  - Date: DD-MM-YYYY default, "11 Mei 2026" formal

### Forbidden register
- ❌ Corporate-sterile ("optimalisasi", "sinergi", "transformasi digital")
- ❌ Hyper-formal ("Bapak yang terhormat") — sounds insincere
- ❌ Slang Jakarta (BBM "gue", "lo") — wrong for Batam Tionghoa SME register
- ❌ Mixed-up honorific (e.g., adding Japanese "-san" suffix to Indonesian "Pak X-san") — borrow looks pretentious

---

## 6. Offline-First Pattern

| Action | Strategy |
|---|---|
| Read-only fetch (dashboard data) | Cache last fetched, show stale badge if >5 min old |
| Write action (status update, photo upload) | Local queue, sync on connection, optimistic UI |
| Voice note | Local record + queue, sync upload background |
| Critical write (delivery completion) | Block until queued locally + sync started, show pending state |
| Conflict resolution | Server-wins by default, surface conflict for owner review if material |

**Buffer window:** 5-30 minutes per CLAUDE.md LOCK. Most peak-hour congestion <10 min, design for 30 min ceiling.

---

## 7. Accessibility (Often Underweighted in Indonesian Mobile)

- **Voice-note input as primary alternative** to text — covers low-literacy + dyslexic + situational (driving, dark, gloves) constraints
- **Icon + label** always paired — never icon-only (low-literacy demographic)
- **High contrast mode** support — outdoor sunlight extreme
- **Font size scalable** — older owner (50+) demographic
- **Color-blind safe** — never rely on color alone for status (use icon + color)

---

## 8. Performance Budget

- **First contentful paint** <2 sec on 4G + budget Android
- **Time to interactive** <4 sec
- **Bundle size** <500KB initial PWA / <50MB native install
- **Animation frame rate** 60fps target; degrade gracefully to 30fps on low-end
- **Battery** — driver app foreground service must be <5% battery/hour additional drain
