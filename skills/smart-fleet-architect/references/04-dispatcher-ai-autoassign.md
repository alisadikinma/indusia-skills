# Pillar 04 — Dispatcher Console & AI Auto-Assign

## Dispatcher Console Architecture

### Core Components

```
┌─────────────────────────────────────────────────────────────┐
│  DISPATCHER CONSOLE LAYOUT                                   │
├──────────────────────┬──────────────────────────────────────┤
│  LEFT SIDEBAR        │  MAIN AREA (4 tabs)                  │
│  - Pending orders    │                                      │
│  - In-progress       │  TAB 1: LIVE MAP                    │
│  - Today completed   │  ├── Vehicle markers (color status)  │
│  - Idle drivers      │  ├── Active route paths              │
│  - Idle chassis      │  ├── Customer locations              │
│  - Alerts            │  └── Geofence overlays               │
│                      │                                      │
│                      │  TAB 2: ASSIGNMENT BOARD             │
│                      │  - Drag-drop trip → driver           │
│                      │  - AI suggestion ranked              │
│                      │                                      │
│                      │  TAB 3: TIMELINE GANTT               │
│                      │  - Per-driver day plan               │
│                      │  - Conflict highlighting             │
│                      │                                      │
│                      │  TAB 4: ALERTS                       │
│                      │  - SLA breach predictions            │
│                      │  - Anti-fraud anomalies              │
│                      │  - Cert expiry                       │
└──────────────────────┴──────────────────────────────────────┘
```

### Tech Stack

| Layer | Pilihan |
|---|---|
| **Framework** | Vue 3 + Vite (sama dengan PWA driver, code reuse) |
| **State** | Pinia |
| **Real-time** | WebSocket (Django Channels) atau Server-Sent Events |
| **Map** | MapLibre GL JS |
| **Charts** | Apache ECharts atau Chart.js |
| **Drag-drop** | vue-draggable-plus |
| **Notifications** | Web Push + browser tab title flash |

---

## Live Map Implementation

### Real-Time Vehicle Tracking

```javascript
// WebSocket subscription
const ws = new WebSocket('wss://api.irn.local/ws/dispatcher/');

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  
  if (data.type === 'gps_ping') {
    // Update map marker
    updateVehicleMarker(data.vehicle_id, data.lat, data.lng, data.heading);
    
    // Check geofence breach
    checkGeofenceBreach(data);
  }
  
  if (data.type === 'event') {
    // Pickup, delivery, etc.
    showNotification(data);
  }
};

// Backend pushes via Django Channels
// channels_layer.group_send('dispatcher_room', { type: 'gps_ping', ... })
```

### Throttling Strategy

GPS ping dari 50 vehicles × 1/30sec = 100 messages/min ke dispatcher. 

**Strategi:**
1. Backend aggregator: hanya forward ke WebSocket kalau **position changed >50m** atau **status changed**
2. Map debounce update: max 1 redraw per 2 detik per vehicle
3. Initial load: HTTP REST snapshot of all vehicles + WS for delta

---

## AI Auto-Assignment Engine

### Problem Statement

Given:
- N pending orders (delivery / crane jobs)
- M available drivers + M' available vehicles + M'' available chassis
- Constraints: SIO/SIM match, location proximity, fatigue limit, customer SLA

**Optimize:** assign trip → (driver, vehicle, chassis) tuple to **maximize total utility**.

### Approach: Constraint Solver vs Heuristic

| Approach | Pros | Cons | Recommended When |
|---|---|---|---|
| **Greedy heuristic** | Fast, easy to debug, deterministic | Suboptimal | MVP, <30 orders/day |
| **Constraint solver (Google OR-Tools)** | Optimal, handles complex constraints | Slower, harder to debug | Phase 1, 30-100 orders/day |
| **Reinforcement learning** | Self-improving | Needs lots of data, opaque | Future, >100 orders/day with quality data |

**Rekomendasi MVP/Phase 1: Greedy heuristic dengan ranking score**.

### Greedy Ranking Algorithm

```python
def rank_drivers_for_trip(trip, available_drivers):
    """
    Rank drivers for a single trip by composite score.
    Higher score = better assignment.
    """
    rankings = []
    
    for driver in available_drivers:
        score = 0
        breakdown = {}
        
        # 1. Distance to pickup (closer = better)
        # Use Haversine or PostGIS ST_Distance
        distance_km = calculate_distance(driver.current_location, trip.pickup_location)
        distance_score = max(0, 100 - distance_km * 2)  # 50km = 0 score
        breakdown['distance'] = distance_score
        score += distance_score * 0.25
        
        # 2. Vehicle type match
        vehicle_match_score = 100 if driver.assigned_vehicle.fits_trip(trip) else 0
        breakdown['vehicle_match'] = vehicle_match_score
        if vehicle_match_score == 0:
            continue  # hard exclude
        score += vehicle_match_score * 0.20
        
        # 3. Daily hours remaining (fatigue limit)
        hours_today = driver.hours_worked_today
        hours_remaining = max(0, 10 - hours_today)
        fatigue_score = min(100, hours_remaining * 12)
        breakdown['fatigue'] = fatigue_score
        if hours_remaining < trip.estimated_hours:
            continue  # hard exclude — would breach Permenaker
        score += fatigue_score * 0.15
        
        # 4. Performance score (higher = preferred)
        performance_score = driver.performance_score or 75
        breakdown['performance'] = performance_score
        score += performance_score * 0.15
        
        # 5. Cashbond limit (driver under high kasbon = lower priority for valuable trips)
        cashbond_pct = driver.outstanding_cashbond / driver.monthly_avg_earning
        cashbond_score = max(0, 100 - cashbond_pct * 200)
        breakdown['cashbond'] = cashbond_score
        score += cashbond_score * 0.10
        
        # 6. Customer affinity (driver yang pernah deliver ke customer ini = bonus)
        affinity_score = 100 if driver.has_history_with(trip.customer) else 50
        breakdown['affinity'] = affinity_score
        score += affinity_score * 0.05
        
        # 7. Return load opportunity (kalau driver pulang dari area trip → combo)
        combo_potential = check_combo_opportunity(driver, trip)
        breakdown['combo'] = combo_potential
        score += combo_potential * 0.10
        
        rankings.append({
            'driver': driver,
            'score': score,
            'breakdown': breakdown
        })
    
    return sorted(rankings, key=lambda x: x['score'], reverse=True)
```

