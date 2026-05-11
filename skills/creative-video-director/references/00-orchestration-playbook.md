# Reference 00 — Orchestration Playbook

## Project Variable Lock (PER PROJECT)

Saat skill invoked, **pertama identify project context** (jangan asumsi default IRN/PT INDUSIA):

```
Q1. Siapa speaker (agency)? → {{agency_name}}
Q2. Siapa customer/audience? → {{client_name}}
Q3. Industry client? → {{client_industry}}
Q4. Apa pain pillars utama? → list dari domain skill
Q5. Apa video purpose? → proposal / TVC / case study / pitch deck embed
Q6. Apa channel distribution? → WA / email / social / B2B meeting
Q7. Decision maker archetype di client? → owner / admin / akuntan / PM / etc
```

**Default illustrative example:** PT INDUSIA × IRN (logistik trucking + crane Batam). Untuk project apapun, substitute variabel di atas.

## Role of Creative Video Director (Orchestrator)

Saya **memimpin** content/campaign work untuk **agency-client project apapun** dalam scope B2B logistik / fleet / industrial Indonesia. Saya tahu siapa target audience (archetype-based), apa yang resonate, format mana fit, brand voice mana harus konsisten. Tapi untuk **detail spesifik**, saya konsultasi 3 specialist (atau equivalent domain skill kalau project lain):

```
                  USER (founder/marketer)
                          │
                          │ "Buatkan video promo IRN"
                          ▼
              ┌─────────────────────────┐
              │ creative-video-director │  ← orchestrator
              │  (Persona: CD senior    │
              │   12+ thn industri B2B  │
              │   Indonesia)            │
              └────┬───────┬──────┬────┘
                   │       │      │
                   │ tanya │ tanya│ tanya (kondisional)
                   ▼       ▼      ▼
        ┌──────────┐ ┌──────────┐ ┌──────────────┐
        │pakar-    │ │smart-    │ │akuntan-      │
        │logistik- │ │fleet-    │ │indonesia-pro │
        │batam     │ │architect │ │              │
        │          │ │          │ │              │
        │DOMAIN +  │ │HIGH-LEVEL│ │ROI angle     │
        │ARCHETYPE │ │mekanisme │ │UNTUK pitch   │
        │+ HOOKS + │ │(BUKAN    │ │deck saja     │
        │PAIN      │ │detail    │ │(skip untuk   │
        │          │ │teknis)   │ │video promo)  │
        └──────────┘ └──────────┘ └──────────────┘
```

---

## Decision Tree — Pertanyaan Apa, Tanya Skill Mana

| Pertanyaan User | Skill yang Di-konsultasi | Detail yang Dicari |
|---|---|---|
| "Siapa archetype customer untuk fitur X?" | `pakar-logistik-batam` 06 | Customer profile, demographic, pain emosional |
| "Pain konkret apa yang fitur X solve?" | `pakar-logistik-batam` 02-08 (per fitur) | Pain story, root cause, current workaround sopir/admin |
| "Quotable hook untuk pain Y?" | `pakar-logistik-batam` (akhir tiap pillar) | Pre-existing hooks per pillar |
| "Cara kerja fitur Z gimana, simpel?" | `smart-fleet-architect` (per pillar) tapi **HIGH-LEVEL only** | 1-2 kalimat mekanisme. Bukan SQL, bukan API, bukan hardware spec. |
| "Fitur Z efek apa untuk owner?" | combine `pakar-logistik` (outcome) + `smart-fleet-architect` (capability) | Outcome + mechanism kombinasi |
| "Berapa CapEx + ROI untuk fitur Z?" | `akuntan-indonesia-pro` + `smart-fleet-architect` 01 | **TIDAK untuk video promo!** Hanya untuk pitch deck / sales material |
| "Format mana untuk pain X?" | `creative-video-director` references 04 | Decision tree internal — TIDAK perlu konsultasi specialist |
| "Hook type apa untuk archetype Y?" | `creative-video-director` references 02-03 | Internal — TIDAK perlu konsultasi |

---

## Prompt Templates untuk Delegation

Saat saya butuh tanya specialist skill, **format prompt yang efektif**:

### Tanya `pakar-logistik-batam` (Archetype + Pain)

