# Pillar 08 — Mapping & GPS Tracking Stack

## TL;DR Recommendation (REVISED — Phone-as-GPS / Gojek Pattern)

**For IRN (23 vehicles + 90 chassis, ~30k map loads/month):**

```
✅ GPS SOURCE:      Driver phone via Flutter app (NO hardware GPS device)  
                    → Rp 0 CapEx, Rp 0 OpEx hardware, save Rp 35-45jt vs Teltonika
                    
✅ MAPS DISPLAY:    Mapbox tiles ($5/10k loads)         → ~Rp 500k-1jt/bulan
✅ GEOCODING:       Nominatim self-hosted (OSM data)    → ~Rp 200-500k/bulan (server)
✅ ROUTING:         OSRM self-hosted (truck profile)    → ~Rp 300-500k/bulan (server)
✅ STORAGE:         PostgreSQL + PostGIS                → included in app DB
✅ MAP LIBRARY:     MapLibre GL JS (free)               → free

TOTAL OpEx:  Rp 1.0-2.0jt/bulan (untuk 23 vehicles)
TOTAL CapEx: ~Rp 0 (hardware-free GPS)

vs. Pure Google Maps + Teltonika hardware stack: Rp 4-8jt/bulan + Rp 35-45jt CapEx
SAVINGS:     Rp 35-45jt CapEx + Rp 30jt/year OpEx = ~Rp 100-150jt over 3 years
```

---

## Phone-as-GPS Architecture (Gojek Pattern)

### Why Phone Instead of Hardware GPS

For IRN scale 23 vehicles + driver-in-app workflow:

| Aspek | Phone (Gojek pattern) ⭐ | Hardware GPS (Teltonika) |
|---|---|---|
| CapEx 23 vehicles | Rp 0 | Rp 35-45jt |
| Monthly SIM data | Rp 0 (driver existing data) | Rp 1.2-1.8jt |
| Install effort | App install 5 menit | Truck wiring 1-2 jam |
| GPS accuracy | 5-10m (urban dense) | 3-5m |
| Reliability (Flutter foreground service) | 95%+ | 98% |
| Reliability (PWA only) | 70-85% | 98% |
| Battery (driver phone) | Drains | n/a |
| CAN bus access (RPM, odometer) | ❌ no | ✅ yes |
| Foreign-driver swap detection | Limited (phone-driver assumption) | n/a |

**Verdict:** Phone-as-GPS sangat fit untuk IRN — 23 vehicles, driver semua punya HP Android, save signifikan vs minor accuracy/feature trade-off.

### Required Mitigations (untuk Phone Reliability)

1. **Flutter wajib (Phase 1)** — PWA 70-85% reliability tidak cukup. Foreground service Flutter capai 95%+.

2. **Phone mount + USB charger di kabin** — IRN sediakan one-time Rp 80-150k/truck. Eliminate "HP mati di tengah jalan" excuse.

3. **App Dropout Detection rule** — kalau app offline >5 menit during scheduled trip, alert dispatcher (lihat `05-anti-fraud-tech-stack.md` Rule 2).

4. **Bonus "Perfect Sync Day"** — Rp 5-10k/hari kalau app online sepanjang trip = positive incentive.

5. **Sanction matrix** — kontrak driver mention konsekuensi HP offline tanpa alasan valid.

### Fuel Sensor Data Path (Side-Channel)

GPS via phone, **fuel sensor data via ESP32 BLE bridge** (lihat `05-anti-fraud-tech-stack.md` lengkap):

```
ESP32 (no SIM) → BLE → Phone → MQTT → Backend
                              (relay)
```

Phone = single gateway untuk both GPS dan fuel data.

---

## Mapping Platform Comparison (Tetap Relevant)

### Cost Per Operation (Indonesia, 2025-2026)

| Platform | Map tile load | Geocoding | Routing | Free tier |
|---|---|---|---|---|
| **Google Maps Platform** | $7 / 1k loads | $5 / 1k requests | $5 / 1k requests | $200/month credit |
| **Mapbox** | $5 / 10k loads | $0.75 / 1k geocode | $1 / 1k routing | 50k loads, 100k geocode/month |
| **HERE Maps** | $1 / 1k loads | $0.50 / 1k | $1 / 1k | Limited |
| **OSM + OSRM (self-host)** | Free (server cost) | Free (Nominatim self-host) | Free | Unlimited |
| **OpenStreetMap public tiles** | Free (rate limit, attribution) | Public Nominatim (rate limit) | Public OSRM (rate limit) | Free for low usage — NOT for production |