### UX Pattern: Suggest, Don't Auto-Execute

```
┌────────────────────────────────────────────────────┐
│  Trip: Batu Ampar → Tg Uncang (PT YIXIN)           │
│  Container: MRKU4449924, 40FT                      │
│                                                     │
│  AI SUGGESTS (top 3):                              │
│  ✓ Pak Hamdardi  | Score 89 | 12km | Match ✓      │
│  ○ Pak Pardamean | Score 76 | 18km | Match ✓      │
│  ○ Pak Adhy      | Score 65 | 8km  | High kasbon  │
│                                                     │
│  [ Confirm Pak Hamdardi ]  [ Override... ]         │
└────────────────────────────────────────────────────┘
```

**Critical:** dispatcher tetap punya **veto power**. AI suggest, human confirm. Build trust gradual — kalau AI suggest 90% match dispatcher choice 30 hari → trust naik → enable "auto-confirm" flag.

---

## Route Batching (Combo Trip)

### Problem

5 trip ke Tanjung Uncang area dalam 1 hari → kalau 5 driver berbeda, masing-masing trip empty pulang. Kalau 1 driver lakukan 2-3 trip berurutan, save BBM + time.

### Algorithm (Simple Greedy)

```python
def cluster_trips_for_batching(trips, max_distance_km=15, max_trips_per_cluster=3):
    """
    Group trips that can be served by 1 driver in 1 route.
    """
    clusters = []
    sorted_trips = sorted(trips, key=lambda t: t.scheduled_at)
    
    for trip in sorted_trips:
        added = False
        for cluster in clusters:
            # Check if trip's drop location near cluster centroid
            if calculate_distance(trip.drop_location, cluster['centroid']) < max_distance_km:
                if len(cluster['trips']) < max_trips_per_cluster:
                    cluster['trips'].append(trip)
                    cluster['centroid'] = calculate_centroid([t.drop_location for t in cluster['trips']])
                    added = True
                    break
        
        if not added:
            clusters.append({
                'trips': [trip],
                'centroid': trip.drop_location
            })
    
    return clusters
```

### UX

Dispatcher dapat suggestion: "3 trip ke area Tg Uncang bisa dikombinasi → assign Pak Hamdardi (12 jam total, savings BBM Rp 350k vs 3 driver)"

---

## SLA Breach Prediction

### Method

Kalau trip-nya scheduled deliver jam 16:00, sekarang jam 14:00, driver masih jauh dari pickup → kemungkinan breach.

```python
def predict_sla_breach(trip, current_state):
    estimated_pickup_at = current_state.driver_eta_to_pickup
    estimated_delivery_at = estimated_pickup_at + trip.estimated_duration
    
    sla_threshold = trip.scheduled_delivery_at
    
    if estimated_delivery_at > sla_threshold:
        risk = (estimated_delivery_at - sla_threshold).total_minutes() / 60
        return {
            'will_breach': True,
            'risk_hours': risk,
            'recommendation': 'reassign_or_notify_customer'
        }
    return {'will_breach': False}
```

### UX

Top alert: "🟡 Trip 26887 risk breach SLA in 1h. Consider: (a) reassign Pak Pardamean, (b) notify customer +30 min delay, (c) accept breach"

---

## Anti-Patterns

❌ **AI auto-execute tanpa dispatcher confirm at MVP** — trust not built, error magnified.

❌ **Optimize for "globally optimal" sambil ignore driver preference** — sopir yang dipaksa rute jauh berkali-kali resign.

❌ **Tight coupling map library** — pilih MapLibre/Mapbox tanpa abstraction = hard switch later.

❌ **WebSocket tanpa fallback** — kalau WS putus, dispatcher blind. Fallback HTTP polling 30s.

❌ **Real-time map redraw setiap ping** — UI lag. Throttle 2s minimum.

---

## Cost Estimate

### Phase 1 Implementation (4-6 weeks)

| Item | Cost |
|---|---|
| Backend: WebSocket + ranking engine | Rp 30jt |
| Frontend: Live map + assignment board | Rp 25jt |
| Map: MapLibre tiles (Mapbox or OSM) | Rp 800k-2.5jt/month OpEx |
| **TOTAL CapEx** | **~Rp 55-60jt** |

---

## Cross-References

- Map cost & GPS device → `08-mapping-gps-tracking-stack.md`
- Driver fatigue / Permenaker → `pakar-logistik-batam/03-crane-operations.md` + `pakar-logistik-batam/05-driver-control-antifraud.md`
- Customer SLA tier → `pakar-logistik-batam/07-decision-frameworks.md`
- Combo trip economics → `pakar-logistik-batam/04-unit-economics.md`
