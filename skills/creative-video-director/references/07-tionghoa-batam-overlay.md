# Reference 07 — Tionghoa Batam Owner Creative Overlay (Generic Archetype Template)

> **Purpose:** Reusable creative-direction overlay for **Tionghoa Batam SME owner archetype** — applied to video, carousel, pitch deck, LinkedIn output. This is a GENERIC ARCHETYPE TEMPLATE for any INDUSIA client matching the archetype, NOT bound to any specific person.
>
> **Project Variable Lock:** Skill operators inject project-specific values via `{{placeholder}}` syntax. For IRN project, see `D:/Projects/Indusia-AI-Logistik/project-variables.md` §1 for binding values.
>
> **Sister overlay (UX layer):** `senior-ux-architect-id/09-tionghoa-batam-owner-overlay.md`. This file = creative/narrative/casting/setting layer.

---

## 1. Archetype Definition

**Tionghoa-Indonesian SME owner, Batam region.** Sub-segment of Indonesian SME owner archetype with culturally specific overlays affecting creative direction:

- Tionghoa-Indonesian ethnic identity (Hokkien-Teochew majority in Batam vs Cantonese-Jakarta vs Hokkien-Hakka-Medan)
- Indonesian primary language; dialect (Hokkien / Mandarin / Teochew) varies — passive to active
- Religious calendar typically Buddhist / Confucian-Tridharma / Kristen — never default Muslim
- Family-business decision pattern (wife + senior anak/menantu involvement varies)
- Practical-skeptic to tech-positive-aspirational register range (project-bound)
- Batam-specific setting cues (kopitiam, shipyard, klenteng, industrial estate)

---

## 2. What This Overlay Adjusts vs Generic Indonesian Default

| Creative Surface | General Indonesian Default | Tionghoa Batam Overlay |
|---|---|---|
| **Cast — owner role** | Generic Indonesian male, age project-bound | Tionghoa-Indonesian male/female, age `{{owner_age}}`, regional Tionghoa phenotype |
| **Cast — supporting family** | Wife / kids generic | Wife (often "Cici"), anak/menantu role varies by `{{owner_age}}` (younger owner = anak kecil, family-of-origin more relevant; older owner = anak/menantu often IT-translator) |
| **Setting — establishing shot** | Office desk / warung | Kopitiam (morning), shipyard/pelabuhan office (day), klenteng accent (calendar mode) — bind via `{{owner_setting_preference}}` |
| **Wardrobe — owner** | Batik formal / corporate | Polo + slacks practical, simple watch, leather sandal Batam casual |
| **Wardrobe — wife** | Kebaya | Practical blouse + slacks; qipao only Imlek scenes |
| **Honorific in VO** | "Bapak" / "Pak" | `{{owner_honorific}}` — bound per project (options: "Pak [name]" / "Pak Tan" / "Cik" / "Koh" / "Pak [Indonesianized name]" / "Bapak") |
| **Religious-calendar content** | Eid / Ramadhan / Christmas | `{{owner_calendar_observed}}` — bound per project (typical options: Imlek, Cap Go Meh, Qingming, Vesak — subset varies by `{{owner_religion}}`) |
| **Music / sound** | Indonesian pop / corporate | Restrained instrumental (NO Chinese-stereotype erhu, NO orchestral bombast) — Batam ambient acceptable |
| **Color grade** | Warm neutral Indonesian | Industrial-desaturated + red-gold accent for calendar mode; neutral premium-practical default |
| **Decision-style pacing** | Multi-character musyawarah-mufakat | `{{owner_decision_style}}` — typical Tionghoa Batam = faster than Melayu/Jawa (consult-istri-singkat-then-decide common; multi-day deliberation rare) |
| **Anti-AI register** | Spiritual / aspirational | `{{owner_register}}` — range from practical-skeptic ROI-numerate to tech-positive aspirational (project-bound) |

---

## 3. Casting Specifications (Generic Template)

### Owner role