### Real Monthly Cost untuk IRN (23 vehicles, ~50 admin+customer users)

**Volume estimate:**
- Map loads (admin + customer + driver app): ~70k/month
- Geocoding (address → lat/lng): ~3k/month
- Routing (driver navigation, dispatcher ETA): ~5k/month

| Stack | Rough Monthly Cost (IDR) |
|---|---|
| **Pure Google Maps** | Rp 4-8jt |
| **Pure Mapbox** | Rp 800k-1.5jt |
| **Mapbox + Self-host Nominatim/OSRM** ⭐ | Rp 600k-1.2jt |
| **HERE Maps** | Rp 1-2jt |

**Winner:** **Mapbox + Self-host hybrid** untuk IRN class.

---

## Self-Host OSM Stack Setup

### Components

```
┌─ BACKEND OSM STACK ──────────────────────────┐
│                                              │
│  1. PostgreSQL + PostGIS                     │
│     └── osm_2dgis schema (load OSM data)     │
│                                              │
│  2. Tile Server (untuk map raster/vector)    │
│     ├── Tegola (vector tiles, recommended)   │
│     └── OR mapnik + mod_tile (raster)        │
│                                              │
│  3. Nominatim                                │
│     └── address geocoding service            │
│     └── port 8088                            │
│                                              │
│  4. OSRM                                     │
│     └── routing engine (car/truck profile)   │
│     └── port 5000                            │
│                                              │
└──────────────────────────────────────────────┘
```

### Server Spec (Indonesia + Asia data)

| Resource | Spec | Cost |
|---|---|---|
| VM (Biznet Cloud) | 8 vCPU, 32GB RAM, 500GB SSD | Rp 1.5-2jt/bulan |
| OSM data Indonesia + Singapore + Malaysia (PBF) | ~3GB | free download |
| Initial import time | 3-6 jam | one-time |
| Tile cache storage | ~50GB | included in SSD |

### Deployment

```bash
# Use Docker Compose for stack
# nominatim/nominatim:4.x
# osrm/osrm-backend:latest
# pelias/openaddresses (optional)

# OSM data import (Indonesia + neighbors)
wget https://download.geofabrik.de/asia/indonesia-latest.osm.pbf
wget https://download.geofabrik.de/asia/singapore-latest.osm.pbf
wget https://download.geofabrik.de/asia/malaysia-latest.osm.pbf

# Combine
osmium merge indonesia-latest.osm.pbf singapore-latest.osm.pbf malaysia-latest.osm.pbf -o sea-region.osm.pbf

# Import to Nominatim
docker run -v $PWD:/data nominatim/nominatim:4.4 \
  /data/sea-region.osm.pbf --threads 8

# Build OSRM graph for car routing (with truck restrictions)
docker run osrm/osrm-backend osrm-extract -p /opt/car.lua /data/sea-region.osm.pbf
docker run osrm/osrm-backend osrm-contract /data/sea-region.osrm
```

### Truck-Specific Routing Profile

```lua
-- Custom truck profile (Lua) untuk OSRM
function process_way(profile, way, result)
    local highway = way:get_value_by_key('highway')
    local maxweight = way:get_value_by_key('maxweight')
    
    if maxweight and tonumber(maxweight) and tonumber(maxweight) < 18 then
        return  -- exclude (truck terlalu berat)
    end
    
    -- Penalize residential / pedestrian for trucks
    if highway == 'residential' or highway == 'living_street' then
        result.forward_speed = result.forward_speed * 0.3
    end
end
```

OSRM truck-specific behavior: avoid jalan kecil (filter by max_height, max_weight tags di OSM), prefer toll roads untuk efisiensi, restrict from non-commercial roads.

---

## Mapbox Hybrid Setup

### Why Mapbox Tiles + Self-Host Backend

- Mapbox tiles **best-in-class** untuk visual quality (Indonesia coverage decent)
- Mapbox costs scale OK at IRN volume (~Rp 500k-1jt/bulan @ 23 vehicles)
- Self-host Nominatim + OSRM untuk geocoding & routing (high volume = cost saver)

### Setup

