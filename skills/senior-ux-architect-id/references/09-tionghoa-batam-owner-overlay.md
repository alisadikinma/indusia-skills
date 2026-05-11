# Reference 09 — Tionghoa Batam Owner UX Overlay (Generic Archetype Template)

> **Purpose:** Reusable UX overlay for **Tionghoa Batam SME owner archetype** — applied to owner dashboard, CEO AI Assistant chat, family-business sub-account flows. Generic archetype template, NOT bound to specific person.
>
> **Project Variable Lock:** Skill operators inject project-specific values via `{{placeholder}}` syntax. For IRN project, see `D:/Projects/Indusia-AI-Logistik/project-variables.md` §1 for binding values.
>
> **Sister overlay (creative layer):** `creative-video-director/07-tionghoa-batam-overlay.md`. This file = UX/UI/conversational/calendar layer.

---

## 1. What This Overlay Adjusts vs Baseline

Overlay on top of `01-indonesian-mobile-ux-canon.md` (general Indonesian baseline) + `08-component-library-indonesian.md` (component primitives). Owner-facing surfaces only — karyawan-facing surfaces retain Indonesian-general baseline.

| Surface Dimension | General Indonesian Baseline | Tionghoa Batam Owner Overlay |
|---|---|---|
| Greeting honorific | "Bapak" / "Pak" | `{{owner_honorific}}` — bind per project (typical options: Pak [family name] / Pak [first name] / Cik / Koh / Bapak) |
| Religious calendar | Eid + Ramadhan + Christmas | `{{owner_calendar_observed}}` — typical Tionghoa options: Imlek + Cap Go Meh + Qingming + Vesak (subset varies by `{{owner_religion}}`) |
| Setting / wallpaper imagery | Multi-ethnic Indonesia | Kopitiam, shipyard, klenteng accent — Batam Tionghoa lifestyle (see creative overlay §5 setting library) |
| Color accent | Indonesian-safe palette | Red + gold auspicious accent available for calendar-event mode |
| Register / copy tone | Warm + face-saving balance | `{{owner_register}}` — range from practical-skeptic ROI-numerate to tech-positive aspirational |
| Response length default | Moderate | `{{owner_response_length}}` — range brief (1-3 lines) to mid (paragraph) to detailed |
| Data density tolerance | Moderate | Tionghoa archetype typically comfortable with HIGHER density (6-8 data points per card OK) — but glance-able invariant |
| Anti-fraud framing | Face-saving emphasis | Owner-side: direct on data; sopir-side: face-saving routed via Asisten clarification (see `06-anti-fraud-with-dignity-ux.md`) |
| Decision-style accommodation | Musyawarah-mufakat ritual | `{{owner_decision_style}}` — faster decision common; consult-wife pattern common; multi-day deliberation rare |
| Singapore family/business ties | Not assumed | `{{owner_singapore_ties}}` — bind per project (affects cross-border B2B positioning + dual-currency display feature) |
| Family-business sub-account | Not assumed | `{{owner_family_business_subaccounts}}` Phase 2 — istri / anak / menantu / family-of-origin advisor — varies by `{{owner_age}}` |

---

## 2. Color Mode — Calendar-Event Auspicious Overlay

When `asisten_owner_preference.religious_calendar` flagged event active (or 3-7 day window):

### Dashboard mode (calendar-event)

```
Background: subtle red gradient (#F4E4E4 → #F0F0F0) for Imlek/Cap Go Meh
            OR muted blue-gold for Vesak
            OR muted neutral for Qingming
Accent: gold (#D4AF37) for headlines + actionable
Card edges: subtle outline matching event palette
Greeting: includes event-specific greeting (e.g., "Selamat Imlek {{owner_honorific}} 🏮 Gong Xi Fa Cai")
```

### Asisten chat mode (calendar-event)