| Trait | Template Pattern | Project Binding |
|---|---|---|
| Age | Calibrate to `{{owner_age}}` ± 3 yr | `{{owner_age}}` |
| Phenotype | Match owner's specific regional Tionghoa (Hokkien-Teochew Batam vs Cantonese-Jakarta vs Hakka-Medan) | Default Batam = Hokkien-Teochew |
| Build | Mid-height, practical build — NOT athletic-corporate stock, NOT overweight stereotype | n/a |
| Hair | Style appropriate for age + role — younger millennial owner ≠ mature graying patriarch | Calibrate to `{{owner_age}}` |
| Face | Calm, mature, approachable — NOT stern stereotype, NOT excessively jolly | n/a |
| Voice (if on-camera) | Mid-tone, conviction over enthusiasm; light Batam-Tionghoa accent (subtle Hokkien cadence in Indonesian) | NOT Jakarta news-anchor, NOT thick Melayu Batam, NOT Mandarin-heavy |
| Wardrobe | Polo + slacks; simple Casio-tier watch; leather sandal Batam casual | Override per persona Q14 scene preference |

**Casting source pool (Batam):** Local Tionghoa community — klenteng community network, shipyard manager references, Batam-based casting agency.

**Anti-pattern:** Stock Asian-business-man image (looks Generic-Singapore or Generic-Mainland). Cast must be **specifically Batam Tionghoa-Indonesian SME owner** — practical, not glossy.

### Wife (often called "Cici" honorifically) role

| Trait | Template Pattern |
|---|---|
| Age | Similar age range to owner |
| Phenotype | Same regional Tionghoa as owner |
| On-camera role | Supporting decision-maker with informal financial veto power — NOT silent background, NOT dominant lead |
| Wardrobe | Practical blouse + slacks; qipao only Imlek scenes |

### Anak / Menantu role (calibrated to owner age)

If `{{owner_age}}` 50+: anak/menantu often 25-35, professional-modern, bilingual Indonesian + English, frequently serve as IT-translator gateway for tech adoption.

If `{{owner_age}}` 30-45: anak masih kecil (0-15) — typically NOT on-camera in B2B owner promo. Family-of-origin (saudara / adik / kakak) more relevant.

### Sopir / karyawan role (Indonesian general — separate archetype)

NOT this overlay's scope. See `02-archetype-routing-hooks.md` for Karyawan layer multi-ethnic routing (Melayu / Jawa / Batak / Minang / Bugis mix).

---

## 4. Honorific in Video / VO Copy

### Honorific routing template

```
{{vo_addressing_owner}}     = {{owner_honorific}} OR "Bapak" alternation
{{vo_referring_owner_3p}}   = "Pak {{owner_first_name}}" generic safe
{{onscreen_text_owner}}     = same as VO addressing
{{owner_to_sopir_oncam}}    = "Pak Sopir {{driver_name}}" / "Mas {{driver_name}}" downward respectful
{{sopir_to_owner_oncam}}    = "Pak" / "Bapak" / "Pak {{owner_first_name}}"
```

### Honorific options + override branches

| Option | When to Use | Cultural Register |
|---|---|---|
| **"Pak [family name]"** (e.g., "Pak Tan") | Traditional Hokkien-respectful, owner-confirms family name surfaceable | Most explicitly Tionghoa-marked |
| **"Pak [first name]"** (Indonesianized) | Owner prefers Indonesianized public-identity OR doesn't want family name surfaced | Indonesia-mass-market neutral |
| **"Cik"** | Hokkien older-respectful, often for senior owner | Tionghoa community signal |
| **"Koh"** | Hokkien older-brother respectful, casual-respectful | Tionghoa community signal |
| **"Bapak"** | Formal generic, safe across all sub-segments | Indonesian-formal |

**Project binding:** Set `{{owner_honorific}}` in `project-variables.md` and propagate consistently across all output.

---

## 5. Setting Library (Generic Batam Tionghoa Lifestyle)

Bind to `{{owner_setting_preference}}` per project.

### Morning beats (5:30-9:00 AM)

- **Kopitiam classic Batam** (Nagoya / Jodoh / Kampung Tua district) — owner with kopi-O sebelum kerja, HP at table, newspaper Batam Pos / Tribun Batam
- **Klenteng exterior** Cap Go Meh / Imlek week background only — NOT during prayer (respect)
- **Owner home** — neat practical Tionghoa middle-class — NOT mansion, NOT cramped, "kantor-sambil-rumah" feel

