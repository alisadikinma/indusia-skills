# Pillar 10 — Anti-Fraud-With-Dignity Workflow (Generic Cross-Layer Template)

> **Purpose:** Reusable workflow template for **cross-cultural-layer fraud detection** that bridges owner directness AND workforce face-saving — without sacrificing either. Generic pattern applicable to any owner-workforce cultural-friction scenario.
>
> **Project Variable Lock:** Operators inject project-specific values via `{{placeholder}}` syntax. For IRN project, see `D:/Projects/Indusia-AI-Logistik/project-variables.md` §10 for illustrative bindings.
>
> **Original framing context:** Designed for Tionghoa-Indonesian SME owner archetype (direct register on fraud data) interfacing with Indonesian-general workforce (face-saving register on confrontation). Adapt to other archetype combinations as needed.

---

## 1. The Cross-Layer Friction (Why Standard Approaches Fail)

This workflow exists because two standard approaches both fail when cultural-layer mismatch exists between owner and workforce:

### Approach A: "Direct alert to owner, ignore workforce feeling"
- Owner sees alert real-time
- Workforce member feels "diawasi seperti maling" → demotivated, eventually quit
- False-positives damage trust permanently (1 false alarm = months of resentment)
- Reputation spreads in WhatsApp networks — recruitment harder

### Approach B: "Soft-handle, give workforce benefit of doubt always"
- Owner blind to real fraud
- Owners with direct register get frustrated with "soft" framing
- Fraud accumulates, financial loss compounds

### Approach C (THIS WORKFLOW): "Both — workforce clarification chance + owner full data + audit trail"
- Workforce gets quiet clarification window via WhatsApp
- Owner gets full data at real-time — sees both "anomaly detected" AND "clarification sent, awaiting response"
- Owner can override (skip clarification window) for confirmed-pattern repeat cases
- Both layers preserved + audit trail compliance-ready

---

## 2. The Workflow (3 Stages)

### Stage 1 — Detection (auto, ~5 sec after sensor event)

**Trigger conditions (any one — calibrate threshold per project):**
- Fuel anomaly: fuel-stop event with tank-rise gap ≥ threshold liters or ≥ percent of fill amount
- Fuel-stop without expected GPS proximity to SPBU
- 30-day rolling outlier: vehicle fuel consumption z-score > threshold vs personal baseline

**System actions:**
1. Insert `fuel_anomaly_alert` row (status='detected')
2. Trigger Stage 2 — workforce clarification
3. Trigger Stage 2-parallel — owner soft-alert (informational only, not action-requested)
4. Log to `asisten_conversation_log` (system-actor turn)

**Owner-side at Stage 1:** Dashboard shows alert with status badge "🟡 Clarification sent, awaiting workforce response (24hr window)". Owner CAN drill in to see full data immediately. Owner CAN override and force-confirm fraud (skips Stage 2 clarification) — but default flow waits 24hr.

### Stage 2 — Workforce Clarification (Asisten → workforce member via WhatsApp, 24hr window)

**Asisten sends face-saving framing message. Template:**

> *"`{{worker_honorific}}` `{{worker_first_name}}`, ada catatan pengisian solar tadi pagi jam 9:15 di SPBU `{{spbu_location}}` — isi 50 liter tapi tank naik 35 liter. Mungkin ada drip / leak / penyusutan udara panas, atau ada yang lain? Bisa konfirmasi ya. Balas chat ini dalam 24 jam (sebelum besok jam 9:15) — kalau tidak ada keterangan, sistem akan flag ke `{{owner_honorific}}` untuk review."*

**Workforce response paths:**

| Response Type | Classification | Next Action |
|---|---|---|
| Reasonable explanation (drip detected, mechanic checked, tank seal worn) | "Clarified — operational issue" | Mark resolved, route to maintenance queue, NO owner action. Badge → "🟢 Clarified — operational" |
| Vague explanation ("biasa aja", "saya gak tau") | "Clarification insufficient" | Wait remaining 24hr. If no follow-up → Stage 3 |
| Admission (alternative use confirmed) | "Admitted alternative use" | Stage 3 escalate immediately with admission text attached |
| No response after 24hr | "No response — escalate" | Stage 3 escalate auto |
| Defensive / aggressive | "Defensive — flag escalate" | Stage 3 escalate + add note for owner |