WhatsApp doesn't allow custom theming, but Asisten behavior shifts:
- Opens with event-specific greeting
- Profile pic temporary event-themed avatar (Phase 2)
- Reduced push frequency (quiet mode active)

### Off-mode reversion

3-7 days after event peak, revert to default Indonesian palette.

**Anti-pattern:** Over-do red — preserve UI legibility. Subtle accent, not Christmas-tree.

---

## 3. Greeting Patterns (Template — bind via `{{owner_honorific}}`)

| Time | Default | Calendar-event mode (`{{owner_calendar_observed}}` active) |
|---|---|---|
| 04:00-10:00 | "Selamat pagi `{{owner_honorific}}`" | "Selamat pagi `{{owner_honorific}}` 🏮" (Imlek) / 🪷 (Vesak) etc. |
| 10:00-15:00 | "Selamat siang `{{owner_honorific}}`" | + event icon |
| 15:00-18:00 | "Selamat sore `{{owner_honorific}}`" | + event icon |
| 18:00-04:00 | "Selamat malam `{{owner_honorific}}`" | + event icon |
| Imlek day 1 | (skip — event-specific) | "Gong Xi Fa Cai `{{owner_honorific}}`! 🏮 Sehat sejahtera tahun ini." |
| Cap Go Meh | (skip — event-specific) | "Selamat Cap Go Meh `{{owner_honorific}}` 🏮" |
| Qingming / Ceng Beng | (skip — event-specific) | "`{{owner_honorific}}`, hari ini Qingming. Saya schedule mode hormat ya." |
| Vesak / Waisak | (skip — event-specific) | "Selamat Hari Raya Waisak `{{owner_honorific}}` 🪷" |

### Hokkien greeting borrowed words (calibrate to `{{owner_language_secondary}}`)

OK to use Hokkien Imlek greetings if `{{owner_language_secondary}}` includes Hokkien aktif:
- "Gong Xi Fa Cai" (Mandarin standard, widely understood across Tionghoa)
- "Keng Hee Huat Tsai" (Hokkien — more authentic Batam dialect)

If `{{owner_language_secondary}}` = passive/none, default to Indonesianized "Selamat Imlek" only.

---

## 4. Response Tone Calibration (Asisten + Dashboard Copy)

Calibrate to `{{owner_register}}` per project.

### Register: Practical-skeptic ROI-numerate

```
Owner: "Cashflow gimana bulan ini?"

Asisten:
"{{owner_honorific}}, bulan ini (per [date]):
• Revenue: Rp 287 jt (+8% vs April)
• AP due: Rp 198 jt
• Net cashflow estimate: +Rp 52 jt
• AR aging 60+: Rp 145 jt (PT [customer])

[customer] {{owner_honorific}} mau saya follow-up?"
```

Brief, numeric, specific, actionable. Pak / Bapak addresses keep it respectful.

### Register: Tech-positive aspirational

```
Owner: "Cashflow gimana bulan ini?"

Asisten:
"{{owner_honorific}}, bulan ini lumayan menarik:
• Revenue +8% naik dari April — Rp 287 jt
• Cashflow net ~Rp 52 jt
• Yang perlu perhatian: AR aging 60+ ada Rp 145 jt — PT [customer] paling lama (73 hari)

Mau saya rancang plan collection-nya? Saya bisa kirim reminder otomatis."
```

Lebih engaged, exploratory tone, still grounded in numbers. Adapted to `{{owner_response_length}}` = mid (paragraph form).

### Anti-register (avoid regardless of `{{owner_register}}`)

```
"Pak yang terhormat, kabar baik! Bulan Mei ini PT [client] menunjukkan 
perkembangan finansial yang menggembirakan. Revenue Bapak meningkat sebesar 
8% dibandingkan bulan April, yang mencerminkan kinerja operasional yang 
sangat baik. Saya senang melaporkan bahwa..."
```

❌ Verbose, fluff, no specific numbers, sounds insincere. Owner dismisses.

---

## 5. Data Density Calibration