### Day beats (9:00-17:00)

- **Shipyard office Tanjung Uncang / Batu Ampar pelabuhan office** — practical ops view, container stack background
- **Batu Ampar terminal exterior** — establishing shot, container trucks visible
- **SPBU Mukakuning industrial estate** — fuel-fraud demonstration scene (if anti-fraud content)
- **Client headquarters / yard** — RFID gate, container stack, chassis line — owner walking through inspection

### Evening beats (17:00-21:00)

- **Owner home dining family** — wife (Cici) + anak/menantu present (if age-appropriate); HP at table for Asisten notification scene
- **Kopitiam evening Nagoya** — owner + business associate meeting
- **Industrial estate dusk** — last shift transition; ops still active

### Calendar-mode beats (overlay when `{{owner_calendar_observed}}` event active)

- **Klenteng during Imlek/Cap Go Meh/Vesak prayer** (respectful B-roll, no dialogue)
- **Owner home Imlek dinner** — red lanterns, golden touches, family reunion
- **Asisten WhatsApp scene with calendar mode active** — UI shows red-gold accent

### Anti-settings (general veto)

- ❌ Warung Padang / sate ambulant (Indonesian-general, wrong register)
- ❌ Masjid (wrong religion default for Tionghoa archetype)
- ❌ Jakarta-corporate skyscraper boardroom (wrong scale + register for Batam SME)
- ❌ Singapore-glossy lifestyle (Batam is practical, not aspirational-island-life)
- ❌ Mainland China factory / Cantonese restaurant (wrong Tionghoa subgroup)

---

## 6. Color + Music + Sound Design

### Default mode (non-calendar-event)

- **Color grade:** Industrial-desaturated, slight warm temperature, practical not glossy
- **Music:** Restrained instrumental — minimal piano / acoustic guitar / ambient pad. NO orchestral swell, NO electronic dance, NO erhu (Chinese-stereotype), NO dangdut (wrong register)
- **Sound design:** Batam ambient acceptable (container yard, ship horn distant, kopitiam kettle whistle) — sensory-specific authentic detail

### Calendar-event mode

Calendar-event modes activate when `{{owner_calendar_observed}}` event date is within ±3 days:

| Event | Visual Accent | Music | Pacing |
|---|---|---|---|
| **Imlek (Lunar New Year)** | Red-gold accent on key frames, retain industrial-practical base | Subtle Imlek motif — gong soft once at scene open, then back to restrained instrumental | Normal |
| **Cap Go Meh** | Lantern imagery subtle | Quieter, reflective | Slower cuts |
| **Qingming / Ceng Beng** | Muted palette, ancestral motif sparse | Quiet, reflective | Slower cuts |
| **Vesak / Waisak** | Lotus motif subtle, blue-gold | Soft instrumental | Reflective |

**Anti-pattern:** Over-do red — preserve UI legibility. Subtle accent, not theme-park.

### Anti-music (general veto)

- ❌ Bombastic orchestral ("epic logistics revolution")
- ❌ EDM / dubstep / trap (wrong register for Tionghoa SME demographic)
- ❌ Karaoke pop ballad
- ❌ Stereotype Chinese-tonal (erhu solo, pentatonic ostinato)
- ❌ Indonesian dangdut (wrong register for Tionghoa Batam)
- ❌ Foreign-corporate-uplift library music (Adobe Stock cliche)

---

## 7. 5-Beat Mapping with Tionghoa Overlay

Beat-by-beat applied (cross-reference `08-narrative-retention-framework.md`):