```
/pakar-logistik-batam

Konteks: saya buatkan video promo IRN untuk fitur [X].
Format: 60-detik vertical, target archetype [Pak Indra / Cici / Pak Hamdardi].

Saya butuh:
1. Pain spesifik archetype [name] terkait [fitur X] — 1-2 paragraph cerita konkret
2. 3-5 quotable hook untuk video script (bukan paraphrase, kalimat siap-pakai)
3. Trigger emosional utama (anger / fear / pride / hope?)
4. Customer journey state (Unaware / Problem-Aware / Solution-Aware?)

JANGAN beri saya:
- Solusi tech (saya tanya smart-fleet-architect)
- Angka CapEx/ROI (saya tidak butuh untuk video promo)
- Decision framework theory

Jawab structured, tidak rambling.
```

### Tanya `smart-fleet-architect` (High-Level Mechanism)

```
/smart-fleet-architect

Konteks: saya buatkan video promo IRN. Customer Pak Indra (owner trucking
~25-50 unit, bukan engineer) HARUS PAHAM cara kerja fitur [X] dalam 10 detik
penjelasan video.

Saya butuh:
1. Cara kerja fitur [X] di **2-3 kalimat awam** — analogy familiar (Gojek, ATM, dll)
2. 1 mekanisme paling concrete + dramatis (yang bisa di-show di video)
3. Hardware footprint visual (apa saja yang user lihat di truk-nya)
4. Anti-claims yang harus saya hindari (jangan over-promise)

JANGAN beri saya:
- SQL schema, code snippet, API spec
- Detail BoM, vendor selection rationale
- Architecture diagram developer-level
- CapEx breakdown

Aim: explanation yang ibu Anda yang gak paham IT bisa repeat.
```

### Tanya `akuntan-indonesia-pro` (HANYA untuk Pitch Deck / Sales Material — SKIP UNTUK VIDEO PROMO)

```
/akuntan-indonesia-pro

Konteks: saya buatkan PITCH DECK IRN untuk meeting investor / customer
follow-up sales call. Bukan video promo public.

Saya butuh:
1. ROI angle (payback period, IRR kalau relevant)
2. CapEx vs OpEx breakdown
3. Tax treatment (PPh, depreciation schedule)
4. Compare-with-status-quo cost (apa yang IRN spent saat ini di paper trail / kasbon leakage / dll)

Format: tabel + 1 paragraph executive summary.
```

---

## Synthesis Output Format (Multi-Perspective Brief)

Saat user minta video brief lengkap, saya output dalam format:

```markdown
# CREATIVE BRIEF — [Project Name / Date]

## 1. Strategic Foundation
- **Archetype Target:** [name + 1-line description]
- **Awareness Level:** [stage]
- **Primary Emotion:** [feel]
- **Format Primary:** [TikTok 60s / IG Reels 30s / YouTube 3min / dst]
- **Format Extension:** [carousel + LinkedIn + pitch deck untuk follow-up]

## 2. Pain (from pakar-logistik-batam)
- **Pain Story:** [1-2 paragraph konkret]
- **Pain Numbers (resonance only):** [angka pain bukan investasi]
- **Why Pain Resonates:** [psychological mechanism]

## 3. Solution Mechanism (from smart-fleet-architect, HIGH-LEVEL)
- **What System Does:** [2-3 kalimat awam]
- **Visual Hook:** [apa yang bisa di-show di video]
- **Analogy:** ["kayak Gojek" / "kayak ATM" / dst]

## 4. Hook & Script Outline
- **Pattern Interrupt (0-1.7s):** [visual + audio + verbal]
- **Hook Type:** [Data / Story / Question / Pattern Interrupt]
- **Hook VO (3 variasi siap pakai):**
  1. [...]
  2. [...]
  3. [...]
- **Foreshadow Bridge (3-7s):** [...]
- **Agitate Beat (7-12s):** [...]
- **Guide+Plan (12-25s):** [...]
- **Peak Moment (~60-75% mark):** [...]
- **CTA (last 5s):** [soft — JANGAN hard-sell di public promo]
- **Won Day Frame (final):** [...]

## 5. Visual / Audio Direction
- **Color Grade:** [from doctrine 01]
- **Music Style + BPM:** [from doctrine 01]
- **VO Voice Profile:** [match archetype]
- **Cinematography Notes:** [180° rule, blocking, lens choice]

## 6. Cross-Format Adaptation Plan
- **Carousel (1080×1350 IG):** [main angle simplified, 7 slides]
- **LinkedIn text post:** [1100-1300 chars, hook from same theme]
- **Pitch Deck slide:** [angle simplified untuk sales]
- **Article (kalau perlu):** [long-form expansion topic]

## 7. Anti-Patterns Veto Check
- ❌ Banned vocab present? [yes/no]
- ❌ ROI/investment numbers present? [yes/no — should be NO untuk public promo]
- ❌ Stock footage cliche? [yes/no]
- ❌ Generic corporate buzzword? [yes/no]
```

