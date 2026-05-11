# Pillar 02 — Data Model & Schema

## Core Entities (15 Tables)

```
┌──────────────────────────────────────────────────────────────────┐
│                   DOMAIN ENTITIES                                │
└──────────────────────────────────────────────────────────────────┘

Core Operations:
  ├── Customer
  ├── Vehicle (head truck / mobile crane unit)
  ├── Chassis (40ft, 20ft, etc — for trucking only)
  ├── Driver / Operator
  ├── Location (port, depot, customer site)
  └── Container (tracking number-based)

Lifecycle:
  ├── Delivery (1 trip = 1 row, multi-leg via parent_id)
  ├── Container_lifecycle (event log per container)
  └── Crane_job (1 project = 1 row)

Asset Management:
  ├── Yard_slot (CYMS — depot grid)
  ├── Cert_silo_sio (compliance tracker)
  └── Maintenance_log

Financial:
  ├── Invoice
  ├── Cashbond_ledger
  ├── Fuel_event
  └── Transaction (general ledger)

Sensor & Telemetry:
  ├── GPS_ping (time-series)
  ├── Fuel_sensor_event
  └── Gate_event (UHF RFID logs)

System:
  ├── User (auth)
  ├── Audit_log
  └── Integration_event (external API events)
```

---

## SQL DDL — Core Schema (PostgreSQL + PostGIS)

### Customer

```sql
CREATE TABLE customer (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    code VARCHAR(20) UNIQUE NOT NULL,   -- "PT-YIXIN-001"
    name VARCHAR(200) NOT NULL,
    type VARCHAR(20) NOT NULL,          -- 'shipping_line', 'shipyard', 'manufaktur', 'epc', 'individual'
    npwp VARCHAR(20),
    address TEXT,
    contact_person VARCHAR(100),
    contact_phone VARCHAR(30),
    email VARCHAR(100),
    
    -- Tier & credit
    tier CHAR(1) DEFAULT 'B',           -- A/B/C/D/E
    payment_terms_days INT DEFAULT 60,
    credit_limit_idr DECIMAL(15,2),
    cas_free_days INT DEFAULT 3,        -- demurrage SLA per customer
    
    -- Status
    is_active BOOLEAN DEFAULT TRUE,
    blacklisted BOOLEAN DEFAULT FALSE,
    
    -- Soft delete
    deleted_at TIMESTAMPTZ,
    
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_customer_active ON customer (is_active) WHERE deleted_at IS NULL;
CREATE INDEX idx_customer_tier ON customer (tier) WHERE is_active = TRUE;
```

### Vehicle (Head Truck atau Crane)

```sql
CREATE TABLE vehicle (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    plate_number VARCHAR(20) UNIQUE NOT NULL,    -- "B 9281 SEH"
    pm_code VARCHAR(10),                          -- "PM 22"
    type VARCHAR(20) NOT NULL,                    -- 'head_truck', 'mobile_crane', 'rough_terrain', 'crawler'
    capacity VARCHAR(20),                         -- "25T" untuk crane
    brand VARCHAR(50),                            -- "Hino", "Mitsubishi", "Tadano"
    model VARCHAR(50),
    year_manufactured INT,
    year_acquired INT,
    
    -- Status
    is_active BOOLEAN DEFAULT TRUE,
    current_driver_id UUID REFERENCES driver(id),
    
    -- Compliance docs (denormalize untuk speed)
    stnk_expires_at DATE,
    kir_expires_at DATE,
    silo_expires_at DATE,                         -- crane only
    sia_expires_at DATE,                          -- crane only
    
    -- Asset value
    capex_idr DECIMAL(15,2),
    book_value_idr DECIMAL(15,2),
    
    deleted_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_vehicle_type_active ON vehicle (type, is_active);
CREATE INDEX idx_vehicle_silo_expires ON vehicle (silo_expires_at) WHERE silo_expires_at IS NOT NULL;
```

### Chassis

