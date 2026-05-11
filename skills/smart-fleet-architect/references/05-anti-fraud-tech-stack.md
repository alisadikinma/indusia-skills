# Pillar 05 — Anti-Fraud Tech Stack: Phone-as-GPS + ESP32 BLE Fuel Sensor

## Final Architecture (IRN — 23 Vehicles + 90 Chassis)

```
┌─ TRUK ─────────────────────────────────────────────────┐
│                                                         │
│   📱 HP Driver (Flutter app)  ◄── PRIMARY GATEWAY       │
│   ├── GPS continuous (60s ping) → backend MQTT         │
│   ├── Photo + ePoD + signature canvas                  │
│   ├── BLE listener untuk ESP32                         │
│   ├── Geofence auto-prompt                             │
│   └── Foreground service (wajib Flutter, bukan PWA)    │
│                                                         │
│              ▲ BLE 5.0 (10-30m range)                   │
│              │                                          │
│   🔧 ESP32-S3 (NO SIM, BLE only)                        │
│   ├── Power: 12V truk → buck 3.3V                      │
│   ├── Read: 240-33 Ohm resistive fuel sensor (ADC)     │
│   ├── Local buffer flash (1-2 minggu data offline)     │
│   ├── Tamper switch microswitch (enclosure cover)      │
│   ├── BLE pair to driver phone + yard gateway          │
│   └── Sync via BLE saat phone/gateway reconnect        │
│                                                         │
└─────────────────────────────────────────────────────────┘
                       │
                       │ BLE relayed via 2 paths:
                       │  Path A: driver HP → MQTT (on-road)
                       │  Path B: yard gateway → ethernet (depot)
                       ▼
       ┌─ BACKEND TRIANGULATION ENGINE ──┐
       │                                  │
       │   Phone GPS stream (location)    │
       │            ⨯                     │
       │   ESP32 fuel stream (deferred OK)│
       │            =                     │
       │   Multi-source ground truth      │
       └──────────────────────────────────┘

YARD GATEWAY (kantor IRN):
  Raspberry Pi 4 atau ESP32 hub + antena BLE long-range
  Posisi: gerbang depot + parking area
  Function: auto-scan semua ESP32 truk yang masuk yard,
            pull backlog buffer, push ke backend via ethernet/wifi.
            Catch sync data saat driver HP mati / off-duty.
```

**Key decisions (locked):**
- ✅ GPS via phone — NO Teltonika hardware (Gojek pattern)
- ✅ Fuel sensor: **240-33 Ohm resistive float-type** (Storeyuuhh atau equivalent, ~Rp 250-500k)
- ✅ ESP32-S3 + BLE bridge — NO SIM module (saves CapEx + OpEx)
- ✅ Dual BLE gateway: **driver HP (mobile) + yard station (fixed di kantor)**
- ✅ All 23 vehicles deploy sekaligus (Day 1 hardware), soft-launch sanction (Month 3+)

---

## Hardware Selection — Final BoM

### Fuel Level Sensor

| Model | Type | Akurasi | Interface | Harga (Rp) | Pilihan IRN |
|---|---|---|---|---|---|
| **Storeyuuhh 240-33 Ohm resistive float** (atau equivalent) | Resistive float (automotive standard) | ±5-10% | Analog 240-33 Ohm | 250-500k | ⭐ **PILIH** — murah, ESP32 ADC langsung baca, wiring familiar bengkel Batam |
| Escort TD-150 | Capacitive tube | ±1.5% | RS232 wired | 1.5-2.5jt | Skip — overkill 5-10× harga untuk gain akurasi marginal |
| Omnicomm LLS 5 | Capacitive | ±1% | RS485 | 2.0-3.0jt | Skip — overkill untuk IRN scale |
| Omnicomm LLS 6 AI | Capacitive + AI | ±0.5% | BLE/RS485 | 3.5-5jt | Skip — Phase 3+ only |

**Pilih:** 240-33 Ohm resistive float sensor. Standar automotive (factory fuel gauge sender). ESP32-S3 baca via ADC + voltage divider — no transceiver chip needed.

**Akurasi trade-off explicit:**
- Capacitive ±1.5% → bisa detect drop **3L** dari tank 200L
- Resistive ±5-10% → reliably detect drop **>10L** dari tank 200L (filter via rolling average 5-min window)
- Kencing solar tipikal di Batam: 10-30L per kejadian → **masih ter-detect** dengan resistive
- Loss: kasus kencing 3-8L (jarang, mostly opportunistic mini-skim) akan miss
- Mitigation: backup via STATISTICAL_FRAUD_OUTLIER 30-day rolling (catch akumulasi)

