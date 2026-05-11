# Pillar 11 — CEO AI Assistant Architecture (Generic Template)

> **Purpose:** Reusable architecture TEMPLATE for **conversational AI assistant** that surfaces real-time fleet ops to SME owner via WhatsApp chat + voice call. Generic pattern applicable to any INDUSIA client running fleet operations. NOT bound to specific person.
>
> **Project Variable Lock:** Operators inject project-specific values via `{{placeholder}}` syntax. For IRN project, see `D:/Projects/Indusia-AI-Logistik/project-variables.md` §1, §9 for binding values.
>
> **3-Pillar mapping:** This file = Pillar 1 (AI) apex architecture. Integrates Pillar 2 (IoT data feed) + Pillar 3 (Mobile WhatsApp + Voice gateway).

---

## 1. Architecture Definition

**CEO AI Assistant** (referred to in spec as `{{asisten_name}}`) = AI assistant that:

- Knows field conditions **REAL-TIME** via tool-calling to live ops data: GPS, fuel sensor, RFID gate, attendance, invoice, container manifest, AR/AP, Coretax e-Faktur
- Accessible via **WhatsApp chat (primary, Phase 1)** + **voice call (secondary, Phase 2)**
- **Bahasa Indonesia native** + optional dialect support per `{{owner_language_secondary}}` (e.g., Hokkien/Mandarin code-switch detection — nice-to-have Phase 2)
- **Owner-only access** Phase 1; expand to admin + accountant Phase 2 with sub-account scope
- **Memory of owner preferences** over time (greeting style, unit preference, currency format, religious-calendar awareness)

### Why This Is a Flagship Feature Pattern

- Concrete: "asisten that owner can ask anytime" vs abstract "AI dashboard"
- Single conversational interface unifies multiple owner visibility gaps (project-specific gap inventory in `pakar-logistik-batam/09-product-feature-map.md`)
- Differentiator vs incumbent competitors that have data layers but no owner-facing conversational layer
- WhatsApp = zero-adoption-friction (most SME owners use WhatsApp daily)

---

## 2. Example Interaction Templates

These are TEMPLATE patterns showing how queries map to tool-calling. Specific entity names (`{{owner_honorific}}`, `{{driver_example_name}}`, `{{vehicle_example_id}}` etc.) bind to project values.

### Real-time field condition query

> **Owner:** *"Truk `{{vehicle_example_id}}` sudah sampai mana?"*
>
> **Asisten:** *"`{{owner_honorific}}`, truk `{{vehicle_example_id}}` sopir `{{driver_example_name}}` sekarang di `{{location_example}}`, 2 km dari customer `{{customer_example_name}}`. ETA 15 menit. Tarik container `{{container_example_id}}` dari `{{port_example}}` 1 jam lalu, GPS bersih, solar masih 45 liter."*

### Fuel anomaly check

> **Owner:** *"Solar hari ini gimana?"*
>
> **Asisten:** *"5 truk aktif. `{{vehicle_anomaly_id}}` ada anomali, `{{owner_honorific}}` — isi 50 liter di SPBU jam 9:15 tapi tank naik cuma 35 liter, gap 15 liter. Sudah saya kirim alert pagi tadi ke WhatsApp Bapak. Saya tahan invoice fuel `{{vehicle_anomaly_id}}` sampai `{{owner_honorific}}` review?"*

### Attendance query

> **Owner:** *"Hari ini ada yang telat?"*
>
> **Asisten:** *"Ya `{{owner_honorific}}`. Sopir `{{driver_example_2}}` clock-in 8:47 (telat 47 menit). Admin `{{admin_example_name}}` 7:55 on-time."*

### ETA + multi-trip status

> **Owner:** *"Sopir `{{driver_example_name}}` hari ini kerja gimana?"*
>
> **Asisten:** *"`{{owner_honorific}}`, sopir `{{driver_example_name}}` hari ini 2 trip selesai, 1 trip running. Trip 1 ETA prediction 11:30, aktual 11:42 — 12 menit lewat ideal. Trip 2 on-time. Trip 3 sekarang berangkat ke `{{port_example}}`, ETA prediction 16:15. Solar normal, GPS bersih, RFID gate triplet match. Mau saya tahan trip 4-nya hari ini, atau lanjut?"*