```javascript
// Frontend (Vue / React)
import maplibregl from 'maplibre-gl';

const map = new maplibregl.Map({
  container: 'map',
  style: 'https://api.mapbox.com/styles/v1/mapbox/streets-v12?access_token=YOUR_TOKEN',
  center: [104.0287, 1.0775],  // Batam
  zoom: 11
});

// Geocoding via self-host Nominatim
async function geocodeAddress(address) {
  const response = await fetch(
    `https://nominatim.irn.local/search?q=${encodeURIComponent(address)}&format=json`
  );
  return await response.json();
}

// Routing via self-host OSRM
async function getRoute(from, to) {
  const response = await fetch(
    `https://osrm.irn.local/route/v1/driving/${from.lng},${from.lat};${to.lng},${to.lat}?overview=full&geometries=geojson`
  );
  return await response.json();
}
```

---

## Phone GPS Implementation (Flutter)

### Background Tracking

```dart
import 'package:flutter_background_geolocation/flutter_background_geolocation.dart' as bg;

await bg.BackgroundGeolocation.ready(bg.Config(
  desiredAccuracy: bg.Config.DESIRED_ACCURACY_HIGH,
  distanceFilter: 50,         // 50m movement triggers ping
  stopOnTerminate: false,     // continue when app killed
  startOnBoot: true,
  enableHeadless: true,
  
  // Foreground service notification (user can't dismiss easily)
  notification: bg.Notification(
    title: "IRN Driver Aktif",
    text: "Tracking trip Anda — pak/bu jangan close",
    channelName: "Trip Tracking",
    smallIcon: "drawable/ic_notification",
    sticky: true
  ),
  
  // Battery optimization
  preventSuspend: true,
  heartbeatInterval: 60,      // Send heartbeat every 60s when no movement
  
  // Geofencing
  geofenceProximityRadius: 1000,
));

await bg.BackgroundGeolocation.start();

// Listener
bg.BackgroundGeolocation.onLocation((bg.Location location) async {
  await sendToBackend({
    'vehicle_id': currentVehicleId,
    'lat': location.coords.latitude,
    'lng': location.coords.longitude,
    'speed_kmh': location.coords.speed * 3.6,
    'heading': location.coords.heading,
    'accuracy_m': location.coords.accuracy,
    'ts': DateTime.now().millisecondsSinceEpoch,
  });
});
```

### Battery Strategy

- **Active trip:** ping every 30-60s atau 50m distance filter
- **Idle (off-trip):** ping every 5 menit (heartbeat) atau movement-triggered
- **Charging:** boost ping rate (more accurate)
- **Low battery <20%:** reduce frequency, alert driver "isi baterai"

### Reliability Targets

| Metric | Target | Measurement |
|---|---|---|
| Uptime during scheduled trip | >95% | Pings received vs expected |
| Average ping latency | <5s | Backend received - device timestamp |
| Geofence detection accuracy | >90% | True positive arrival/departure |
| App-killed-during-trip incidents | <5% | Auto-flag, monthly review |

---

## PostGIS for Tracking Storage (Tetap Relevant)

### Schema for Time-Series GPS Pings

(See `02-data-model-schema.md` for full schema)

Key strategy:
- **Partition by month** untuk performance
- **Indexes** on (vehicle_id, pinged_at) descending
- **GIST index** on geom for radius queries
- **Retention**: keep raw 90 days, then aggregate to daily summary

### Common Queries

```sql
-- 1. Vehicle current location (latest ping)
SELECT * FROM gps_ping
WHERE vehicle_id = '...'
ORDER BY pinged_at DESC
LIMIT 1;

-- 2. Vehicles within 5km of customer
SELECT v.*, ST_Distance(g.geom, c.geom) AS distance_meters
FROM vehicle v
JOIN LATERAL (
  SELECT geom FROM gps_ping
  WHERE vehicle_id = v.id
  ORDER BY pinged_at DESC LIMIT 1
) g ON true
JOIN customer c ON c.id = '...'
WHERE ST_DWithin(g.geom, c.geom, 5000);  -- 5km

-- 3. Trip path between two timestamps
SELECT geom, pinged_at, speed_kmh
FROM gps_ping
WHERE vehicle_id = '...'
AND pinged_at BETWEEN '2026-05-10 08:00' AND '2026-05-10 17:00'
ORDER BY pinged_at;