| Beat | Tionghoa Overlay Application |
|---|---|
| **HOOK** | Curiosity-gap with owner-name direct address: `{{owner_honorific}}, Anda tahu...?` — calm Tionghoa register tier (NOT shouty); register tone calibrated to `{{owner_register}}` (practical-skeptic loss-frame OR aspirational gain-frame) |
| **FORESHADOW** | Plant Peak with Tionghoa-practical promise — practical register: *"`{{owner_honorific}}` punya cara baru lihat ini — tanpa harus telpon sopir satu-satu."*; aspirational register: *"Lihat apa yang `{{owner_honorific}}` bisa lihat sekarang dari HP-nya."* |
| **BODY** | Demo scene at `{{owner_setting_preference}}` (kopitiam / shipyard office / owner home). Owner reaction shot Tionghoa register: quiet smile + nod, NOT broad enthusiastic gesture (unless `{{owner_register}}` aspirational and tone supports) |
| **PEAK** | Insight moment with Tionghoa-specific sensory anchor — HP getar saat owner di kopitiam pagi, Asisten reply lands; OR calendar-mode scene with red-gold accent reveal |
| **CTA** | Soft consultative + Tionghoa-respectful: *"`{{owner_honorific}}`, DM kami untuk diskusi — gratis, 30 menit."* — practical phrasing, no high-pressure |

---

## 8. Decision-Style Accommodation in Story Arc

Calibrate to `{{owner_decision_style}}` per project. Typical Tionghoa Batam SME options:

| `{{owner_decision_style}}` | Story-Arc Pacing |
|---|---|
| **Solo decide (fast)** | Single-frame decision OK — owner reads Asisten reply, single nod, taps Y |
| **Konsul istri singkat → decide** | Brief Cici reaction scene in BODY beat (Variant B story arc, see §9) |
| **Konsul anak/menantu IT (older owner)** | Anak/menantu visible in BODY explaining feature, owner ultimately decides |
| **Multi-step deliberation (data + analysis)** | Longer BODY with data-overlay scenes, owner asks probing question, decides |

### Anti-pattern in story arc

- ❌ Multi-character musyawarah scene with sopir + admin + accountant all discussing — wrong register for Tionghoa SME decision-making
- ❌ Owner waiting overnight to decide simple operational matter — slow pacing kills Peak

---

## 9. Story Arc Variants (Generic Templates)

Select variant per `{{gtm_protagonist}}` and `{{owner_age}}` and `{{owner_decision_style}}`:

### Variant A — Solo owner

Owner alone with Asisten. Family present in background but not active in decision. Default for short-form / vertical 60s. Best for younger owner with anak kecil.

### Variant B — Owner + Wife visible

Wife beside owner reading Asisten reply together; wife's reaction validates owner's decision. Used in family-pride Peak moments. Mid-length format (120s LinkedIn).

### Variant C — Owner + Anak/Menantu (IT translator)

Anak/menantu present, occasionally explains Asisten feature to owner; owner ultimately decides. Used for tech-adoption-friendly framing — useful when targeting next-gen-leadership Tionghoa family-business demographic. Best for older owner.

### Variant D — Owner + Family-of-Origin (adik/saudara)

For younger owner whose anak is kecil — family-of-origin (adik / kakak / cousin) serves the trusted-advisor role. May overlap with Variant E if `{{gtm_protagonist}}` is a family member.

### Variant E — Agency Advisor as Protagonist (NARRATOR-LED inversion)

When `{{gtm_protagonist}}` = agency-side advisor (e.g., agency founder who happens to be family member of owner), this advisor is the NARRATIVE PROTAGONIST and owner becomes supporting case-study. Bind:

```
{{gtm_protagonist}}   = agency founder / advisor (on-camera primary)
{{gtm_supporting}}    = owner (featured customer)
{{gtm_story_arc}}     = advisor narrates → owner appears in BODY/PEAK as proof
```

Best when trust-pre-built relationship between agency and owner is the narrative engine (e.g., family connection). Long-format pitch deck / founder story content. See sister doc on Speaker-Subject architecture.

### Variant F — Calendar-Event Family Scene

Calendar event (Imlek/Cap Go Meh/Vesak) dinner. Asisten silent (quiet mode active), owner + family together. ONE non-disruptive notification arrives (critical anomaly), owner glances HP, decides quickly, returns to family. Demonstrates respect-for-calendar feature. Specialized calendar-event content.

---

## 10. Cross-Format Application

This overlay applies to ALL creative formats. Format-specific notes:

### Video promo (any length)
- Full cast + setting + music application
- See §7 for 5-beat mapping
- LOCK-3 mandatory Asisten demo scene if `{{client_has_asisten_feature}}` = true (1+ in BODY)