### Container + chassis location

> **Owner:** *"Chassis `{{chassis_example_id}}` di mana, container yang harus keluar besok pagi posisi mana?"*
>
> **Asisten:** *"Chassis `{{chassis_example_id}}` di yard slot `{{yard_slot_example}}`, terakhir scan RFID jam 14:22. Container `{{container_example_id}}` (untuk delivery customer `{{customer_example_name}}` besok pagi 07:00) ada di slot `{{yard_slot_example}}` juga, sudah loaded ke `{{chassis_example_id}}`."*

### Finance query (cross-pillar with akuntan integration)

> **Owner:** *"Cashflow bulan ini gimana?"*
>
> **Asisten:** *"`{{owner_honorific}}`, AR aging hari ini Rp 487jt — 23% di-bucket 60+ hari, customer terbesar `{{customer_example_name_2}}` Rp 145jt aging 73 hari. AP due 7 hari ke depan Rp 198jt. Cashflow net bulan ini estimasi Rp +52jt. Coretax e-Faktur sync status: 47/47 OK."*

**Register calibration:** Adjust tone per `{{owner_register}}` — practical-skeptic = direct + numeric + brief; tech-positive aspirational = engaged + exploratory + paragraph-length.

---

## 3. Architecture (Layer Stack)

| Layer | Tech Recommendation | Rationale |
|---|---|---|
| **LLM backend** | Claude Sonnet 4.6 / Opus 4.7 via Anthropic API (Phase 1) — defer Llama 3 self-host evaluation to Phase 2 | Native Bahasa Indonesia + dialect flexibility, tool-calling reliable, prompt caching reduces cost. Anthropic SDK with prompt caching mandatory. |
| **RAG / Tool-calling** | **Live ops data via tool-calling pattern** (LLM calls DB/API real-time, NOT pre-indexed) | Stale RAG index = wrong answer for real-time query. Tool-calling = guaranteed fresh data. |
| **Chat gateway** | WhatsApp Business API via **Wati** (most likely) — alt: Maytapi / 360dialog / Mekari Qontak | Indonesian ecosystem coverage. Decision pending vendor proposal per project. |
| **Voice gateway (Phase 2)** | **Whisper STT Indonesian** → LLM → **ElevenLabs/Google TTS Indonesian**. Integration via Twilio Indonesia / Vonage / direct SIP | STT Indonesian quality matters — Whisper validated baseline. TTS Indonesian voice quality — ElevenLabs > Google for naturalness. |
| **Memory layer** | Owner preferences persist via dedicated table (`asisten_owner_preference`) — greeting style, unit, currency, religious-calendar | Personalization without re-asking. Persona validation interview answers seed initial preferences. |
| **Permission layer** | Owner-only Phase 1 → admin + accountant sub-account Phase 2 dengan **scope berbeda** | Owner = full read access. Admin = scope-limited. Accountant = scope-limited. Family sub-accounts (Phase 2) per `{{owner_family_business_subaccounts}}`. |
| **Audit trail** | Everything logged to **`asisten_conversation_log`** table, Coretax-compliance-ready | Every owner decision logged with timestamp + owner_id + action_taken — audit-defensible. |

### 3.1 Tool-Calling Tool Inventory (Phase 1 must-have)

Asisten LLM can call these tools via Anthropic SDK tool-use API:

| Tool Name | Purpose | Data Source | Latency Budget |
|---|---|---|---|
| `get_vehicle_location(vehicle_id)` | Real-time field condition | `vehicle_position_live` (PostGIS) | <500ms |
| `get_vehicle_status(vehicle_id, optional date_range)` | Trip + status check | `delivery` + `trip_log` | <800ms |
| `get_fuel_anomaly_alerts(date_range)` | Fuel-fraud detection | `fuel_anomaly_alert` (rolling 30-day) | <1s |
| `get_attendance_today(optional employee_id)` | Attendance | `attendance_rfid_log` | <500ms |
| `get_container_location(container_id)` | Container in-transit + yard | `container_position` (last RFID scan + AI inference) | <500ms |
| `get_chassis_location(chassis_id)` | Chassis tracking | `chassis_position` (RFID + GPS) | <500ms |
| `get_yard_inventory(date)` | Yard slot occupancy | `yard_slot_occupancy` | <1s |
| `get_eta_prediction(vehicle_id, destination)` | ETA model | ML inference | <2s |
| `get_cashflow_summary(date_range)` | Finance overview | Akuntansi backend (akuntan skill scope) | <2s |
| `get_ar_aging()` | Receivables status | `customer_invoice` | <1s |
| `get_coretax_sync_status()` | Tax sync health | Coretax integration log | <1s |
| `hold_invoice(invoice_id, reason)` | Action: invoice hold | Action API + permission + audit | <500ms |
| `send_sopir_clarification(sopir_id, anomaly_id, message)` | Anti-fraud-with-dignity workflow trigger | WhatsApp Business API | <2s |
| `get_owner_preference(key)` / `set_owner_preference(key, value)` | Personalization | `asisten_owner_preference` | <300ms |
| `log_conversation(turn_id, content, action_taken)` | Audit | `asisten_conversation_log` | <300ms |

**Anti-pattern:** Generic "execute_action" tool naming — every action must be specific tool for permission scope + audit clarity.

### 3.2 Permission Escalation Pattern (Critical Principle)

Asisten **TIDAK auto-execute** actions that affect operations. Always confirm owner Y/N first:

```
User: "Solar hari ini gimana?"
Asisten: (read-only — auto-respond with data)

User: "Tahan invoice {{vehicle_example_id}}."
Asisten: "Konfirmasi {{owner_honorific}} — tahan invoice fuel {{vehicle_example_id}} 
        (Rp 850k) karena 15L solar gap pagi tadi? Y/N"
User: "Y"
Asisten: → call hold_invoice() tool → log to audit trail → confirm
```

**Why:** Owner-direct framing + auditable (Coretax-compliance) + reversible.

### 3.3 Proactive Alert Mode

Asisten may push WhatsApp without trigger when anomaly threshold breached:

| Trigger | Alert Pattern | Permission |
|---|---|---|
| Fuel anomaly ≥10L gap detected | *"`{{owner_honorific}}`, `{{vehicle_id}}` fuel anomali 15L jam 9:15 di `{{spbu_example}}`. Detail di [link]. Saya tahan invoice atau lanjut?"* | Auto-send + Y/N required |
| Sopir telat >60 min | *"`{{owner_honorific}}`, sopir `{{driver_name}}` belum clock-in jam 9:30 (jadwal 8:00). Saya hubungi sopir / supervisor?"* | Auto-send |
| Customer urgent call masuk | *"`{{owner_honorific}}`, customer `{{customer_name}}` call WhatsApp jam 14:23 (urgent flag). Mau saya forward atau respond dulu?"* | Auto-send |
| Coretax sync fail | *"`{{owner_honorific}}`, e-Faktur sync gagal 3 faktur jam 16:00. Detail di [link]. Saya retry atau panggil accountant?"* | Auto-send |
| Religious-calendar reminder | *"`{{owner_honorific}}`, besok `{{calendar_event}}`. Operasional saya schedule mode-libur — mau saya pertahankan X sopir on-call atau full libur?"* | Auto-send + Y/N |

**Anti-pattern:** Auto-send for normal status updates. Owner = pull-based query, not push-spam.

### 3.4 Religious + Working-Hours Sensitivity Mode

Calibrate to `{{owner_calendar_observed}}` + `{{owner_working_hours}}`:

| Mode | Trigger | Behavior |
|---|---|---|
| **Brief mode** | Sholat/prayer time (Muslim archetypes — not Tionghoa default) | Reply short, no push notifications |
| **Calendar-event week** | Event date in `{{owner_calendar_observed}}` (e.g., Imlek, Eid, Vesak) ± 3 days | Auto-quiet mode, only critical alert |
| **Specific event reminders** | Per `{{owner_calendar_observed}}` event list | Reminder + reduced push |
| **Working hours pattern** | `{{owner_working_hours}}` (e.g., 06:00-19:00) | Auto-quiet outside owner-defined window |

**Auto-detection:** Asisten checks calendar daily 04:00 WIB, sets mode flag for the day. Owner can override anytime.

### 3.5 Conversational UX Canon (cross-reference)

Detail in `senior-ux-architect-id/04-conversational-ux-canon.md`. Summary:

1. **Bahasa Indonesia first** + `{{owner_language_secondary}}` code-switch when natural
2. **Honorific always** — `{{owner_honorific}}` (never strip)
3. **Response length per `{{owner_response_length}}`** — brief / mid / detailed per project
4. **Religious sensitivity** — calendar mode (3.4)
5. **Permission escalation** — confirm Y/N before action
6. **Audit trail** — log everything
7. **Register per `{{owner_register}}`** — practical-skeptic / aspirational / cautious / etc.

---

## 4. Schema Additions (cross-reference `02-data-model-schema.md` Addendum)

3 new tables required Phase 1:

```sql
-- 4.1 Conversation log (audit-defensible, Coretax-compliance-ready)
CREATE TABLE asisten_conversation_log (
  id BIGSERIAL PRIMARY KEY,
  owner_id INTEGER NOT NULL REFERENCES owner(id),
  session_id UUID NOT NULL,
  turn_index INTEGER NOT NULL,
  role TEXT NOT NULL CHECK (role IN ('user','asisten','tool','system')),
  content TEXT NOT NULL,
  tool_name TEXT,
  tool_input JSONB,
  tool_output JSONB,
  action_taken TEXT,
  permission_check TEXT,
  override_reason TEXT,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- 4.2 Alert queue (proactive mode + delivery tracking)
CREATE TABLE asisten_alert_queue (...);  -- see 02-data-model-schema.md Addendum

-- 4.3 Owner preferences (memory layer, personalization)
CREATE TABLE asisten_owner_preference (
  owner_id INTEGER NOT NULL REFERENCES owner(id),
  preference_key TEXT NOT NULL,
  preference_value JSONB NOT NULL,
  source TEXT NOT NULL CHECK (source IN ('persona_validation','learned','owner_explicit')),
  last_updated TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  PRIMARY KEY (owner_id, preference_key)
);
```

### Seed `preference_key` set (Phase 1)

Standard preference keys (values bound per project via persona validation):

| Key | Value Shape | Bound From |
|---|---|---|
| `greeting` | `{"honorific": "<bound>", "language": "<bound>"}` | `{{owner_honorific}}` + `{{owner_language_secondary}}` |
| `unit_volume` | `"liter"` or `"galon"` | Project preference |
| `unit_distance` | `"km"` or `"mil"` | Project preference |
| `currency_format` | `{"separator": ".", "decimal": ","}` (Indonesian standard) | Default Indonesian |
| `working_hours_start` | `"HH:MM"` | `{{owner_working_hours}}` start |
| `working_hours_end` | `"HH:MM"` | `{{owner_working_hours}}` end |
| `quiet_hours` | `[{"start":"HH:MM","end":"HH:MM"}]` | overnight default |
| `religious_calendar` | array of event names | `{{owner_calendar_observed}}` |
| `language_secondary` | `"hokkien"` / `"mandarin"` / `"teochew"` / null | `{{owner_language_secondary}}` |
| `response_length` | `"brief"` / `"mid"` / `"detailed"` | `{{owner_response_length}}` |
| `register` | `"practical_skeptic"` / `"aspirational"` / `"cautious"` | `{{owner_register}}` |
| `proactive_alert_threshold` | per-anomaly-type thresholds | learned over time |

**Why JSONB for `preference_value`:** future-proof — new preference keys without schema migration.