```sql
CREATE TABLE chassis (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    code VARCHAR(20) UNIQUE NOT NULL,             -- "IRN 40-027"
    size VARCHAR(10) NOT NULL,                    -- '20FT', '40FT', '40HC', '40FP'
    rfid_tag VARCHAR(64),                         -- UHF tag EPC
    capacity_tonnage DECIMAL(5,2),
    
    is_active BOOLEAN DEFAULT TRUE,
    current_location_id UUID REFERENCES location(id),
    last_used_at TIMESTAMPTZ,
    last_delivery_id UUID REFERENCES delivery(id),
    
    -- Yard slot (kalau di depot)
    current_yard_slot_id UUID REFERENCES yard_slot(id),
    
    deleted_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_chassis_active_size ON chassis (size, is_active);
CREATE INDEX idx_chassis_idle ON chassis (last_used_at) WHERE is_active = TRUE;
```

### Driver

```sql
CREATE TABLE driver (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    employee_code VARCHAR(20) UNIQUE NOT NULL,    -- "DRV-001"
    name VARCHAR(100) NOT NULL,
    rfid_tag VARCHAR(64),                         -- UHF tag for gate logging
    
    nik VARCHAR(20),                              -- KTP (encrypted at rest)
    sim_class VARCHAR(10),                        -- 'B1', 'B2', 'B2 Umum'
    sim_expires_at DATE,
    sio_class VARCHAR(10),                        -- 'I', 'II', 'III' (crane operator)
    sio_expires_at DATE,
    
    join_date DATE,
    leave_date DATE,
    is_active BOOLEAN DEFAULT TRUE,
    
    -- Performance
    performance_score DECIMAL(5,2),               -- 0-100, rolling 90-day
    safety_score DECIMAL(5,2),
    fuel_efficiency_score DECIMAL(5,2),
    
    -- Pay
    pay_model VARCHAR(20) DEFAULT 'commission',   -- 'commission', 'salary', 'hybrid'
    commission_pct DECIMAL(5,4) DEFAULT 0.25,     -- 25%
    base_salary_idr DECIMAL(10,2) DEFAULT 0,
    
    deleted_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_driver_active ON driver (is_active);
CREATE INDEX idx_driver_sio_expires ON driver (sio_expires_at) WHERE sio_expires_at IS NOT NULL;
```

### Location

```sql
CREATE TABLE location (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    code VARCHAR(20) UNIQUE NOT NULL,             -- 'BATU_AMPAR', 'TANJUNG_UNCANG'
    name VARCHAR(100) NOT NULL,
    type VARCHAR(20),                             -- 'port', 'depot_internal', 'depot_external', 'customer_site', 'workshop'
    
    -- Geo
    geom GEOGRAPHY(POINT, 4326),                  -- PostGIS for radius queries
    geofence GEOGRAPHY(POLYGON, 4326),            -- for auto-prompt trigger
    address TEXT,
    
    is_active BOOLEAN DEFAULT TRUE,
    deleted_at TIMESTAMPTZ
);

CREATE INDEX idx_location_geom ON location USING GIST (geom);
CREATE INDEX idx_location_geofence ON location USING GIST (geofence);
```

### Delivery (Trip)

```sql
CREATE TABLE delivery (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    delivery_no VARCHAR(20) UNIQUE NOT NULL,      -- "26887"
    
    -- Multi-leg link
    parent_delivery_id UUID REFERENCES delivery(id), -- for tarik empty linked to original deliver
    container_id UUID REFERENCES container(id),
    leg_type VARCHAR(20),                         -- 'impor_deliver', 'tarik_empty', 'load_export', 'drop_terminal'
    
    -- Parties
    customer_id UUID REFERENCES customer(id),
    partner_id UUID REFERENCES customer(id),      -- if forwarder/shipping line
    from_location_id UUID REFERENCES location(id),
    to_location_id UUID REFERENCES location(id),
    
    -- Resources
    driver_id UUID REFERENCES driver(id),
    vehicle_id UUID REFERENCES vehicle(id),
    chassis_id UUID REFERENCES chassis(id),
    
    -- Container info
    container_no VARCHAR(20),                     -- "MRKU4449924"
    container_size VARCHAR(10),
    seal_no VARCHAR(50),
    
    -- Timing
    scheduled_at TIMESTAMPTZ,
    pickup_at TIMESTAMPTZ,
    delivery_at TIMESTAMPTZ,
    
    -- Pricing
    tarif_idr DECIMAL(12,2) NOT NULL,
    fuel_cost_idr DECIMAL(10,2),
    toll_cost_idr DECIMAL(10,2) DEFAULT 0,
    misc_cost_idr DECIMAL(10,2) DEFAULT 0,
    driver_commission_idr DECIMAL(10,2),
    
    -- Status
    status VARCHAR(30) NOT NULL DEFAULT 'DRAFT',  -- DRAFT, ASSIGNED, IN_PROGRESS, DELIVERED, CONFIRMED, INVOICED, PAID, CANCELLED
    
    -- ePoD
    customer_signature_url TEXT,
    customer_signed_at TIMESTAMPTZ,
    delivery_photo_urls TEXT[],
    pickup_photo_urls TEXT[],
    
    created_at TIMESTAMPTZ DEFAULT NOW(),
    created_by UUID REFERENCES app_user(id),
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    deleted_at TIMESTAMPTZ
);

CREATE INDEX idx_delivery_status ON delivery (status, scheduled_at);
CREATE INDEX idx_delivery_customer ON delivery (customer_id, scheduled_at);
CREATE INDEX idx_delivery_driver ON delivery (driver_id, pickup_at);
CREATE INDEX idx_delivery_container ON delivery (container_id);
CREATE INDEX idx_delivery_parent ON delivery (parent_delivery_id);
```