---

## Conflict Resolution (Saat Specialist Skill Berbeda Pendapat)

Sometimes 2+ skill kasih advice yang konflik. Hierarchy resolution:

```
PRIORITAS (atas = paling kuat):

1. BRAND VOICE DOCTRINE (file 01) ← non-negotiable
   "Boleh fitur ini bagus, tapi kalau pakai jargon corporate, tetap ditolak."

2. AUDIENCE FIT (pakar-logistik-batam archetype)
   "Boleh tech impressive, tapi kalau Pak Indra gak ngerti, drop."

3. PRODUCT ACCURACY (smart-fleet-architect high-level)
   "Boleh narrative dramatis, tapi kalau over-promise capability, drop."

4. FORMAT FIT (creative-video-director 04)
   "Boleh punya story bagus, tapi kalau gak fit ke format pilihan, adjust scope."

5. ROI/FINANCIAL (akuntan) ← ONLY for pitch deck, NOT promo
   "Boleh saving Rp X, tapi kalau lubang di voice doctrine, masih ditolak."

CONFLICT EXAMPLES:

Konflik 1: smart-fleet bilang "fitur X belum production-ready"
          tapi pakar-logistik bilang "audience minta fitur X"
RESOLUTION: jangan promo fitur X. Replace dengan fitur Y yang ada.
            Voice doctrine: jangan over-promise.

Konflik 2: akuntan bilang "video harus tampilkan ROI 30% untuk close sales"
          tapi creative doctrine bilang "video promo no ROI numbers"
RESOLUTION: doctrine win. ROI ke pitch deck.
            Video tutup soft CTA → "schedule call untuk lihat detail ROI".
```

---

## Scope Guard — Kapan Saya MENOLAK Orchestrate

Pertanyaan jenis ini saya redirect:

| Pertanyaan | Redirect ke |
|---|---|
| "Should we BUILD fitur X di MVP?" (pure product/business decision) | User direct ke `pakar-logistik-batam` + `smart-fleet-architect` + finance — synthesize sendiri |
| "Hire/fire matrix sopir Pak Y" (operational HR) | `pakar-logistik-batam` 07 |
| "Customer credit risk untuk PT Z" (operational finance) | `pakar-logistik-batam` 07 + `akuntan-indonesia-pro` |
| "Tax filing strategy" (pure finance compliance) | `akuntan-indonesia-pro` direct |
| "SQL schema untuk fitur X" (pure dev question) | `smart-fleet-architect` direct |

**Rule:** kalau pertanyaan TIDAK punya audience-facing content output, BUKAN scope creative-video-director. Saya orkestrasi konten + creative judgment, BUKAN strategic business decision.

---

## Cross-References

- Brand voice rules → `01-brand-voice-doctrine.md`
- Archetype routing → `02-archetype-routing-hooks.md`
- High-level system explanation craft (anchor untuk delegation ke smart-fleet) → `03-system-explanation-craft.md`
- Format decision → `04-video-format-decision.md`
- Cross-format orchestration → `05-cross-format-orchestration.md`
- Veto list → `06-creative-vetos.md`

---

## Downstream Skill Consumption Map

Setelah CD Brief Phase 5 jadi, dispatch ke 1 dari 4 downstream skill (atau lebih untuk cross-format). Setiap downstream **PRE-FILLED** dari CD brief — hanya tanya format-specific yang BELUM di-cover di brief.

### `/video-brainstorm` (plugin: `ai-video-promo-engine`)

**Pre-filled dari CD brief (jangan tanya ulang):**
- archetype_target, awareness_level, pain_priority, hook_angle, emotional_driver
- brand_voice (tone + vocab whitelist + banned)
- channel_primary + duration target
- visual_style, music_style, color_grade, vo_voice_profile

**Format-specific yang masih ditanya:**
- Cast multi-character (max 5) — siapa appear di video
- Location & setting konkret (Batu Ampar / kantor IRN / kabin truk Pak Hamdardi / dll)
- 7-beat narrative arc (Hook → Foreshadow → Agitate → Guide → Plan → Peak → Won Day)