---

## 5. Phase 1 → Phase 2 → Phase 3 Roadmap

| Phase | Scope | Timeline (project-bound) | Effort (project-bound) |
|---|---|---|---|
| **Phase 1** | Owner-only WhatsApp chat. ~15 tool-calling tools (read + 2 write actions). Audit log + permission escalation. Bahasa Indonesia. | Project-specific | Project-specific |
| **Phase 2** | Voice call gateway (Twilio + Whisper + ElevenLabs). Admin + accountant sub-accounts dengan scope. Dialect code-switch detection per `{{owner_language_secondary}}`. Religious calendar proactive mode. Family sub-accounts per `{{owner_family_business_subaccounts}}`. | Project-specific | Project-specific |
| **Phase 3** | Auto-execution (curated patterns). Multi-tenant SaaS architecture for INDUSIA agency to deploy to multiple clients. Multi-archetype register calibration. | Project-specific | Project-specific |

---

## 6. Anti-Patterns

❌ **Pre-index RAG over live ops data** — staleness = wrong answers. Tool-calling pattern locked.

❌ **Auto-execute actions without owner confirmation** — destroys trust, audit risk. Always Y/N gate.

❌ **Generic LLM persona** ("Hi! I'm an AI assistant! How can I help you today?") — Indonesian SME owners hate fluff. Brief Indonesian opening matching `{{owner_register}}`: practical-skeptic = *"`{{owner_honorific}}`, saya di sini. Mau cek apa?"*; aspirational = *"`{{owner_honorific}}`, ada yang menarik hari ini?"*

❌ **Push notification spam** for normal events. Only anomaly + threshold breach.

❌ **Voice call Phase 1** — STT Indonesian voice + latency budget not validated. Defer Phase 2.

❌ **Admin/accountant full access Phase 1** — owner-only first. Permission scope design needs UX validation Phase 2.

❌ **English in default prompts** — Bahasa Indonesia primary. English code-switch okay for technical terms (RFID, GPS, container).

❌ **Hardcoded `{{owner_honorific}}` in skill examples** — examples must use placeholder syntax; project repo binds.

❌ **Long responses** when `{{owner_response_length}}` = "brief" — owner wants direct answer, not essay.

---

## 7. Naming Convention (Template)

`{{asisten_name}}` options for project to choose:

- Option A: **"INDUSIA Asisten"** (agency-branded, reusable across clients)
- Option B: **"Asisten `{{owner_first_name}}`"** (owner-personalized — e.g., "Asisten Pak Indra")
- Option C: **"Asisten Logistik `{{client_short_name}}`"** (company-branded)
- Option D: **Custom name** (give human persona — e.g., "Pak Asep", "Mas Joko")

**Default in spec/code:** "Asisten" generic until project picks naming via `project-variables.md` §9.

---

## 8. Cross-References

- Owner visibility gap pattern source-of-truth → `pakar-logistik-batam/09-product-feature-map.md` (project binds specific gap inventory)
- Schema integration → `02-data-model-schema.md` Addendum (full DDL)
- Conversational UX canon → `senior-ux-architect-id/04-conversational-ux-canon.md`
- Voice UX low-cognitive-load → `senior-ux-architect-id/05-voice-ux-low-cognitive-load.md`
- Anti-fraud-with-dignity workflow (clarification routing) → `10-anti-fraud-with-dignity.md`
- Coretax audit trail integration → `akuntan-indonesia-pro/06-ppn-efaktur-coretax.md` + `08-internal-control-antifraud.md`
- Owner overlay UX (Tionghoa Batam example) → `senior-ux-architect-id/09-tionghoa-batam-owner-overlay.md`
- Owner overlay creative (Tionghoa Batam example) → `creative-video-director/07-tionghoa-batam-overlay.md`
- Anthropic SDK integration patterns (prompt caching, tool-use) → use `claude-api` skill when implementing
- **Project binding values:** project's own `project-variables.md` (e.g., `D:/Projects/Indusia-AI-Logistik/project-variables.md` for IRN)