### Container Lifecycle (Event Log)

```sql
CREATE TABLE container (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    container_no VARCHAR(20) UNIQUE NOT NULL,     -- "MRKU4449924"
    size VARCHAR(10),
    type VARCHAR(20),                              -- 'dry', 'reefer', 'flat_rack'
    owner_line_id UUID REFERENCES customer(id),    -- shipping line owner
    
    -- Latest state (denormalize for speed)
    current_status VARCHAR(30),                    -- IN_TERMINAL, AT_CUSTOMER, AT_DEPOT_IRN, RETURNED
    current_location_id UUID REFERENCES location(id),
    current_yard_slot_id UUID REFERENCES yard_slot(id),
    last_event_at TIMESTAMPTZ,
    
    arrived_at TIMESTAMPTZ,                        -- saat pertama kali ke IRN
    expected_return_at TIMESTAMPTZ,                -- ETA kembali ke shipping line
    days_in_country INT GENERATED ALWAYS AS (
        EXTRACT(DAY FROM NOW() - arrived_at)::INT
    ) STORED
);

CREATE TABLE container_event (
    id BIGSERIAL PRIMARY KEY,
    container_id UUID NOT NULL REFERENCES container(id),
    event_type VARCHAR(30) NOT NULL,               -- VESSEL_ARRIVED, SP2_ISSUED, PICKUP, DELIVERED, CUSTOMER_SIGNED, RETURN_REQUEST, RETURN_PICKUP, DEPOT_IN, SLOT_ASSIGNED, EXPORT_LOAD, TERMINAL_DROP, RETURNED
    event_at TIMESTAMPTZ DEFAULT NOW(),
    location_id UUID REFERENCES location(id),
    delivery_id UUID REFERENCES delivery(id),
    actor_user_id UUID REFERENCES app_user(id),
    metadata JSONB                                 -- flexible additional data
);

CREATE INDEX idx_container_event_container ON container_event (container_id, event_at DESC);
CREATE INDEX idx_container_event_type ON container_event (event_type, event_at);
```

### Yard Slot (CYMS)

```sql
CREATE TABLE yard_slot (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    code VARCHAR(30) UNIQUE NOT NULL,             -- "A.05.03.2" = Block A, Bay 5, Row 3, Tier 2
    block CHAR(1) NOT NULL,                       -- 'A', 'B', 'C'
    bay_no INT NOT NULL,
    row_no INT NOT NULL,
    tier_no INT NOT NULL,                         -- 1=ground, 2=stacked, 3=top
    
    -- Capacity
    max_size VARCHAR(10),                         -- '40FT' or '20FT'
    
    -- Current state
    is_occupied BOOLEAN DEFAULT FALSE,
    current_container_id UUID REFERENCES container(id),
    occupied_since TIMESTAMPTZ,
    
    -- Geo (untuk yard map UI)
    grid_x INT,
    grid_y INT,
    
    is_active BOOLEAN DEFAULT TRUE
);

CREATE INDEX idx_yard_slot_occupied ON yard_slot (is_occupied, block);
CREATE INDEX idx_yard_slot_container ON yard_slot (current_container_id) WHERE is_occupied = TRUE;
```