Tionghoa Batam SME owners typically comfortable with **6-8 data points per glance card** (higher than Indonesian-general default 4-6) — IF visually grouped + each glance-able.

### Example density (acceptable for Tionghoa overlay)

```
FLEET HARI INI ([date])
🚛 {{fleet_active}}/{{fleet_total}} aktif  📦 {{container_in_transit}} in-transit  🅿️ {{yard_count}} yard
⏱ {{trips_done}} done · {{trips_in_progress}} in-progress · {{trips_cancelled}} cancelled
💰 Revenue est: {{revenue_today}} ({{revenue_trend_vs_avg}})
⚠️ {{anomaly_count}} anomali pending review
```

8 data points, visually grouped sub-rows. Acceptable.

**Anti-pattern:** Bulk 20+ KPI corporate-style — even Tionghoa owner tunes out beyond 8-10 per card.

---

## 6. Decision-Style Accommodation (UX Implications)

Bind to `{{owner_decision_style}}`. Action prompt patterns vary:

### Solo-decide fast

```
Asisten: "{{owner_honorific}}, saya tahan invoice fuel {{vehicle_example_id}} (Rp 850k)? Y/N"
```

Single-question, fast decision path.

### Konsul-istri-singkat

```
Asisten: "{{owner_honorific}}, ada anomali fuel {{vehicle_example_id}} Rp 850k. 
Mau saya tahan invoice atau diskusi dengan {{wife_honorific}} dulu? Y/N/D"
```

Optional spouse-loop. Variant B story arc echo.

### Konsul anak/menantu IT (older owner)

```
Asisten: "{{owner_honorific}}, mau saya alokasi Rp 65jt untuk Phase 2 hardware? 
{{anak_honorific}} sudah review proposal, dia approve. 
Mau diskusi dengan {{wife_honorific}} dulu, atau saya proceed?"
```

Family-business decision-loop respected.

### Multi-step deliberation (data + analysis)

```
Asisten: "{{owner_honorific}}, untuk keputusan Rp 65jt ini saya susun briefing:
[summary]
[3 alternative options with cost/risk]
[recommended option dengan justifikasi]
Mau saya jadwalkan diskusi lengkap besok, atau decide sekarang?"
```

---

## 7. Family-Business Sub-Account (Phase 2 — Calibrate to `{{owner_age}}` + `{{owner_family_business_subaccounts}}`)

Sub-account roles depend on owner age and family structure. Template:

| Role | Typical Older Owner (50+) | Typical Younger Owner (30-45) | Honorific |
|---|---|---|---|
| Wife (Cici) | Finance read + religious-calendar set | Same | "Cik" / "Ibu" / `{{wife_honorific}}` |
| Anak / Menantu (IT translator) | Full read + tech ops + escalation — IT-translator gateway | N/A (anak kecil) | "Pak / Mas [name]" |
| Adik / Saudara / Cousin | N/A | Trusted-advisor role (replaces anak/menantu IT for younger owner) | "Pak / Mas / Mbak" |
| Agency-side advisor (Variant E `{{gtm_protagonist}}` if family-connected) | Cross-account architect view | Same | "Pak [advisor name]" |

**Asisten honorific awareness:** when responding to non-owner family-member sub-account, switch honorific accordingly. Configure via `asisten_owner_preference.family_subaccount_map`.

**Anti-pattern:** Single shared family-account login. Each family member has own sub-account with audit trail of which person did what.

---

## 8. Singapore Cross-Border Touch (Conditional)

If `{{owner_singapore_ties}}` = "family" or "business" or "both":

- Currency: dual display option Rp + SGD in pitch material, owner dashboard
- Customs context: cross-border container flow Batam ↔ Singapore aware
- Time-zone: same WIB, but ferry schedule aware (e.g., Batam Centre ↔ HarbourFront)
- B2B network: leverage cross-border B2B credibility — pitch positioning

If `{{owner_singapore_ties}}` = "none", omit this surface entirely.