### ESP32 Bridge Module — DIY Build

| Component | Spec | Harga/unit (Rp) |
|---|---|---|
| **ESP32-S3 DevKit** (BLE 5 built-in, more memory than ESP32) | 8MB flash, 2MB PSRAM | 80-120k |
| **Buck converter 12V→3.3V** | LM2596 atau MP1584 module | 30-50k |
| **Voltage divider resistor pair** | 1% precision, untuk ADC scaling 0-3.3V dari 240-33 Ohm | 5-10k |
| **Tamper switch microswitch** | Normally-open, pressed when enclosure closed | 20-30k |
| **Enclosure IP65** | ABS plastic, cable gland 2-port | 80-150k |
| **Wiring harness** | shielded 3-core cable to fuel sensor | 50-80k |
| **TOTAL ESP32 stack/unit** | | **265-440k** |

### Yard Gateway (One-Time, Shared)

| Component | Spec | Harga (Rp) |
|---|---|---|
| Raspberry Pi 4 (4GB) + case + PSU | atau ESP32-S3 hub kalau preferred minimal | 1.2-1.8jt |
| Long-range BLE antenna (yagi atau directional) | Coverage ~50-100m yard area | 300-500k |
| Software: BLE scanner daemon + MQTT relay | Open-source Python service | (dev internal) |
| Network: ethernet ke kantor router | Existing infra | 0 |
| **Subtotal yard gateway** | | **1.5-2.3jt** |

Tempatkan 1-2 gateway di gerbang depot + parking area → auto-pull buffer setiap kali truk masuk yard.

### Total Per-Vehicle Cost

| Item | Per Vehicle (Rp) |
|---|---|
| Fuel sensor 240-33 Ohm | 400,000 (avg) |
| ESP32 stack (above) | 350,000 (avg) |
| Install + wiring (1 hari teknisi) | 400,000 |
| **Subtotal per vehicle** | **~Rp 1,150,000** |

### 23 Vehicles Phase 1 Deployment

| Item | Cost |
|---|---|
| Hardware × 23 vehicles | Rp 26.5jt |
| Yard gateway (2 units) | Rp 4jt |
| Firmware development (one-time) | Rp 25-40jt |
| Yard gateway software (one-time) | Rp 10-15jt |
| Cloud backend setup (already in app stack) | included |
| **TOTAL Phase 1 CapEx** | **~Rp 65-86jt** |

**Saving vs Escort TD-150 architecture:** ~Rp 30-40jt cheaper untuk full 23-vehicle deployment.

---

## ESP32 Firmware Architecture

### Specification

```
Platform: ESP-IDF 5.x (atau Arduino-ESP32 framework)
Language: C/C++ (ESP-IDF) atau C++ (Arduino)
RTOS: FreeRTOS (ESP-IDF default)

CORE TASKS:
  Task 1: Sensor reading (every 30s)
    └── ADC sample voltage divider (240-33 Ohm sensor)
    └── Apply rolling median filter (5-sample window) → reject spike noise
    └── Map ADC → liters via per-tank calibration curve (lookup table)
    └── Store to flash buffer (CircularBuffer)
    
  Task 2: BLE GATT server (dual-target: phone + yard gateway)
    └── Advertising name: "IRN-FUEL-{VIN}"
    └── Custom GATT service UUID
    └── Characteristics:
        - Fuel level (read/notify)
        - Buffer count (read)
        - Buffer dump (read, paginated)
        - Tamper status (read/notify)
        - Last sync timestamp (read/write)
    └── Accept connection dari driver phone (paired) OR yard gateway (paired)
    
  Task 3: Tamper detection
    └── Watch microswitch GPIO
    └── If switch released → tamper event → store to buffer + notify BLE
    
  Task 4: Power management
    └── Light sleep saat idle
    └── Wake on RTC timer atau BLE event
    └── Watchdog reset kalau hang

LOCAL BUFFER:
  Format: timestamp + fuel_level + tamper_flag + odometer_proxy
  Size: 4MB allocated → ~1.5 minggu @ 30s polling
  Strategy: FIFO overwrite kalau penuh
```

### Tamper-Resistant Build