### Fuel Event

```sql
CREATE TABLE fuel_event (
    id BIGSERIAL PRIMARY KEY,
    vehicle_id UUID NOT NULL REFERENCES vehicle(id),
    driver_id UUID REFERENCES driver(id),
    
    event_type VARCHAR(20) NOT NULL,              -- 'REFUEL', 'CONSUMPTION_DROP', 'SENSOR_ANOMALY', 'TAMPER_ALERT'
    event_at TIMESTAMPTZ DEFAULT NOW(),
    
    -- Refuel details
    liters DECIMAL(8,2),
    cost_idr DECIMAL(10,2),
    fuel_station VARCHAR(100),
    receipt_url TEXT,
    fuel_card_used VARCHAR(50),
    
    -- Sensor reading
    sensor_level_before DECIMAL(8,2),             -- liters
    sensor_level_after DECIMAL(8,2),
    sensor_drop_l DECIMAL(8,2),                   -- positive = drop
    
    -- Location at event
    geom GEOGRAPHY(POINT, 4326),
    odometer_km DECIMAL(10,2),
    
    -- Triangulation result
    is_anomaly BOOLEAN DEFAULT FALSE,
    anomaly_reason VARCHAR(200),
    investigation_status VARCHAR(20),             -- NULL, OPEN, REVIEWED, CONFIRMED_FRAUD, FALSE_POSITIVE
    
    delivery_id UUID REFERENCES delivery(id)
);

CREATE INDEX idx_fuel_event_vehicle ON fuel_event (vehicle_id, event_at DESC);
CREATE INDEX idx_fuel_event_anomaly ON fuel_event (is_anomaly, event_at DESC) WHERE is_anomaly = TRUE;
```

### GPS Ping (Time-Series)

```sql
CREATE TABLE gps_ping (
    id BIGSERIAL,
    vehicle_id UUID NOT NULL REFERENCES vehicle(id),
    
    pinged_at TIMESTAMPTZ DEFAULT NOW(),
    geom GEOGRAPHY(POINT, 4326) NOT NULL,
    speed_kmh DECIMAL(5,1),
    heading INT,                                  -- 0-359
    ignition_on BOOLEAN,
    odometer_km DECIMAL(10,2),
    
    -- Aggregated indicators
    is_harsh_brake BOOLEAN DEFAULT FALSE,
    is_speeding BOOLEAN DEFAULT FALSE,
    
    PRIMARY KEY (vehicle_id, pinged_at)
) PARTITION BY RANGE (pinged_at);

-- Monthly partitions for performance + retention
CREATE TABLE gps_ping_2026_05 PARTITION OF gps_ping
    FOR VALUES FROM ('2026-05-01') TO ('2026-06-01');

CREATE INDEX idx_gps_vehicle_time ON gps_ping (vehicle_id, pinged_at DESC);
CREATE INDEX idx_gps_geom ON gps_ping USING GIST (geom);
```

**Retention strategy:** keep raw GPS pings 90 hari, aggregate ke daily summary table (per vehicle) untuk historical.

### Gate Event (RFID UHF)

```sql
CREATE TABLE gate_event (
    id BIGSERIAL PRIMARY KEY,
    gate_id VARCHAR(50) NOT NULL,                 -- 'DEPO_IRN_GATE_1'
    detected_at TIMESTAMPTZ DEFAULT NOW(),
    direction VARCHAR(10),                        -- 'IN', 'OUT'
    
    -- Tags detected (1 event = multi tags)
    driver_id UUID REFERENCES driver(id),
    vehicle_id UUID REFERENCES vehicle(id),
    chassis_id UUID REFERENCES chassis(id),
    
    -- Raw detection
    raw_tags JSONB,                               -- list of EPC codes detected
    rssi_data JSONB,                              -- signal strength per tag
    
    -- Triangulation outcome
    is_complete_triplet BOOLEAN,                  -- true kalau driver+vehicle+chassis lengkap
    is_anomaly BOOLEAN DEFAULT FALSE,             -- e.g., chassis terdeteksi tanpa driver/vehicle
    anomaly_reason VARCHAR(200)
);

CREATE INDEX idx_gate_event_time ON gate_event (detected_at DESC);
CREATE INDEX idx_gate_event_driver ON gate_event (driver_id, detected_at DESC);
CREATE INDEX idx_gate_event_chassis ON gate_event (chassis_id, detected_at DESC);
```

