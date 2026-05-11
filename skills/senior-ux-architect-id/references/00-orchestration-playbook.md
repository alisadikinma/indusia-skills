# Reference 00 — Orchestration Playbook

> **Purpose:** Help you (the skill operator) pick the right reference combination per UX task. Don't read all 10 sequentially — they're addressable, not linear.

---

## Task → Reference Combination Map

| Task | Primary Refs | Secondary Refs | Cross-Skill |
|---|---|---|---|
| **Design sopir driver app screen** | `02-driver-app-ux-deep` | `01-indonesian-mobile-ux-canon`, `08-component-library-indonesian` | `smart-fleet-architect/03-driver-mobile-app-architecture` |
| **Design owner dashboard HP-first** | `03-owner-dashboard-ux` | `09-tionghoa-batam-owner-overlay`, `08-component-library-indonesian` | `pakar-logistik-batam/09-product-feature-map` |
| **Design Asisten SME owner chat** | `04-conversational-ux-canon` | `09-tionghoa-batam-owner-overlay` | `smart-fleet-architect/11-ceo-ai-assistant-architecture` |
| **Design anti-fraud sopir clarification UI** | `06-anti-fraud-with-dignity-ux` | `02-driver-app-ux-deep`, `04-conversational-ux-canon` | `smart-fleet-architect/10-anti-fraud-with-dignity` |
| **Design onboarding flow** | `07-onboarding-zero-training` | `01-indonesian-mobile-ux-canon`, `04-conversational-ux-canon` | — |
| **Voice IO design (Phase 2)** | `05-voice-ux-low-cognitive-load` | `04-conversational-ux-canon` | `smart-fleet-architect/11-ceo-ai-assistant-architecture` Section 3 |
| **Component / iconography / typography decision** | `08-component-library-indonesian` | `09-tionghoa-batam-owner-overlay` | — |
| **Cultural register calibration (owner vs karyawan)** | `09-tionghoa-batam-owner-overlay` | `01-indonesian-mobile-ux-canon` | `creative-video-director/01-brand-voice-doctrine` |

---

## Standard Output Template

Every UX deliverable from this skill must include:

```
## [Surface name] — [Owner Visibility Gap N — short description]

**Layer:** Owner-Tionghoa / Karyawan-Indonesian / Both
**Pillar mapping:** AI / IoT / Mobile (one or more)
**Owner Gap covered:** [1-7 list]
**Zero-training verdict:** PASS / FAIL (+ rationale)

### Wireframe / Component Spec
[ASCII wireframe OR component composition]

### Copy Deck (Bahasa Indonesia default)
- [State 1]: "..."
- [State 2]: "..."
- [Error]: "..." (face-saving framing)

### Cultural Friction Resolution
[If cross-layer: how does this surface handle Tionghoa-Indonesian register mismatch?]

### Interaction Notes
[Tap targets, voice-note placement, offline behavior, etc.]

### Open Questions (TBD — pending persona validation / WS7)
[List specific persona-dependent decisions deferred]
```

---

## When This Skill Is the Primary Driver (vs Supporting)

**Primary driver:**
- User asks "design X UX" / "wireframe X" / "UX canon for X"
- User asks Asisten conversational message structure / honorific decision
- User asks driver app screen specific behavior

**Supporting (called by other skills):**
- `creative-video-director` invokes for video scene UI demo accuracy (e.g., WhatsApp screen rendering for Asisten demo scene mandate)
- `smart-fleet-architect` invokes for cross-validating data model surfaces vs UX viability
- `pakar-logistik-batam` invokes for feature roadmap → UX feasibility check

**NOT this skill's job:**
- System architecture / backend / API design → `smart-fleet-architect`
- Brand voice / video scripting / pitch narrative → `creative-video-director`
- Domain pain articulation → `pakar-logistik-batam`
- Accounting / tax → `akuntan-indonesia-pro`

---

## How to Chain with Other Skills

### Driver app development flow

```
1. pakar-logistik-batam/05-driver-control-antifraud   ← sopir psychology, sanction matrix
2. smart-fleet-architect/03-driver-mobile-app-architecture   ← tech stack, offline buffer, BLE
3. senior-ux-architect-id/02-driver-app-ux-deep      ← THIS — UX wireframes + copy
4. gaspol-dev (design → plan → execute)              ← implementation
```

### CEO AI Assistant flow (LOCK-3 flagship)

```
1. pakar-logistik-batam/09-product-feature-map       ← owner query taxonomy
2. smart-fleet-architect/11-ceo-ai-assistant-architecture   ← LLM + RAG + WhatsApp + voice gateway
3. senior-ux-architect-id/04-conversational-ux-canon ← THIS — message structure + honorific + permission-escalation
4. senior-ux-architect-id/05-voice-ux-low-cognitive-load   ← Phase 2 voice
5. akuntan-indonesia-pro/06-ppn-efaktur-coretax       ← audit trail integration
6. gaspol-dev
```

### Anti-fraud-with-dignity workflow flow

```
1. smart-fleet-architect/05-anti-fraud-tech-stack     ← detection
2. smart-fleet-architect/10-anti-fraud-with-dignity   ← workflow 3-stage
3. senior-ux-architect-id/06-anti-fraud-with-dignity-ux   ← THIS — sopir + owner UI wireframes
4. creative-video-director/01-brand-voice-doctrine   ← anti-fraud framing rules for any video promo
```

---

## Quick-Reference Constraints Cheat Sheet

| Constraint | Value | Source |
|---|---|---|
| Default screen size | 6-7 inch Android | Indonesian market reality |
| Default RAM | 2-3 GB | Indonesian Android demographic |
| Network | 4G Telkomsel >97% Batam, intermittent peaks | WS8 LOCK |
| Offline buffer | 5-30 min driver app | CLAUDE.md LOCK |
| Currency format | Rp 1.250.000 (. thousand sep, , decimal) | Indonesian standard |
| Date format | DD-MM-YYYY default, Indonesian month names | Indonesian standard |
| Default language | Bahasa Indonesia | LOCK-1 |
| Honorific upward (sopir/admin → owner) | "Pak Tan" / "Cik" / "Koh" / "Bapak" | LOCK-4 + LOCK-5 |
| Honorific downward (owner → sopir) | "Pak Sopir Rohim" / "Mas Hamdani" | LOCK-5 |
| Voice-note acceptance | Mandatory for sopir-facing | LOCK-5 |
| Religious-calendar awareness | Imlek + Cap Go Meh + Qingming (owner) / Eid + Ramadhan + Christmas (karyawan) | LOCK-5 |