### Pitch deck (cross-call to `pitch-deck-designer`)
- Slide imagery: morning kopitiam establishing, owner-with-Asisten-on-HP visual recurring
- Slide copy register matches `{{owner_register}}`
- Speaker notes: address procurement audience as separate layer (general-Indonesian register)

### Carousel (cross-call to `carousel-gen`)
- 7-10 slides
- Slide 1 (Hook): curiosity-gap question
- Slide 2-3 (Foreshadow): planted promise
- Slide 4-7 (BODY): 3-pillar demo
- Slide 8-9 (Peak): Insight moment / owner reaction
- Slide 10 (CTA): soft consultative

### LinkedIn (cross-call to `linkedin-post-writer`)
- Text register: Tionghoa-practical-but-public-respectful (LinkedIn = procurement-also-watching)
- Hashtag: avoid calendar-event-only hashtags unless within event week
- Image: kopitiam-morning OR owner-with-HP framing

### Article (cross-call to `article-content-writer`)
- Long-form respectful — Tionghoa SME readership comfortable with detail
- Indonesian-language primary, English technical terms inline
- Story arc Variant C (owner + anak/menantu IT framing) works well for adoption-narrative articles (older owner) — Variant E for founder-story content

---

## 11. Project Variable Bindings Reference

This overlay consumes the following `{{placeholders}}` — bind in project's `project-variables.md`:

| Variable | Description |
|---|---|
| `{{owner_name}}` | Display name in copy + casting |
| `{{owner_honorific}}` | Address form (Pak X / Cik / Koh / Bapak) |
| `{{owner_first_name}}` | First-name form for narrative reference |
| `{{owner_age}}` | Casting age + decision-style calibration |
| `{{owner_religion}}` | Calendar mode selection |
| `{{owner_language_secondary}}` | Code-switch decisions in VO |
| `{{owner_decision_style}}` | Story-arc pacing |
| `{{owner_register}}` | Hook + CTA tone calibration |
| `{{owner_calendar_observed}}` | Religious calendar event triggers |
| `{{owner_setting_preference}}` | Setting library selection |
| `{{gtm_protagonist}}` | Variant E inversion trigger |
| `{{gtm_supporting}}` | Variant E inversion supporting role |
| `{{client_has_asisten_feature}}` | Whether LOCK-3 Asisten demo scene applies |
| `{{driver_example_name}}` etc. | Illustrative anonymized supporting characters |

For IRN project: bindings at `D:/Projects/Indusia-AI-Logistik/project-variables.md` §1, §2.

---

## 12. Reusability for Other Clients

This overlay is **agency-level archetype template, not bound to any specific person or project**. Future INDUSIA clients matching Tionghoa Batam SME archetype reuse pattern with different bindings:

- **Tionghoa Batam SME owners** (any industry — manufacturing, food, distribution, marine, logistik) — full reuse
- **Tionghoa Jakarta SME owners** — adjust setting cues (Jakarta-specific districts), honorific dialect (Cantonese conventions), family name range
- **Tionghoa Surabaya / Medan / Pekanbaru SME owners** — regional dialect + setting variations
- **Indonesian Muslim SME owners** — DO NOT REUSE — different archetype entirely; needs separate overlay file

---

## 13. Cross-References

- 5-beat narrative scaffold → `08-narrative-retention-framework.md`
- Brand voice register options → `01-brand-voice-doctrine.md`
- Hook curiosity-gap pattern library → `02-archetype-routing-hooks.md`
- UX overlay (matched pair) → `senior-ux-architect-id/09-tionghoa-batam-owner-overlay.md`
- CEO AI Assistant architecture template → `smart-fleet-architect/11-ceo-ai-assistant-architecture.md`
- Anti-fraud-with-dignity cross-layer template → `smart-fleet-architect/10-anti-fraud-with-dignity.md`
- Family-business Tionghoa accounting patterns → `akuntan-indonesia-pro/10-family-business-tionghoa-context.md`
- Tacit Batam business culture → `pakar-logistik-batam/06-tacit-batam-context.md`
- **Project binding values (for IRN project specifically):** `D:/Projects/Indusia-AI-Logistik/project-variables.md`
