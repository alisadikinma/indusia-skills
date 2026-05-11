# Reference 08 — Component Library Indonesian Calibration

> **Anchor — Layer: Both** (cross-cutting). Component primitives calibrated for Indonesian conventions — currency, date, time, address, honorific, religious-calendar widget, multi-ethnic-safe iconography.

---

## 1. Currency Component

### Format

Indonesian standard: `Rp 1.250.000` (thousand `.`, decimal `,`).

| Magnitude | Default Display | Shorthand (owner dashboard) |
|---|---|---|
| <Rp 1.000 | `Rp 850` | same |
| Rp 1k - 999k | `Rp 850.000` | `Rp 850 rb` (option) |
| Rp 1jt - 999jt | `Rp 1.250.000` | `Rp 1,25 jt` |
| Rp 1M - 999M | `Rp 1.250.000.000` | `Rp 1,25 M` |

**Decision rule:** Full format in invoice, transaction history, audit log. Shorthand allowed in owner dashboard glance card (Tionghoa practical preference Q11 to validate).

### Negative values
- Display: `(Rp 850.000)` parentheses OR `-Rp 850.000` minus. owner preference Q11.

### Input

- Auto-format as user types: `1250000` → renders `Rp 1.250.000`
- Decimal allowed for liter (`50,5 L`) but rare for currency Indonesian SME context
- Keypad: numeric with `.` and `,` accessible

---

## 2. Date + Time Component

### Date Format

| Context | Format | Example |
|---|---|---|
| Default UI | `DD-MM-YYYY` | `11-05-2026` |
| Formal / report | Long form Indonesian | `11 Mei 2026` |
| Relative (recent) | "kemarin", "tadi pagi", "2 jam lalu" | "tadi pagi" |
| Absolute (older) | Long form | "11 Mei 2026, 09:15" |

### Time Format

- **24-hour for ops** (`14:30`) — fleet, dispatcher, ePoD timestamp
- **12-hour AM/PM optional consumer-facing** (`2:30 PM`) — but Indonesian users comfortable with 24h
- **WIB timezone displayed** when ambiguous: `09:15 WIB`

### Indonesian Month Names

```
Januari, Februari, Maret, April, Mei, Juni,
Juli, Agustus, September, Oktober, November, Desember
```

Abbreviation: `Jan, Feb, Mar, Apr, Mei, Jun, Jul, Agt, Sep, Okt, Nov, Des` (Mei full, Agt short for Agustus, Okt for Oktober — Indonesian-specific abbreviations).

### Religious Calendar Annotations

When applicable date is religious holiday, render with badge:

```
11 Februari 2027 🏮 Imlek
1 Maret 2027 🌙 Eid al-Fitr (estimated)
```

Calendar component supports overlaying:
- Tionghoa: Imlek, Cap Go Meh, Qingming, Vesak
- Indonesian: Eid al-Fitr, Eid al-Adha, Maulid Nabi, Isra Mikraj, Christmas, Good Friday, Nyepi, Waisak
- National: Independence Day (17 Agustus), Pancasila Day, etc.

---

## 3. Honorific Component

Input + display component for person name with honorific:

### Schema

```typescript
type PersonName = {
  honorific: "Pak" | "Bapak" | "Cik" | "Koh" | "Mbak" | "Bu" | "Mas" | null
  firstName: string
  lastName?: string
  preferredFullDisplay?: string  // e.g., "`{{owner_honorific}}`", "Cik Lim"
}
```

### Display patterns

| Context | Format |
|---|---|
| Greeting | "Selamat pagi Pak Rohim" |
| Address line | "`{{owner_honorific}}` / Cik Lim" |
| Reference in copy | "owner", "sopir Pak Rohim", "admin Mbak Sharah" |
| Audit log | Full name + honorific |
| Avatar tooltip | "Pak Rohim Wijaya" (full if needed) |

### Owner preference seeding

`asisten_owner_preference.greeting.honorific` overrides default per-user. Persona Q4 seeds for owner.

### Avatar component

```
┌─────────────┐
│   Avatar    │   ← photo if available, else
│   image     │     initials "PR" (Pak Rohim)
│  or initial │     with ethnic-safe color palette
└─────────────┘
```

Diverse skin tone library + neutral silhouette fallback (no single-ethnicity stereotype).

---

## 4. Address Component (Indonesian)

```typescript
type IndonesianAddress = {
  street: string         // "Jl. Tanjung Pinang II No. 12"
  rt?: string            // "RT 03"
  rw?: string            // "RW 05"
  kelurahan?: string     // "Mukakuning"
  kecamatan?: string     // "Batuaji"
  kota: string           // "Batam"
  provinsi?: string      // "Kepulauan Riau"
  kodepos?: string       // "29423"
}
```

### Display

```
Jl. Tanjung Pinang II No. 12
RT 03 / RW 05, Mukakuning, Batuaji
Batam, Kepulauan Riau 29423
```

### Batam-specific

Batam has industrial estate naming (Mukakuning, Tunas, Batamindo, Tanjung Uncang shipyard) commonly used instead of formal kelurahan. Address component should accept both:

```
Industrial Estate: Mukakuning
Block / Plot: B5 Plot 12
```

---

## 5. Phone Number Component

Indonesian format:
- Mobile: `+62 812-3456-7890` or `0812-3456-7890` local format
- WhatsApp identifier: international format mandatory
- Auto-validate Indonesian operator prefixes (0811, 0812, 0813, 0821, 0822, 0823, 0851, 0852, 0853 Telkomsel; 0814, 0815, 0816 etc. Indosat; etc.)
- Input formats accepted: with/without country code, with/without dashes

---

## 6. Container ID Component

UHF RFID + container EPC standard:
- ISO 6346 4-letter owner code + 6 digits + 1 check digit (e.g., `EISU 456789 0`)
- Display: `EISU456789` (no spaces) or `EISU 456 789` (formatted) — owner preference
- Auto-validate check digit
- QR code generation option (for sopir scan instead of manual entry)

---

## 7. Iconography Library

### Multi-ethnic safe principles

- Avatar icons: diverse skin tones, optional head coverings (peci, hijab, none)
- Hand gestures: avoid culturally specific (thumbs-up OK; OK-circle problematic in some contexts)
- Religious icons: use specifically (klenteng, masjid, gereja, pura, vihara — clear when intended) — never generic "religious building"

### Action icons (Indonesian context)

| Icon | Meaning | Notes |
|---|---|---|
| 🚛 | Truk container | preferred over generic truck |
| 📦 | Container | |
| 🅿️ | Yard / parking | |
| ⛽ | SPBU / fuel | |
| 📍 | Lokasi GPS | |
| 🎤 | Voice note | mandatory in driver app |
| 💬 | Chat / Asisten | |
| 🏮 | Imlek / Tionghoa cultural | use specifically |
| 🌙 | Eid / Islamic cultural | use specifically |
| ⚠️ | Anomali / attention | |
| 🚨 | Urgent / fraud confirmed | |
| ✓ | Done / clarified | |
| 🔧 | Maintenance | |

### Color semantics (refresher from `01-indonesian-mobile-ux-canon.md`)

- 🟢 OK / clarified
- 🟡 attention pending
- 🟠 anomaly detected soft
- 🔴 confirmed-anomaly urgent
- ⚪ neutral
- Tionghoa overlay: red + gold auspicious in owner-facing Imlek mode

---

## 8. Form Input Components

### Numeric Input (currency, liter, km)

- Right-aligned text (numeric convention)
- Auto-format separators
- Numeric keypad (no alphabet)
- Inline unit display: `1.250.000 (Rp)`, `50 L`, `45 km`

### Text Input

- Bahasa Indonesia placeholder examples
- Auto-capitalize sentence-first (Indonesian convention)
- Voice-note alternative button visible for long-text inputs (mandatory in driver app)

### Select / Dropdown

- Searchable (long lists like customer, sopir)
- Multi-language label if relevant (Indonesian + English technical term)
- "Lainnya..." option always last for free-form input

### Date Picker

- Indonesian month names
- Religious calendar badge overlay
- "Hari ini", "Kemarin", "Minggu lalu" quick options

---

## 9. Status Badge Component

Reusable badge for state machines (sopir clarification, AR aging, trip status):

```
┌────────────────┐
│ 🟡 Klarifikasi │
│    pending     │
└────────────────┘
```

Variants:
- Pill shape, small
- Color + icon + 1-line label
- Tap to drill-in (where applicable)

State libraries pre-defined for:
- Anti-fraud clarification (🟡 pending, 🟢 clarified-op, 🔧 sensor, 🟠 await-owner, 🔴 confirmed, ⚪ tolerated)
- Trip status (📅 scheduled, 🚛 in-transit, ✓ done, ⚠️ delayed, ❌ cancelled)
- AR aging (🟢 0-30, 🟡 31-60, 🟠 61-90, 🔴 91+)
- Coretax sync (✓ synced, ⏳ pending, ⚠️ retry, ❌ failed)

---

## 10. Toast / Snackbar Component

For transient confirmations + errors:

```
┌──────────────────────────────────────┐
│ ✓ Trip selesai, terima kasih Pak     │
│    Rohim                              │
└──────────────────────────────────────┘
```

Duration:
- Success: 3 sec
- Info: 4 sec
- Warning: 5 sec
- Error: 6 sec + action ("Coba Lagi" / "Lihat Detail")

Bahasa Indonesia copy always.

---

## 11. Empty State Component

Never show "No data" alone. Always include next action:

```
┌─────────────────────────────────────┐
│         📦                           │
│  Belum ada trip aktif hari ini       │
│                                      │
│  [ Buat Trip Manual ] (admin)        │
│  [ Cek Jadwal Besok ]                │
└─────────────────────────────────────┘
```

---

## 12. Cross-References

- Owner dashboard surface using these components → `03-owner-dashboard-ux.md`
- Driver app surface → `02-driver-app-ux-deep.md`
- Asisten chat using these patterns → `04-conversational-ux-canon.md`
- Tionghoa-specific component overlays (Imlek color etc.) → `09-tionghoa-batam-owner-overlay.md`
