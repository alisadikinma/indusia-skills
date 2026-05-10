# Pillar 07 — Container Yard Management System (CYMS)

## Why CYMS Matters

IRN punya **95 chassis** + ratusan empty containers yang masuk-keluar depo per bulan. Tanpa CYMS:

- Sopir butuh container ABC-123 → 30 menit cari di yard → ternyata ditimbun 2 layer → harus pindahin dulu → delay 1 jam
- Container "ngumpet" — tertimbun, ditemukan saat audit 6 bulan kemudian → cas customer hilang
- Salah ambil — sopir bawa BCD-456 alih-alih ABC-123 (nomor mirip) → re-deliver
- Operator forklift waste 30% time mencari, bukan stacking

**Threshold to build CYMS:** kalau yard > 100 active assets (chassis + container) atau pernah lose Rp 5jt+/bulan karena container hilang/salah ambil.

**For IRN:** Phase 2 candidate (bulan 7-12). MVP skip — manual whiteboard depo cukup untuk <50 active.

---

## Slot Addressing System

Standard industry format: **Block . Bay . Row . Tier**

```
A . 05 . 03 . 2
│   │    │   │
│   │    │   └─ Tier (1=ground, 2=tumpukan ke-2, 3=top)
│   │    └────── Row (urutan ke arah samping)
│   └─────────── Bay (urutan ke arah depan)
└─────────────── Block (zona besar — A, B, C)
```

### Example IRN Yard Layout

```
                  GATE (entrance/exit)
                          │
                          ▼
┌─────────────────────────────────────────────┐
│                                             │
│  BLOCK A (40FT containers)                  │
│  ┌───┬───┬───┬───┬───┬───┐                 │
│  │A1 │A2 │A3 │A4 │A5 │A6 │  ← Bay numbers  │
│  │R1-│R1-│R1-│R1-│R1-│R1-│                 │
│  │T1 │T1 │T1 │T1 │T1 │T1 │  ← R=Row, T=Tier│
│  ├───┼───┼───┼───┼───┼───┤                 │
│  │R2 │R2 │R2 │R2 │R2 │R2 │                 │
│  ...                                        │
│                                             │
│  BLOCK B (20FT containers)                  │
│  ...                                        │
│                                             │
│  BLOCK C (Empty / Maintenance)              │
│                                             │
└─────────────────────────────────────────────┘

Capacity examples:
  Block A: 6 bay × 4 row × 3 tier = 72 slots × 40FT
  Block B: 8 bay × 4 row × 3 tier = 96 slots × 20FT
  Block C: 4 bay × 2 row × 1 tier = 8 slots (workshop area)
```

---

## Stacking Workflow (Reach Stacker Operator App)

```
OPERATOR APP (mobile / tablet) — di reach stacker

┌─────────────────────────────────────────┐
│  STACK CONTAINER                        │
│                                         │
│  1. SCAN CONTAINER TAG (UHF handheld)   │
│     → "MRKU4449924, 40FT"               │
│                                         │
│  2. SUGGESTED SLOT:                     │
│     "A.03.02.1" (Block A, Bay 3,        │
│      Row 2, Tier 1 — ground level)      │
│                                         │
│  3. CONFIRMATION:                       │
│     ☐ Slot empty? ✓                    │
│     ☐ Container size match? ✓           │
│     ☐ ETA out > 7 hari? ✓ (allow stack) │
│                                         │
│  4. CONFIRM STACKED                     │
│                                         │
└─────────────────────────────────────────┘
```

Backend logic: pilih slot **terbaik** untuk container baru:

```python
def suggest_slot_for_incoming_container(container):
    """
    Pilih slot dengan FIFO logic:
    Container yang ETA out paling cepat → tier paling atas
    Container yang lama → tier bawah / inner
    """
    
    # 1. Filter slots yang capacity match
    candidate_slots = YardSlot.objects.filter(
        is_occupied=False,
        max_size=container.size,
        is_active=True,
        block__in=get_allowed_blocks(container.type)
    )
    
    # 2. Score each slot
    ranked = []
    for slot in candidate_slots:
        score = 0
        
        # Prefer ground-level slots for fresh containers
        if slot.tier_no == 1:
            score += 50
        
        # Avoid stacking on slot with container expiring sooner
        if slot.tier_no > 1:
            below_slot = get_slot_below(slot)
            if below_slot.current_container.expected_out_date > container.expected_out_date:
                score -= 100  # bad — blocks the one below from going out
            else:
                score += 30  # OK — bottom one will leave first or same
        
        # Prefer slots near gate (faster retrieval)
        gate_distance = calculate_distance(slot.geom, gate.geom)
        score -= gate_distance * 0.1
        
        # Prefer slots in less-occupied bays (avoid crowding)
        bay_occupancy = get_bay_occupancy(slot.block, slot.bay_no)
        score += (1 - bay_occupancy) * 20
        
        ranked.append({'slot': slot, 'score': score})
    
    return sorted(ranked, key=lambda x: x['score'], reverse=True)[:3]  # top 3 suggestions
```

---

## Retrieval Planner (Re-Stacking Suggestion)

Saat order keluar "Container ABC-123 harus diambil hari ini":