| Threat | Mitigation |
|---|---|
| Sopir flash ulang firmware | Locked bootloader (eFuse), no UART debug pin exposed in production |
| Sopir cabut ESP32 | Tamper switch trigger event saved to flash + BLE notify |
| Sopir cabut sensor cable | ADC reading saturated (open circuit = max V atau min V) → flag in event log |
| Sopir buka enclosure | Microswitch in lid releases → tamper event |
| Sopir spoof BLE (replay attack) | HMAC signing dengan secret key per device + nonce sequence |
| Sopir block BLE saat kencing solar | ESP32 tetap log to flash → backend dapat backlog saat phone reconnect |

### MQTT Message Format (Relayed via Phone)

```json
{
  "device_id": "IRN-ESP32-001",
  "vehicle_id": "PM22-B9281SEHH",
  "events": [
    {
      "ts": 1715342400,
      "fuel_level": 78.5,
      "fuel_level_unit": "percent",
      "tamper": false
    },
    {
      "ts": 1715342430,
      "fuel_level": 78.4,
      "tamper": false
    }
  ],
  "buffer_remaining": 1247,
  "ble_signal_rssi": -54,
  "firmware_version": "1.0.3"
}
```

Phone app:
1. BLE polling ESP32 every 30s when connected
2. Receive batch (kalau buffer ada)
3. Push ke backend via MQTT broker
4. ACK back to ESP32 (mark as synced, can clear buffer)

Yard gateway (Raspberry Pi):
1. Scan BLE advertisement continuously (whitelist IRN-FUEL-* devices)
2. Saat truk masuk yard → auto-connect → pull buffer
3. Push batch ke backend via MQTT (ethernet)
4. ACK back → truk siap untuk trip berikutnya dengan buffer kosong
5. Key win: capture data dari sopir yang HP-nya mati / off-duty period

---

## Triangulation Rule Engine (Updated for IRN Architecture)

### Rule 1 — FUEL_THEFT_CANDIDATE (HIGH severity)

```python
def fuel_theft_candidate(context):
    """
    GPS stationary + fuel level drop + bukan di customer/depot/SPBU
    
    Note: works EVENTUALLY karena fuel data via phone (deferred saat phone offline).
    """
    v = context['vehicle']
    window = timedelta(minutes=15)
    end_time = context['now']
    start_time = end_time - window
    
    # GPS check (from phone)
    gps_pings = get_gps_pings(v.id, start_time, end_time)
    if not gps_pings:
        return None  # phone offline, can't evaluate
    
    if not all(p.speed_kmh < 5 for p in gps_pings):
        return None
    
    # Fuel check (from ESP32 via phone OR yard gateway relay)
    # Threshold tuned untuk resistive sensor ±5-10% noise floor
    fuel_drop = get_fuel_drop(v.id, start_time, end_time)
    if fuel_drop < 10:  # liters — raised from 5 to filter resistive sensor noise
        return None
    
    # Location classification
    last_geom = gps_pings[-1].geom
    location_type = classify_location(last_geom)
    if location_type in ['customer', 'depot', 'fuel_station', 'workshop']:
        return None
    
    return {
        'rule': 'FUEL_THEFT_CANDIDATE',
        'severity': 'HIGH',
        'vehicle_id': v.id,
        'driver_id': v.current_driver_id,
        'evidence': {
            'duration_stationary_min': window.seconds / 60,
            'location': last_geom,
            'fuel_drop_liters': fuel_drop,
            'detection_latency_min': calc_latency(end_time, fuel_data_received_at)
        }
    }
```

### Rule 2 — APP_DROPOUT_DURING_TRIP (NEW — KEY for phone-based architecture)

```python
def app_dropout_during_trip(context):
    """
    Driver phone offline >X minutes during scheduled trip = anomaly
    
    Why: kalau sopir mati'in HP saat kerja, suspicious. Bisa untuk hide
    fuel theft, fake route, atau lain.
    """
    v = context['vehicle']
    
    # Is there an active trip?
    active_trip = get_active_trip(v.id, context['now'])
    if not active_trip:
        return None  # OK, sopir off-duty
    
    # Last GPS ping
    last_ping = get_last_gps_ping(v.id)
    minutes_since_ping = (context['now'] - last_ping.pinged_at).seconds / 60
    
    if minutes_since_ping > 5:
        severity = 'MEDIUM' if minutes_since_ping < 30 else 'HIGH'
        return {
            'rule': 'APP_DROPOUT_DURING_TRIP',
            'severity': severity,
            'vehicle_id': v.id,
            'driver_id': active_trip.driver_id,
            'trip_id': active_trip.id,
            'evidence': {
                'minutes_offline': minutes_since_ping,
                'last_known_location': last_ping.geom,
                'trip_status': active_trip.status,
                'suggested_action': 'contact_driver_immediately'
            }
        }
```