**Honorific rule:** Asisten addresses workforce with `{{worker_honorific}}` (face-saving). Per cultural archetype (e.g., Indonesian general: "Pak [first name]" / "Mas [first name]" / "Mbak [first name]"). NEVER full name, NEVER "Anda" formal in workforce-facing message.

**Voice-note option:** Asisten message includes "🎤 Bisa balas via voice note juga ya `{{worker_honorific}}`" — lower text literacy accommodation common in Indonesian general workforce.

### Stage 3 — Owner Confirmation (Asisten → owner via WhatsApp)

**Triggered by:** workforce unresolved (Stage 2 paths "vague" / "admission" / "no response" / "defensive")

**Asisten sends owner-facing message. Template (calibrated to `{{owner_register}}`):**

> *"`{{owner_honorific}}`, `{{vehicle_id}}` fuel anomali 15L gap (jam 9:15 SPBU `{{spbu_location}}`) belum terklarifikasi. Sopir `{{worker_first_name}}` balas: [path-specific summary]. Saya tahan invoice fuel `{{vehicle_id}}` (Rp 850k) sampai `{{owner_honorific}}` review? Y/N — atau tanya lebih detail dulu?"*

**Owner response paths:**

| Owner Response | Asisten Action | Audit Log |
|---|---|---|
| "Y" (tahan invoice) | Call `hold_invoice(<invoice_id>)`. Mark anomaly as "owner-confirmed-fraud" | `action_taken='invoice_held:<id>'`, `permission_check='owner-confirmed:Y'` |
| "N" (lanjut, biarkan) | Mark as "owner-acknowledged-tolerated" | `action_taken='alert_dismissed:tolerated'`, retain |
| "Tanya detail" | Show full sensor data, workforce history, similar anomalies | Conversation continues |
| "Suruh klarifikasi lagi" | Re-send Stage 2 with note "`{{owner_honorific}}` minta detail tambahan" | Stage 2 restart, log re-iteration |
| "Suruh supervisor check" | Route to supervisor WhatsApp + add owner-flag | `escalation='supervisor-routed'` |

**Owner override path (skip Stage 2):** Dashboard option "🔴 Force confirm fraud (skip 24hr clarification)" — use when same workforce member already 3+ confirmed-fraud history. Documented in `asisten_conversation_log.override_reason`.

---

## 3. Workforce-Side UX Detail (Cross-Reference UX Skill)

Workforce-facing message design principles (detail in `senior-ux-architect-id/06-anti-fraud-with-dignity-ux.md`):

1. **Face-saving framing** — passive observation ("ada catatan pengisian solar") NOT accusation
2. **Plausible alternative explanations offered** — gives workforce face-saving exit ("mungkin drip / leak / penyusutan udara panas")
3. **24hr window** — not minute-pressure, allows investigation (might be real mechanical issue)
4. **Voice-note option mandatory** — cultural accommodation for lower-text-literacy demographics
5. **Asisten = honest broker framing** — neutral observer, NOT "owner's eyes"
6. **No public visibility** — only workforce member + owner + supervisor (if escalated) see
7. **No automatic punishment** — Stage 2 resolution doesn't trigger any consequence; consequences only from Stage 3 owner decision

---

## 4. Edge Cases

| Case | Handling |
|---|---|
| Workforce member sick / leave at anomaly time | 24hr window extends to "next on-duty day + 24hr". Asisten doesn't send WhatsApp during leave. |
| Workforce member no longer employed | Auto-skip to Stage 3 immediately. Note in log: "worker-no-longer-employed". |
| Multiple anomaly same worker, same shift | Batch into single clarification message ("ada 2 catatan...") |
| Anomaly during religious calendar event (any layer) | Window extends to "post-event + 24hr" — religious calendar awareness |
| Workforce admits with mitigating context (sakit anak, family emergency, etc.) | Asisten classify as "admission-with-mitigating-context" → Stage 3 with full context. Owner decision space includes "kasbon top-up + counseling" path. |
| False positive (sensor malfunction) | Worker + mechanic both confirm hardware. Alert → 🔧 sensor maintenance, no fraud action. Audit retains. |