### Cert SILO/SIO Tracker

```sql
CREATE TABLE cert_compliance (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    entity_type VARCHAR(20) NOT NULL,             -- 'vehicle', 'driver'
    entity_id UUID NOT NULL,
    cert_type VARCHAR(20) NOT NULL,               -- 'STNK', 'KIR', 'SILO', 'SIA', 'SIO', 'SIM', 'INSURANCE_CAR', 'INSURANCE_TPL'
    cert_number VARCHAR(50),
    issue_date DATE,
    expires_at DATE NOT NULL,
    issuer VARCHAR(100),
    file_url TEXT,
    
    -- Alert state
    last_alert_60_at TIMESTAMPTZ,                 -- 60 hari sebelum expire
    last_alert_30_at TIMESTAMPTZ,
    last_alert_7_at TIMESTAMPTZ,
    
    -- Renewal tracking
    renewed_to_id UUID REFERENCES cert_compliance(id),
    
    notes TEXT,
    deleted_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_cert_entity ON cert_compliance (entity_type, entity_id);
CREATE INDEX idx_cert_expires_alerting ON cert_compliance (expires_at)
    WHERE deleted_at IS NULL AND expires_at > NOW();
```

### Cashbond Ledger

```sql
CREATE TABLE cashbond (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    driver_id UUID NOT NULL REFERENCES driver(id),
    
    requested_at TIMESTAMPTZ DEFAULT NOW(),
    amount_idr DECIMAL(10,2) NOT NULL,
    reason VARCHAR(200) NOT NULL,                 -- 'BBM_advance', 'family_emergency', 'tools', 'kebutuhan_rutin', 'other'
    reason_detail TEXT,
    
    approved_by UUID REFERENCES app_user(id),
    approved_at TIMESTAMPTZ,
    paid_at TIMESTAMPTZ,
    
    -- Repayment
    deduction_pct DECIMAL(5,4) DEFAULT 0.30,      -- potong 30% dari komisi per trip
    paid_amount_idr DECIMAL(10,2) DEFAULT 0,
    outstanding_idr DECIMAL(10,2) GENERATED ALWAYS AS (amount_idr - paid_amount_idr) STORED,
    
    status VARCHAR(20) NOT NULL,                  -- DRAFT, APPROVED, PAID, DEDUCTING, SETTLED, WRITTEN_OFF
    
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_cashbond_driver ON cashbond (driver_id, status);
CREATE INDEX idx_cashbond_outstanding ON cashbond (driver_id) WHERE status IN ('APPROVED', 'PAID', 'DEDUCTING');
```

### Audit Log (Pattern)

```sql
CREATE TABLE audit_log (
    id BIGSERIAL PRIMARY KEY,
    table_name VARCHAR(100) NOT NULL,
    record_id UUID NOT NULL,
    action VARCHAR(20) NOT NULL,                  -- 'INSERT', 'UPDATE', 'DELETE'
    
    actor_user_id UUID REFERENCES app_user(id),
    actor_ip VARCHAR(45),
    
    old_data JSONB,
    new_data JSONB,
    changed_fields TEXT[],
    
    occurred_at TIMESTAMPTZ DEFAULT NOW()
) PARTITION BY RANGE (occurred_at);

-- Use Postgres trigger for auto-populate
```

---

## Soft Delete Pattern

Setiap entity utama punya `deleted_at TIMESTAMPTZ`. Default queries pakai `WHERE deleted_at IS NULL`. Soft delete preserves audit trail (penting untuk compliance + dispute).

**Hard delete** hanya untuk:
- GDPR/PDP "right to be forgotten" requests
- Data >7 tahun (per regulasi tax)

---

## Anti-Patterns

❌ **VARCHAR untuk semua ID** — pakai UUID atau BIGSERIAL. UUID safer untuk multi-tenant masa depan.

❌ **Tidak pakai PostGIS untuk geo** — geo query (radius, polygon contains) jadi pain. Pakai PostGIS dari awal.

❌ **Single big delivery table tanpa partition** — saat data >5 tahun, partition by year.