### Rule 3 — ESP32_TAMPER (CRITICAL severity)

```python
def esp32_tamper(context):
    """
    Tamper switch triggered or BLE silent >24 jam during active service.
    """
    latest_esp_event = context.get('latest_esp32_event')
    
    if latest_esp_event and latest_esp_event['tamper'] == True:
        return {
            'rule': 'ESP32_TAMPER',
            'severity': 'CRITICAL',
            'vehicle_id': latest_esp_event['vehicle_id'],
            'evidence': {
                'tamper_at': latest_esp_event['ts'],
                'last_known_fuel_level': latest_esp_event['fuel_level']
            }
        }
    
    # No event >24h?
    last_event_age = check_esp32_silent_duration(latest_esp_event)
    if last_event_age > 24:  # hours
        return {
            'rule': 'ESP32_TAMPER',
            'severity': 'HIGH',
            'evidence': {
                'silent_hours': last_event_age,
                'possible_cause': 'tamper / broken / phone-not-paired'
            }
        }
```

### Rule 4 — STATISTICAL_FRAUD_OUTLIER (rolling 30-day, catch-all)

```python
def statistical_fraud_outlier(context):
    """
    Kalau sopir's km/L 30-day rolling >2 sigma di bawah fleet average,
    flag as needs investigation regardless of real-time alert.
    
    KEY: ini catch sopir sophisticated yang bisa hide real-time detection
    (matiin HP saat kencing) — dia akan tetap kelihatan via stat anomaly.
    """
    fleet_kmpl_mean, fleet_kmpl_std = get_fleet_30day_stats()
    driver_kmpl = get_driver_30day_kmpl(context['driver_id'])
    
    z_score = (driver_kmpl - fleet_kmpl_mean) / fleet_kmpl_std
    
    if z_score < -2.0:  # significantly below mean
        return {
            'rule': 'STATISTICAL_FRAUD_OUTLIER',
            'severity': 'MEDIUM',
            'driver_id': context['driver_id'],
            'evidence': {
                'driver_kmpl': driver_kmpl,
                'fleet_mean': fleet_kmpl_mean,
                'z_score': z_score,
                'investigation_recommended': True
            }
        }
```

---

## Trade-off Acknowledgment

This architecture has **deferred detection weakness** — kalau sopir matikan HP saat kencing solar, real-time alert tidak masuk. Mitigations:

| Risk | Mitigation Layer |
|---|---|
| Sopir matiin HP saat kencing | **Layer 1**: ESP32 buffer 1-2 minggu → backend dapat backlog saat phone reconnect ATAU saat truk masuk yard (auto-sync via gateway), anomaly visible |
| | **Layer 2**: APP_DROPOUT_DURING_TRIP rule → flag immediately, dispatcher contact sopir |
| | **Layer 3**: STATISTICAL_FRAUD_OUTLIER 30-day rolling — akumulasi pasti ketahuan |
| | **Layer 4**: **Yard gateway auto-pull saat parking** — truk masuk depot otomatis sync, sopir tidak bisa avoid |
| | **Layer 5**: Sanction matrix in driver contract: "HP offline >2 jam saat trip = potong komisi 10%" |
| | **Layer 6**: Bonus "Perfect Sync Day" Rp 5-10k/hari → economic incentive keep online |
| Sopir cabut ESP32 hardware | Tamper microswitch + signed firmware + locked bootloader |
| Sopir spoof BLE replay | HMAC + nonce sequence per message |
| Resistive sensor noise spike false-positive | Rolling median filter on-device + threshold 10L not 5L + cross-validate dengan GPS stationary state |

**Realitanya:** 90% kasus kencing solar = opportunistic, tidak sophisticated. Layer 1 + 2 sudah enough catch. 10% sophisticated = layer 3 catches them via statistical pattern over 30 days.

---

## Sanction Matrix (Updated)

| Pelanggaran | First Time | Second (in 6 mo) | Third |
|---|---|---|---|
| FUEL_THEFT_CANDIDATE confirmed | Verbal warning + denda Rp 500k + 30-day monitoring | Suspended 7 hari + denda Rp 1jt | Terminate |
| APP_DROPOUT >2h saat trip (no valid reason) | Komisi potong 10% trip itu | SP1 + 60-day monitoring | Terminate |
| ESP32_TAMPER (sengaja) | SP1 + denda Rp 1jt | Terminate |
| Statistical outlier confirmed | Coaching + investigation | SP1 + improvement plan | Terminate |