-- 4. Geofence breach detection
SELECT v.id, ST_AsText(g.geom)
FROM vehicle v
JOIN gps_ping g ON g.vehicle_id = v.id AND g.pinged_at > NOW() - INTERVAL '5 min'
JOIN location l ON l.id = '...'
WHERE NOT ST_Within(g.geom, l.geofence);
```

---

## Latency Targets

| Operation | Target |
|---|---|
| GPS ping → backend persist | <2 sec |
| GPS ping → dispatcher map update | <5 sec |
| Geofence breach → alert | <10 sec |
| Customer self-service tracking page load | <3 sec |
| Map tile load | <500ms |

**Strategi:**
- MQTT push (real-time) untuk GPS ingestion
- Redis pub-sub untuk dispatcher live update
- CDN cache untuk map tiles (Mapbox CDN built-in)

---

## Alternative Considered: Hardware GPS Devices (NOT CHOSEN for IRN)

> **Note:** Reference content for awareness — phone-as-GPS is locked default per CLAUDE.md 2026-05-11. Hardware GPS may be reconsidered for Phase 3+ fleet scale >100 vehicles or for crane-rental segment dengan harsh-environment requirements (IP67).

### Comparison untuk IRN-Equivalent Use Case

| Device | Capabilities | Pros | Cons | Harga (Rp) |
|---|---|---|---|---|
| **Teltonika FMC640** | GPS+CAN+RS232+Accel+IO+Bluetooth | EU-quality, robust SDK, distributor Indonesia, works with fuel sensor | Setup butuh expert | 1.4-1.8jt |
| **Teltonika FMM640** | + Marine, IP67 | Crane outdoor | Mahal | 2-3jt |
| **Concox JM-VL03** | GPS basic + ignition | Murah, install gampang | Less features, no RS232 | 800k-1.1jt |
| **Concox GT06N** | GPS + 2-way audio | Audio remote | Limited | 700k-900k |
| **Digital Sky Indonesia (lokal)** | OEM China rebrand | Local distributor support | Quality variable | 600k-1.2jt |
| **Nicelock NK-GPS** | Indonesia local | Local warranty | Limited firmware updates | 800k-1.2jt |

### Hypothetical hardware-GPS scenario (NOT for IRN)

If hardware GPS were chosen for 23-vehicle fleet:
- 23 truck × Rp 1.6jt = Rp 36.8jt
- Install: 23 × Rp 250k = Rp 5.75jt
- SIM card data: 23 × Rp 50k/bulan = Rp 1.15jt/month
- **Total hypothetical CapEx: Rp 42-45jt** (vs locked Phase 1 anti-fraud CapEx Rp 65-87.5jt yang INCLUDES IoT sensors)

For Phase 2 crane line (if hardware route taken): 12 crane × Rp 2.2jt (FMM640 IP67) = Rp 26.4jt

### SIM Card Strategy (if hardware GPS used)

```
Indosat Ooredoo IoT SIM     : Rp 30-50k/SIM/month (data 1GB)
Telkomsel IoT                : Rp 50-80k (better coverage at sea / remote)
XL IoT                       : Rp 30-60k