❌ **Soft delete tanpa indexing `deleted_at`** — query slow saat data growth.

❌ **Sensor data direct insert ke Postgres tanpa partitioning** — GPS ping 50 vehicles × 1 ping/30sec × 30 hari = 4.3M rows/bulan. Wajib partition.

❌ **JSONB everything** — kalau struktur stabil, jadikan kolom typed. JSONB hanya untuk variable metadata.

---

## Cross-References

- Driver app data sync → `03-driver-mobile-app-architecture.md`
- Sensor data ingestion → `05-anti-fraud-tech-stack.md` + `06-rfid-uhf-attendance-asset.md`
- Yard slot allocation logic → `07-container-yard-management.md`
- Integration external API persistence → `09-integration-ai-ml-layer.md`
- CEO AI Assistant data flow (LOCK-3) → `11-ceo-ai-assistant-architecture.md` Section 4
- Anti-fraud-with-dignity workflow tables (LOCK-5) → `10-anti-fraud-with-dignity.md`

---

## Addendum (Day R3, 2026-05-11) — CEO AI Assistant Schema Additions (LOCK-3)

> **Anchor — LOCK-3 flagship:** 3 new tables required Phase 1 untuk CEO AI Assistant. Audit-defensible, Coretax-compliance-ready, partitionable for >100k turn growth.

### Table 1 — `asisten_conversation_log` (chat + tool-use audit trail)

```sql
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

CREATE INDEX idx_asisten_log_owner_session ON asisten_conversation_log (owner_id, session_id, turn_index);
CREATE INDEX idx_asisten_log_action ON asisten_conversation_log (action_taken) WHERE action_taken IS NOT NULL;
CREATE INDEX idx_asisten_log_created_at ON asisten_conversation_log (created_at);

-- Partitioning recommended once table exceeds 1M rows (monthly partition by created_at)
```

**Columns explained:**
- `role`: who produced the turn — user (owner asking), asisten (LLM response), tool (tool-call output), system (initial prompt / context injection)
- `tool_name`: e.g., `get_fuel_anomaly_alerts`, `hold_invoice` — see `11-ceo-ai-assistant-architecture.md` Section 3.1
- `permission_check`: `owner-confirmed:Y`, `owner-confirmed:N`, `auto-approved:read-only`, `auto-blocked:scope-violation`
- `override_reason`: populated when owner uses force-confirm-fraud override path (LOCK-5)

**Compliance:** Coretax audit-ready. Every `action_taken` that affects financial records (hold_invoice, etc.) has full trace.

### Table 2 — `asisten_alert_queue` (proactive mode + delivery tracking)

```sql
CREATE TABLE asisten_alert_queue (
  id BIGSERIAL PRIMARY KEY,
  owner_id INTEGER NOT NULL REFERENCES owner(id),
  alert_type TEXT NOT NULL,
  severity TEXT NOT NULL CHECK (severity IN ('info','warn','urgent')),
  payload JSONB NOT NULL,
  sent_at TIMESTAMPTZ,
  delivered_at TIMESTAMPTZ,
  read_at TIMESTAMPTZ,
  owner_action TEXT,
  responded_at TIMESTAMPTZ,
  related_entity_type TEXT,
  related_entity_id BIGINT,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_asisten_alert_owner_severity ON asisten_alert_queue (owner_id, severity, created_at DESC);
CREATE INDEX idx_asisten_alert_unactioned ON asisten_alert_queue (owner_id, created_at) WHERE owner_action IS NULL;
CREATE INDEX idx_asisten_alert_related ON asisten_alert_queue (related_entity_type, related_entity_id);
```

**`alert_type` enum (Phase 1):**
- `fuel_anomaly` — links to `fuel_anomaly_alert` (anti-fraud)
- `attendance_late` — links to `attendance_rfid_log`
- `customer_urgent` — links to `customer_communication`
- `coretax_sync_fail` — links to `coretax_sync_log`
- `calendar_reminder` — religious calendar (Imlek, Eid, etc.)
- `eta_deviation` — links to `delivery` with predicted vs actual gap >threshold

**`owner_action` enum:** `Y`, `N`, `snoozed`, `escalated`, `dismissed` — null if no response yet

### Table 3 — `asisten_owner_preference` (memory layer)