**Critical:** sanction matrix HARUS di-disclose di kontrak driver dari awal. Konsisten enforcement = trust + deterrent.

---

## Bonus Structure (Counter-Balance)

| Bonus | Trigger | Amount |
|---|---|---|
| **Perfect Sync Day** | App + ESP32 BLE sync uninterrupted sepanjang scheduled trip | Rp 5-10k/hari |
| **Fuel Efficiency Bonus** | km/L >fleet avg + 10% over 30 days | Rp 100-300k/bulan |
| **Zero Incident Quarter** | No anti-fraud alert in 90 days | Rp 500k-1jt one-time |
| **Career Ladder Promotion** | Senior status after 12 month clean record | Komisi % increase |

---

## Phase Roadmap

### Month 1 — Hardware Deployment

```
□ Order 240-33 Ohm fuel sensors × 23 (1 spare per 5)
□ Build ESP32-S3 boards × 23 (atau outsource ke EMS local Batam)
□ Build yard gateway (Raspberry Pi + antenna) × 2 units
□ Firmware v1.0 finalized + tested di 2 prototype truk
□ Per-tank calibration curve (isi penuh → kuras → kalibrasi ADC ke liter mapping)
□ Install all 23 vehicles (1-2 minggu, 5 teknisi parallel)
□ Yard gateway deployed di gerbang + parking area kantor IRN
□ BLE pairing per phone-vehicle + gateway whitelist
□ Driver training session (1-2 jam per driver)
```

### Month 2 — Soft Launch (Data Collection Mode)

```
□ Triangulation rules ACTIVE tapi alert hanya ke dispatcher
□ NO sanction yet — data collection only
□ Calibrate threshold (false-positive rate target <15%)
□ Tune Q-value, BLE polling interval, fuel drop threshold
□ Bonus structure activate (positive incentive)
```

### Month 3 — Sanction Activation

```
□ Driver contract update + signing (sanction matrix disclosed)
□ Sanction enforcement begin
□ Monthly fraud report to owner
□ Dashboard mature
```

### Month 4+ — Continuous Improvement

```
□ Add new rules berdasar pattern observed
□ Statistical outlier detection mature (need 60 days data)
□ Driver leaderboard public (peer pressure positive)
□ Monthly KPI review per driver
```

---

## ROI Calculation (23 Vehicles, Resistive Sensor Architecture)

| Item | Value |
|---|---|
| Estimated kencing solar before: 30% × 23 sopir × Rp 691k/bulan | Rp 4.77jt/bulan = **Rp 57.2jt/year** |
| Recovery rate (assume 65% — sedikit lebih rendah dari capacitive karena miss kasus <8L) | Rp 37.2jt/year saved |
| CapEx | Rp 65-86jt |
| Payback period | **17-23 bulan** |

**Honest assessment:** Payback ~1.5-2 tahun = solid business case. **Recommend strongly** karena:
1. Once installed, ROI continues for ~5-7 years lifecycle = ~Rp 180-260jt total saved
2. Behavioral effect — sopir yang TAU sistem ada, behavior berubah immediately (placebo deterrent ~30-40% reduction without any actual catch)
3. Positioning value — IRN bisa charge premium ke customer yang demand transparency
4. Cross-benefit: same hardware juga bantu maintenance prediction, fuel efficiency optimization
5. **Resistive sensor architecture survive replacement cost lebih murah** — kalau float macet, ganti Rp 400k bukan Rp 2jt

---

## Anti-Patterns

❌ **Pasang Teltonika hardware GPS terpisah** — duplicate dengan phone GPS, waste Rp 30-40jt CapEx untuk 23 unit. Phone cukup.

❌ **Beli sensor capacitive Escort/Omnicomm di Phase 1** — 5-10× harga vs resistive untuk akurasi marginal. Resistive cukup catch kencing solar >10L (mayoritas kasus). Upgrade ke capacitive Phase 3 kalau perlu detect skim <8L.

❌ **Pasang SIM module di ESP32** — tampak more reliable tapi adds Rp 200-350k/unit + Rp 30-50k/month/SIM. Yard gateway + phone bridge cukup.

❌ **Skip yard gateway** — bergantung 100% pada phone driver = sopir tinggal matiin HP untuk skip detection. Yard gateway = auto-sync saat parking, sopir tidak bisa avoid.