```python
def retrieve_container(container_id):
    container = Container.objects.get(id=container_id)
    slot = container.current_yard_slot
    
    if slot.tier_no == 1:
        return {
            'action': 'DIRECT_PICK',
            'slot': slot.code,
            'estimated_time_min': 5
        }
    
    # Container di tier > 1 → ada container di atasnya, harus dipindahin dulu
    above_slots = get_slots_above(slot)
    above_containers = [s.current_container for s in above_slots]
    
    moves = []
    for above_container in above_containers:
        # Cari slot temporary untuk pindahin container atas
        temp_slot = suggest_slot_for_incoming_container(above_container)[0]
        moves.append({
            'container': above_container.container_no,
            'from': above_container.current_yard_slot.code,
            'to': temp_slot.code,
            'estimated_time_min': 4
        })
    
    moves.append({
        'action': 'PICK_TARGET',
        'container': container.container_no,
        'from': slot.code,
        'estimated_time_min': 5
    })
    
    return {
        'action': 'RESTACK_REQUIRED',
        'moves': moves,
        'total_moves': len(moves),
        'estimated_total_min': sum(m['estimated_time_min'] for m in moves)
    }
```

---

## Yard Map UI (2D Grid + Tier Indicator)

```
DISPATCHER YARD MAP

┌─ BLOCK A (40FT) ─────────────────────┐
│                                       │
│   Bay 1   Bay 2   Bay 3   Bay 4      │
│  ┌─────┬─────┬─────┬─────┐           │
│  │██▓▓▓│██▓▓ │█    │     │ Row 1    │
│  │T:3:2│T:2  │T:1  │T:0  │          │
│  ├─────┼─────┼─────┼─────┤           │
│  │██▓▓ │██   │     │     │ Row 2    │
│  │T:2  │T:1  │T:0  │T:0  │          │
│  ├─────┼─────┼─────┼─────┤           │
│  │█████│██▓▓▓│██▓▓ │█    │ Row 3    │
│  │T:3  │T:3:2│T:2:1│T:1  │          │
│  └─────┴─────┴─────┴─────┘           │
│                                       │
│  Legend:                              │
│  █ = occupied (T=tier with container) │
│  ▓ = stacked level                    │
│  (empty) = available slot             │
└───────────────────────────────────────┘

Click on slot → show:
  - Container No
  - Size
  - Owner (shipping line)
  - Arrived: 15 hari yang lalu
  - Expected out: 5 hari dari sekarang
  - "PRIORITY: Soon retrieve" (red highlight)
```

### Color Coding

| Color | Meaning |
|---|---|
| Green | Empty slot, available |
| Yellow | Occupied <7 days |
| Orange | Occupied 7-30 days |
| Red | Occupied >30 days (idle alert!) |
| Purple | Reserved (incoming today) |
| Gray | Slot disabled (maintenance) |

---

## Idle Container Alert

```python
@celery_task(schedule=crontab(hour=8, minute=0))  # daily 8am
def check_idle_containers():
    threshold_days = 30
    idle_containers = Container.objects.filter(
        current_status='AT_DEPOT_IRN',
        last_event_at__lt=timezone.now() - timedelta(days=threshold_days)
    )
    
    for container in idle_containers:
        # Notify owner / shipping line
        notify_shipping_line(container)
        
        # Update for cas charge calculation if customer-attached
        if container.last_customer:
            calculate_cas_charge(container)
        
        # Flag in dispatcher dashboard
        create_alert({
            'type': 'IDLE_CONTAINER',
            'severity': 'MEDIUM',
            'container_id': container.id,
            'days_idle': (timezone.now() - container.last_event_at).days
        })
```

---

## Drone Yard Audit (Phase 3, Optional)

Setiap pagi, drone fly over yard → snap photo → AI count container, identify EPC tag visible → cross-check dengan database.

**Use case:** detect ghost container (di database tapi gak ada fisik) atau orphan container (fisik ada tapi gak di database).

**Cost:** Drone DJI Mavic ~Rp 15jt + AI vision setup + monthly OpEx ~Rp 2jt. **Skip for IRN sampai size > 500 chassis.**

---

## CYMS Phase Roadmap

### MVP — SKIP (manual cukup untuk <50 active)

### Phase 2 (Bulan 7-12)

```
□ Yard slot model + UI 2D grid
□ Operator app (PWA atau Flutter mobile) untuk reach stacker
□ Stacking workflow with UHF tag scan
□ Retrieval planner (re-stacking suggestion)
□ Idle alert >30 days
□ Daily yard audit report
```

**Estimated cost Phase 2:**
- Backend dev: Rp 80-120jt (CYMS adalah module sizable)
- UI / yard map dev: Rp 40-60jt
- UHF handheld scanner (1-2 unit untuk operator): Rp 8-15jt
- **Total: Rp 130-200jt CapEx**

### Phase 3 (Year 2+)

```
□ AI optimizer for stacking pattern (minimize re-shuffles)
□ Drone yard audit
□ Predictive demand (when container will come out)
```

---

## Anti-Patterns

❌ **CYMS di MVP** — overkill untuk <50 active container. Manual whiteboard cukup.

❌ **Slot addressing tanpa convention** — chaos saat operator beda interpret. Standar wajib.

❌ **No tier limit policy** — tumpuk 5+ layer = unsafe + sulit retrieve. Max 3 tier rule.

❌ **Skip drone audit selama-lamanya** — yard >500 active = audit reconciliation impossible manual.

❌ **Force operator update slot via UI desktop** — operator di lapangan, butuh handheld / tablet rugged.

---

## Cross-References

- Container lifecycle event log → `02-data-model-schema.md`
- UHF handheld for operator scan → `06-rfid-uhf-attendance-asset.md`
- Cas charge calculation → `pakar-logistik-batam/02-cargo-operations.md` + `pakar-logistik-batam/08-tax-finance-invoicing.md`
- When to invest in CYMS → `pakar-logistik-batam/07-decision-frameworks.md`