→ Phase 2 nice-to-have feature: SGD-aware finance view (only if `{{owner_singapore_ties}}` non-null).

---

## 9. What This Overlay Does NOT Change

These remain Indonesian-baseline regardless of Tionghoa overlay:
- Driver app UX (sopir layer, Indonesian general — see `02-driver-app-ux-deep.md`)
- Customer-facing tracking app (mixed B2B audience)
- Admin app (admin = Indonesian general workforce)
- Anti-fraud workflow Stage 2 sopir clarification (face-saving framing mandatory — see `06-anti-fraud-with-dignity-ux.md`)
- Underlying component primitives (currency, date, phone — see `08-component-library-indonesian.md`)
- Accessibility / multi-ethnic icon library

**Owner-layer Tionghoa overlay is overlay-only on owner-direct surfaces.** Karyawan-facing surfaces stay Indonesian-general.

---

## 10. Project Variable Bindings Reference

This overlay consumes the following `{{placeholders}}` — bind in project's `project-variables.md`:

| Variable | Description |
|---|---|
| `{{owner_honorific}}` | Address form in Asisten + dashboard greeting |
| `{{owner_first_name}}` | Optional first-name form for casual context |
| `{{owner_religion}}` | Calendar mode selection (subset of Tionghoa calendar events) |
| `{{owner_calendar_observed}}` | Specific event triggers for calendar-event mode |
| `{{owner_language_secondary}}` | Hokkien/Mandarin/Teochew code-switch decision in greeting |
| `{{owner_register}}` | Asisten + dashboard copy tone calibration |
| `{{owner_response_length}}` | Asisten reply length default |
| `{{owner_decision_style}}` | Action prompt pattern selection |
| `{{owner_singapore_ties}}` | Singapore cross-border feature enable |
| `{{owner_age}}` | Sub-account role calibration |
| `{{owner_family_business_subaccounts}}` | Phase 2 sub-account configuration |
| `{{wife_honorific}}` | Sub-account honorific (typically "Cik" / "Ibu") |
| `{{anak_honorific}}` | Sub-account honorific for IT-translator anak/menantu |

For IRN project: bindings at `D:/Projects/Indusia-AI-Logistik/project-variables.md` §1, §3.

---

## 11. Reusability for Other Tionghoa SME Clients

Reuse pattern across INDUSIA agency clients:

- **Tionghoa Batam / Jakarta / Surabaya / Medan SME** — full reuse with minor regional adjustments
- **Tionghoa Indonesian PMA (foreign-investment)** — additional TP Doc / transfer pricing layer; this overlay as secondary
- **Indonesian Muslim family-business SME** — DO NOT REUSE — different cultural / accounting / calendar / honorific conventions. Needs separate Muslim-family-business overlay file.

---

## 12. Cross-References

- 2-layer cultural model + 7-gap owner visibility pattern → `01-indonesian-mobile-ux-canon.md` baseline
- Owner dashboard generic UX → `03-owner-dashboard-ux.md`
- Conversational UX canon (Asisten chat) → `04-conversational-ux-canon.md`
- Voice UX low-cognitive-load Phase 2 → `05-voice-ux-low-cognitive-load.md`
- Anti-fraud-with-dignity cross-layer → `06-anti-fraud-with-dignity-ux.md` + `smart-fleet-architect/10-anti-fraud-with-dignity.md`
- Component library Indonesian primitives → `08-component-library-indonesian.md`
- Creative overlay matched pair → `creative-video-director/07-tionghoa-batam-overlay.md`
- Family-business Tionghoa accounting patterns → `akuntan-indonesia-pro/10-family-business-tionghoa-context.md`
- CEO AI Assistant architecture template → `smart-fleet-architect/11-ceo-ai-assistant-architecture.md`
- **Project binding values (for IRN project specifically):** `D:/Projects/Indusia-AI-Logistik/project-variables.md`