❌ **Skip per-tank calibration** — beda tank shape = beda mapping ADC→liter. Wajib calibration curve per vehicle saat install (isi penuh → kuras → record waypoint).

❌ **Skip rolling median filter** — resistive sensor noise spike akan trigger false-positive. On-device smoothing wajib sebelum kirim ke backend.

❌ **Skip soft-launch period** — langsung sanction Day 1 = false-positive akan bunuh trust. Wajib calibration phase Month 2.

❌ **AI dashcam di MVP** — overkill, deferred ke Phase 2+.

❌ **ML anomaly di MVP** — butuh 6+ bulan data quality dulu. Rule-based first.

---

## Cross-References

- Driver phone app + BLE listener → `03-driver-mobile-app-architecture.md`
- Mapping & GPS via phone (Gojek pattern detail) → `08-mapping-gps-tracking-stack.md`
- RFID gate triplet logging (complementary) → `06-rfid-uhf-attendance-asset.md`
- Driver psikologi root cause + sanction design → `pakar-logistik-batam/05-driver-control-antifraud.md`
- Tax treatment kasbon + sanction → `pakar-logistik-batam/08-tax-finance-invoicing.md`
- Anti-fraud-with-dignity workflow (Phase C, LOCK-5 cross-layer) → `10-anti-fraud-with-dignity.md` (NEW)
- CEO AI Assistant fraud alert routing (Phase C, LOCK-3) → `11-ceo-ai-assistant-architecture.md` (NEW)

---

## Addendum (Day R3, 2026-05-11) — WS8 Resilience + Cross-Layer Anti-Fraud Workflow

> **Anchor — Owner Gap #3:** Owner gak tau ada pencurian solar. This pillar = **IoT (resistive sensor) + AI (30-day rolling anomaly outlier)**. Owner gets full data alert; sopir gets clarification chance (24hr window) — see `10-anti-fraud-with-dignity.md` (NEW Phase C) for the full cross-layer workflow that preserves Tionghoa owner directness AND Indonesian sopir face-saving simultaneously.

### Infrastructure Resilience Requirement (WS8 LOCK)

| Risk | Lock | Implementation |
|---|---|---|
| **PLN brownouts monthly routine** di industrial estate Mukakuning / Tunas / Batamindo | Yard station UPS **Rp 800k-1.5jt MANDATORY** | UPS sebelum Raspberry Pi gateway + RFID reader. Tanpa UPS = fuel log + RFID log corruption during brownout window. Already locked in CLAUDE.md. |
| ESP32-S3 BLE bridge offline saat brownout | Battery-backed (built-in on ESP32 board + capacitor) | OK out-of-box, low risk |
| Driver phone BLE gateway offline saat mobil mati | Local buffer 5-30 min sync | Already in driver app spec |
| Single-point-of-failure yard station | Dual BLE gateway pattern (driver HP + yard Pi) | Already locked CLAUDE.md |

**Why monthly PLN brownout matters for anti-fraud:** Kalau yard station mati 30 menit tepat saat sopir gate-out, RFID triplet log (driver + vehicle + chassis) hilang. Fraud detection algorithm akan flag false-positive di hari brownout. UPS Rp 800k-1.5jt one-time = eliminate this attack vector + data integrity risk.

### Cross-Layer Anti-Fraud Workflow Preview (full spec → file 10 Phase C)

3-step workflow yang bridge Tionghoa owner directness ↔ Indonesian sopir face-saving:

1. **AI detect anomaly** (fuel gap ≥10L, fuel-stop without expected progress, etc.)
2. **Sopir gets QUIET clarification chance** — Asisten kirim WhatsApp ke sopir: *"Pak Rohim, ada catatan pengisian solar tadi pagi 50L tapi tank naik 35L. Bisa konfirmasi apakah ada drip / leak / penyusutan?"* — 24hr response window
3. **If unresolved after 24hr OR sopir confirms intentional** → "confirmed anomaly" surfaces ke owner dashboard + Asisten owner-alert + invoice fuel hold pending owner Y/N

**Why this matters (LOCK-5 cross-layer friction):**
- Tionghoa owner direct/to-the-point on fraud = OK, dia mau full data
- Indonesian sopir face-saving = preserve dignity, jangan langsung "diawasi seperti maling"
- Asisten = honest broker yang give sopir chance to explain BEFORE owner sees confirmed-anomaly flag
- Reduces false-positive friction (legitimate drip/leak/temp variation gets explained quietly, no owner-sopir confrontation needed)
- Preserves "owner-direct visibility" while preserving "sopir dignity"