---

## 5. Metrics + Dashboard

Owner dashboard weekly summary:

| Metric | Definition |
|---|---|
| Anomaly detected count | Total fuel anomaly alerts triggered |
| Clarified (operational) % | % resolved via worker clarification as legitimate operational issue |
| Confirmed-fraud count | Owner-confirmed at Stage 3 |
| Average response time (worker) | Mean hours from Stage 2 sent to worker response |
| Average response time (owner) | Mean hours from Stage 3 sent to owner decision |
| Tolerated count | Owner-acknowledged-tolerated (rare, but tracked) |
| False positive rate | (Clarified operational + sensor-malfunction) / total |

**Health target:** False positive rate <15% (above this → tune detection threshold / sensor calibration).

---

## 6. Cross-Layer Cultural Validation (Calibrate per Project)

### Owner expectations (calibrate to `{{owner_register}}`)

| Register | Owner Expectation | Resolution |
|---|---|---|
| Practical-skeptic / direct (e.g., Tionghoa Batam SME default) | "Saya mau lihat full data, tidak mau di-filter dulu" | ✅ Stage 1 owner gets data real-time with badge |
| Practical-skeptic | "Sopir saya kalau salah harus tahu, tapi saya tidak mau jadi yang konfrontasi langsung" | ✅ Asisten as honest broker |
| Aspirational / tech-positive | "Sistem-nya pintar — bisa kasih solusi otomatis ya?" | Same workflow, frame as system intelligence not surveillance |
| Cautious | "Saya tidak suka false alarm" | ✅ 24hr clarification window reduces false positive surfacing |

### Workforce expectations (calibrate to cultural archetype)

| Workforce Archetype | Expectation | Resolution |
|---|---|---|
| Indonesian general (Melayu / Jawa / Batak / Minang / Bugis mix) face-saving | "Saya tidak mau di-tuduh tanpa kesempatan jelasin" | ✅ Stage 2 clarification window |
| Same | "Saya tidak mau owner langsung marah ke saya" | ✅ Asisten as buffer |
| Same | "Saya pengen tetap kerja kalau memang tidak salah" | ✅ False positive can be clarified without consequences |
| Same | "Saya tidak mau muka saya jatuh di depan rekan" | ✅ Stage 2 message private, no broadcast |

**Asisten role:** Honest broker that gives both layers what they need simultaneously. Unique value proposition vs competitor approaches that have data layer but no clarification layer.

---

## 7. Pitch Quote Template (for creative output)

```
"`{{owner_honorific}}` mau tahu kalau ada yang ngambil solar. Tapi sopir `{{owner_honorific}}` 
tidak mau di-tuduh maling tanpa kesempatan jelasin. Asisten `{{owner_honorific}}` 
jadi orang tengah: kasih sopir 24 jam untuk klarifikasi, kalau tidak ada keterangan 
baru `{{owner_honorific}}` di-info dengan full data. Tidak ada yang merasa diawasi 
seperti maling. Tidak ada yang merasa buta. Dua-duanya tahu, dua-duanya respect."
```

Project replaces `{{owner_honorific}}` with bound value (e.g., "Pak Indra" or "Bapak").

---

## 8. Cross-References

- Detection tech stack → `05-anti-fraud-tech-stack.md`
- Asisten architecture template → `11-ceo-ai-assistant-architecture.md`
- Workforce-side UX wireframes + voice-note design → `senior-ux-architect-id/06-anti-fraud-with-dignity-ux.md`
- Confirmed-fraud kasbon offset tax + accounting treatment → `akuntan-indonesia-pro/08-internal-control-antifraud.md`
- Owner-archetype cultural register options → `creative-video-director/01-brand-voice-doctrine.md`
- Cross-layer cultural friction context per archetype → project's own owner-archetype overlay file (e.g., `creative-video-director/07-tionghoa-batam-overlay.md` for Tionghoa Batam example)
- Workforce-side UX (Indonesian general): voice-note + face-saving + multi-ethnic safety → `senior-ux-architect-id/02-driver-app-ux-deep.md`
- **Project binding values:** project's own `project-variables.md` (e.g., `D:/Projects/Indusia-AI-Logistik/project-variables.md` for IRN)