```sql
CREATE TABLE asisten_owner_preference (
  owner_id INTEGER NOT NULL REFERENCES owner(id),
  preference_key TEXT NOT NULL,
  preference_value JSONB NOT NULL,
  source TEXT NOT NULL CHECK (source IN ('persona_validation','learned','owner_explicit')),
  last_updated TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  PRIMARY KEY (owner_id, preference_key)
);
```

**Seed `preference_key` set (Phase 1, from persona validation interview Q1-15):**

| Key | Value Shape | Default | Seeded From |
|---|---|---|---|
| `greeting` | `{"honorific": "<bound from `{{owner_honorific}}`>", "language": "<bound from `{{owner_language_secondary}}`>"}` | `{"honorific": "Bapak", "language": "id"}` | project-variables |
| `unit_volume` | `"liter"` or `"galon"` | `"liter"` | Q11 |
| `unit_distance` | `"km"` or `"mil"` | `"km"` | Q11 |
| `currency_format` | `{"separator": ".", "decimal": ","}` (Indonesian standard) | Indonesian default | Q11 |
| `working_hours_start` | `"06:00"` (string HH:MM) | `"08:00"` | Q8 |
| `working_hours_end` | `"19:00"` | `"18:00"` | Q8 |
| `quiet_hours` | `[{"start":"22:00","end":"06:00"}]` (array of windows) | overnight default | Q8 |
| `religious_calendar` | `["imlek","cap_go_meh","qingming","vesak"]` | empty array | Q9, Q3 |
| `language_secondary` | `"hokkien"` or `"mandarin"` or null | null | Q5 |
| `response_length` | `"brief"` or `"detailed"` | `"brief"` | Q12 |
| `proactive_alert_threshold` | `{"fuel_gap_liters": 10, "attendance_late_min": 30}` | defaults from architecture | learned over time |

**Why JSONB for `preference_value`:** future-proof — new preference keys without schema migration.

### Cross-Table Relationships

```
owner (existing)
  ├── 1:N asisten_conversation_log (audit trail)
  ├── 1:N asisten_alert_queue (proactive mode)
  └── 1:N asisten_owner_preference (memory layer)

asisten_alert_queue
  └── N:1 → related_entity (polymorphic: fuel_anomaly_alert / attendance_rfid_log / customer_communication / delivery / etc.)

asisten_conversation_log
  └── (logical link via tool_name + tool_input/output to existing ops tables — no FK to preserve LLM flexibility)
```

### Retention + Partitioning

| Table | Retention | Partition Strategy |
|---|---|---|
| `asisten_conversation_log` | 7 years (Coretax audit compliance) | Monthly partition by `created_at` once >1M rows |
| `asisten_alert_queue` | 2 years | Monthly partition once >500k rows |
| `asisten_owner_preference` | Indefinite (small table, <100 rows) | No partition needed |

### Security + Access

- All 3 tables: row-level security (RLS) by `owner_id` — owner sees only own data
- Phase 2 admin/accountant sub-account: scope-filtered access via Postgres RLS policy
- Backup: daily incremental + weekly full to off-site (Jakarta cloud)
- Encryption at rest: PostgreSQL TDE or filesystem-level

### Phase 2 Schema Extensions (NOT in Phase 1)

```sql
-- Voice call session tracking (Phase 2)
CREATE TABLE asisten_voice_session (
  id BIGSERIAL PRIMARY KEY,
  conversation_log_session_id UUID NOT NULL,
  twilio_call_sid TEXT,
  whisper_stt_duration_ms INTEGER,
  tts_voice_id TEXT,
  total_duration_sec INTEGER,
  cost_idr NUMERIC(12,2),
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Sub-account scope (admin/accountant Phase 2)
CREATE TABLE asisten_subaccount_scope (
  id BIGSERIAL PRIMARY KEY,
  owner_id INTEGER NOT NULL REFERENCES owner(id),
  subaccount_user_id INTEGER NOT NULL REFERENCES user_account(id),
  scope_tools TEXT[] NOT NULL,  -- whitelist of tool_name accessible
  scope_entities JSONB,         -- e.g., {"vehicle_ids": [1,2,5], "customer_ids": "all"}
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

Defer Phase 2 table creation until Phase 1 stable + scope requirements validated via user testing.