If hardware: Telkomsel untuk crane di shipyard remote, Indosat IoT untuk truck mainland Batam
```

**For IRN locked phone-as-GPS:** SIM cost = Rp 0 (driver existing data plan). N/A.

---

## Anti-Patterns

❌ **Hardware GPS Teltonika untuk IRN** — overkill scale 23 vehicles, save Rp 35-45jt CapEx + Rp 30jt/year dengan phone-as-GPS.

❌ **PWA only untuk driver app** — BG GPS reliability 70-85% gak cukup untuk anti-fraud production. Wajib Flutter Phase 1.

❌ **Pure Google Maps tanpa cek cost** — bisa Rp 4-8jt/bulan untuk 23 vehicles. Mapbox + OSM hybrid hanya Rp 600k-1.2jt.

❌ **Public OSM tile (tile.openstreetmap.org) di production** — rate-limited, ToS violation. Self-host wajib.

❌ **Track GPS setiap 10 detik** — battery drain di driver phone (PWA), data cost SIM. 30-60 sec + distance filter cukup.

❌ **Tidak ada fallback kalau phone offline** — wajib server-side anomaly detection (lihat APP_DROPOUT rule di file 05).

❌ **Tidak partition GPS table** — performance degradasi saat data >50M rows.

❌ **Tile rendering server-side from PostGIS** — overkill, slow. Pakai pre-rendered Mapbox/CartoDB tiles.

❌ **No fallback kalau Mapbox down** — fallback to OSRM-rendered MapLibre style or warning user.

---

## Phase Roadmap

### MVP / Phase 0 (Bulan 1-3) — PWA driver app
- PWA Flutter Web ATAU Vue PWA — quick deploy
- BG GPS via Geolocation API (limited reliability ~70-85%)
- Customer tracking link via Mapbox
- Validate UX dengan 5-10 driver

### Phase 1 (Bulan 4-6) — Migrate to Flutter ⚠️ KRITIKAL
- Flutter app dengan flutter_background_geolocation foreground service
- Reliability target 95%+
- BLE pairing dengan ESP32 fuel sensor (per file 05)
- Geofence auto-prompt mature
- Self-host Nominatim + OSRM (cost optimization)

### Phase 2 (Bulan 7-12)
- Truck-specific routing profile
- Historical heatmap analytics
- ML route optimization (rule-based first)
- Vector tile self-rendering (kalau Mapbox cost > Rp 5jt/bulan)

---

## Cross-References

- ESP32 fuel sensor side-channel detail → `05-anti-fraud-tech-stack.md`
- Flutter migration urgency → `03-driver-mobile-app-architecture.md`
- Live map dispatcher → `04-dispatcher-ai-autoassign.md`
- PostGIS schema → `02-data-model-schema.md`
- Anti-fraud-with-dignity workflow → `10-anti-fraud-with-dignity.md`
- CEO AI Assistant location/ETA query tools (LOCK-3) → `11-ceo-ai-assistant-architecture.md` §3.1

---

## Addendum (Day R3, 2026-05-11) — Phone-as-GPS Network Defensibility (WS8)

> **Anchor — Owner Gap #1 + #5:** Real-time kondisi lapangan + posisi container di jalan = solved via Mobile (driver phone GPS) + IoT (RFID gate triplet) + AI (location inference between gate scans).

### Telkomsel Coverage Reality Batam (WS8 LOCK)

| Metric | Value | Source |
|---|---|---|
| Telkomsel 4G coverage Batam | **>97%** (Opensignal Q1-Q2 2025) | WS8 deep report |
| 5G median Batam | **88 Mbps** | Opensignal |
| 4G median Batam | ~35 Mbps | Opensignal |

**Why this matters:** Phone-as-GPS pattern (Gojek-style) TECHNICALLY DEFENSIBLE Phase 1. **No hardware GPS tracker needed**. Saves ~Rp 35-45jt CapEx vs 23 vehicles × hardware GPS (~Rp 1.5-2jt/unit installed Teltonika/Concox tier).

### Cost Comparison: Phone-as-GPS vs Hardware GPS (Locked CLAUDE.md)

| Approach | CapEx (23 vehicles) | OpEx | Risk |
|---|---|---|---|
| **Phone-as-GPS (LOCKED)** | Rp 0 (use driver existing HP) | Telkomsel postpaid Rp 100-150k/sopir/bulan = Rp 2.3-3.5jt/bulan fleet | Battery, signal drop, app foreground discipline |
| Hardware GPS (Teltonika/Concox) — NOT chosen | Rp 35-45jt one-time | Sim card data Rp 50-100k/unit/bulan = Rp 1.2-2.3jt/bulan fleet | Lower (always-on), but theft risk + install cost |

**Discipline required for phone-as-GPS:**
1. Driver app foreground service mandatory (Android: foreground notification persistent)
2. Battery management — provide car charger MANDATORY for fleet
3. Local buffer 5-30 min for RAN congestion peak-hour (Pelita/Mukakuning 17:00-19:00)
4. Driver SOP: phone ON, app ON during shift — sanction matrix di `pakar-logistik-batam/05-driver-control-antifraud.md`

### Competitive Differentiator (vs PT Eddi)

Eddi (incumbent) menggunakan hardware GPS tracker — corporate-tier CapEx. INDUSIA differentiator = phone-as-GPS lower-CapEx model untuk SME segment. Frame untuk pitch deck: *"Kami tidak suruh Bapak invest Rp 45jt untuk GPS hardware. Telkomsel coverage >97% Batam — phone driver Anda sudah jadi GPS-nya."*