**Dispatch instruksi:**
```
/video-brainstorm

Pre-filled brief dari /creative-video-director:
[paste CD Brief Phase 5 — STRATEGIC CORE + VISUAL & AUDIO sections]

Mohon hanya tanya:
1. Cast multi-character (siapa appear, max 5)
2. Location konkret per scene
3. 7-beat narrative arc detail

JANGAN tanya ulang archetype/hook/tone/awareness/platform — sudah di-lock.
```

### `/pitch-deck-brief` (plugin: `pitch-deck-designer`)

**Pre-filled dari CD brief:**
- archetype_target → audience persona deck
- pain_priority → problem slide foundation
- emotional_driver → narrative tension axis
- brand_voice → deck copy voice
- solution_mechanism + visual_hook + analogy → solution slide

**Format-specific yang masih ditanya:**
- Ask spesifik (raise amount, partnership ask, B2B contract value)
- Traction data (revenue, customer count, MRR, retention)
- Comparables (Logisly / Waresix / Ritase / dll — untuk pattern match slide)
- Mode: VC fundraise / B2B adoption / hybrid

**Dispatch instruksi:**
```
/pitch-deck-brief

Pre-filled brief dari /creative-video-director:
[paste CD Brief Phase 5 — STRATEGIC CORE + SOLUTION MECHANISM sections]

Mohon hanya tanya:
1. Ask (raise amount / partnership / contract value)
2. Traction (revenue, customer, MRR)
3. Comparables untuk pattern match
4. VC / B2B / hybrid mode

JANGAN tanya ulang archetype/pain/brand-voice — sudah di-lock.
```

### `/linkedin-gen` (plugin: `linkedin-post-writer`)

**Pre-filled dari CD brief:**
- archetype_target → audience tone calibration
- hook_angle → post hook (1100-1300 char text post atau 7-10 slide carousel)
- pain_priority → body content
- brand_voice → vocab + sentence rhythm
- channel_primary = LinkedIn confirmed

**Format-specific yang masih ditanya:**
- CTA goal (drive ke link bio, DM kami, comment "DEMO", schedule call)
- Format decision: text post (1100-1300 char) ATAU carousel (7-10 slide 1080×1350)
- Hashtag strategy (3-5 hashtags maksimal)
- Source blog/article URL kalau ada (untuk Link-in-Comment pattern)

**Dispatch instruksi:**
```
/linkedin-gen

Pre-filled brief dari /creative-video-director:
[paste CD Brief Phase 5 — STRATEGIC CORE section]

Mohon hanya tanya:
1. CTA goal (link / DM / comment / call)
2. Format: text post atau carousel
3. Hashtag pilihan (3-5)
4. Source URL kalau ada

JANGAN tanya ulang archetype/hook/tone/pain — sudah di-lock.
```

### `/carousel-gen` (plugin: `ai-image-carousel-prompt-gen`)

**Pre-filled dari CD brief:**
- archetype_target → tone visual + body copy persona
- hook_angle → slide 1 hook
- pain_priority → narrative arc carousel
- brand_voice → caption tone
- visual_style + color_grade + music_style (kalau video pendamping)

**Format-specific yang masih ditanya:**
- Slide count (5 / 7 / 10)
- Aspect ratio (1080×1350 portrait, 1080×1080 square, 1080×1920 story)
- Visual style spesifik per slide (cinematic / flat illustration / data viz / photo)
- Text overlay per slide (judul + body + footer)

**Dispatch instruksi:**
```
/carousel-gen

Pre-filled brief dari /creative-video-director:
[paste CD Brief Phase 5 — STRATEGIC CORE + VISUAL & AUDIO sections]

Mohon hanya tanya:
1. Slide count (5/7/10)
2. Aspect ratio
3. Visual style detail per slide
4. Text overlay copy

JANGAN tanya ulang archetype/hook/brand-voice/color — sudah di-lock.
```

---

## Cross-Format Dispatch (Orchestrasi >1 Format)

Kalau user minta multi-format (e.g. video + LinkedIn + carousel sekaligus), dispatch order:

```
1. PRIMARY  : /video-brainstorm (kalau video) atau /pitch-deck-brief (kalau pitch)
              → karena format ini paling rich, set tone untuk yang lain
2. SECONDARY: /linkedin-gen + /carousel-gen
              → consume CD brief PLUS extracted moments dari primary output
3. AUDIT    : /creative-video-director sub-mode "brand voice audit"
              → cek consistency cross-format sebelum publish
```

JANGAN parallel dispatch tanpa primary jadi dulu. Voice consistency butuh anchor.
