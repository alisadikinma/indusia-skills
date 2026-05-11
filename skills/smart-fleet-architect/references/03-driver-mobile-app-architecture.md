# Pillar 03 — Driver Mobile App Architecture

## Decision: Flutter vs PWA vs Native vs Hybrid

### Recommendation Matrix (IRN Context)

| Criteria | PWA | Flutter | React Native | Native (Kotlin) |
|---|---|---|---|---|
| **Time to ship MVP** | 1-2 bulan ⭐ | 2-4 bulan | 2-3 bulan | 4+ bulan |
| **Background GPS reliability (Android)** | 70-85% | 95% ⭐ | 90% | 95% |
| **Hardware (BLE, NFC, accelerometer)** | terbatas | excellent ⭐ | good | excellent |
| **Hot update tanpa Play Store** | instant ⭐ | tidak | tidak | tidak |
| **Re-use codebase admin web** | ya ⭐ | tidak | partial | tidak |
| **Cost developer** | 1 web dev | 1 Flutter dev | 1 RN dev | 2 dev (And+iOS) |
| **App size** | ~5MB cache | ~30MB | ~25MB | ~10MB |

### Final Recommendation untuk IRN

```
PHASE 0 (Bulan 1-3): PWA driver app
  - Tech: Vue 3 + Vite + Vue Router + Pinia + Workbox (service worker)
  - Backend: Same Django REST API
  - Validate UX dengan 5-10 sopir pilot
  - Risiko: BG GPS reliability ~80%, masih acceptable untuk MVP

PHASE 1 (Bulan 4-6, kalau >40 driver aktif): Migrate to Flutter
  - Background GPS via foreground service (95% reliability)
  - BLE pairing untuk fuel sensor close-range read
  - Camera quality lebih baik untuk OCR container number
  - Tetap pakai backend yang sama

PHASE 2 (Bulan 7+): Optional Flutter additional features
  - In-app dashcam preview
  - AR overlay container number scan
  - Voice memo (kalau driver request)
```

**Trigger migrate ke Flutter (concrete metrics):**
- ✅ >40 sopir aktif harian
- ✅ Customer complain "GPS hilang sinyal" >10/bulan
- ✅ Mau pasang fuel sensor BLE atau dashcam dengan companion app
- ❌ Bukan: "Flutter terlihat lebih profesional" — bukan trigger valid

---

## PWA MVP Architecture

### Stack

| Layer | Pilihan |
|---|---|
| **Framework** | Vue 3 + Vite |
| **Routing** | Vue Router (history mode) |
| **State** | Pinia |
| **Service Worker** | Workbox 7 |
| **Offline DB** | IndexedDB via Dexie.js |
| **HTTP** | Axios + interceptor untuk retry |
| **UI** | Tailwind CSS + Headless UI |
| **Map** | MapLibre GL JS (gratis tile dari OSM atau Mapbox tile) |
| **Camera** | `getUserMedia` API + canvas crop |
| **Signature** | `signature_pad` library |
| **Push** | Web Push API (Firebase Cloud Messaging) |

### Offline-First Pattern

```javascript
// Submission queue pattern
class DeliveryQueue {
  async submitDelivery(deliveryData) {
    // Always store locally first
    const localId = await db.deliveries.add({
      ...deliveryData,
      status: 'pending_sync',
      created_at: new Date()
    });
    
    // Try sync immediately if online
    if (navigator.onLine) {
      this.syncPending();
    }
    
    return localId;
  }
  
  async syncPending() {
    const pending = await db.deliveries
      .where('status').equals('pending_sync')
      .toArray();
    
    for (const item of pending) {
      try {
        const response = await api.post('/deliveries', item);
        await db.deliveries.update(item.id, {
          status: 'synced',
          server_id: response.data.id
        });
      } catch (err) {
        // Will retry on next sync trigger
        console.error('Sync failed for', item.id);
      }
    }
  }
}

// Auto-sync triggers:
// 1. App startup
// 2. Network reconnect (online event)
// 3. Service worker periodic sync (every 15 min when app open)
// 4. Manual user-pull
```

### Background GPS Strategy (PWA Limitation)

```javascript
// Active tracking (foreground)
navigator.geolocation.watchPosition(handlePosition, handleError, {
  enableHighAccuracy: true,
  maximumAge: 0,
  timeout: 30000
});

// Background tracking — TRICKY di PWA
// Solution 1: Periodic Background Sync (limited support, ~15 min interval)
self.addEventListener('periodicsync', async (event) => {
  if (event.tag === 'gps-ping') {
    await sendCachedPosition();
  }
});

// Solution 2: Wake Lock API to keep screen on (drains battery)
const wakeLock = await navigator.wakeLock.request('screen');

// Solution 3 (best for PWA): user must keep app open
// → Show big "ACTIVE TRIP" mode that prevents accidental close
```

**Real-world:** PWA BG GPS = 70-85% reliability. **Mitigate:**
- Geofence-based "auto-arrival" detection (saat masuk radius customer, popup forced confirm)
- Hourly "check-in" prompt (driver tap untuk konfirm masih aktif)
- Backend GPS interpolation (kalau ping drop, fallback ke last-known + estimate)

### Driver UX Principles

1. **Max 3 buttons per screen.** Sopir low-literacy. Gojek pattern.
2. **Big text (min 18pt).** Sopir 40+ tahun, sometimes pakai HP murah dengan layar kecil.
3. **Icon-first, text second.** Universal recognition.
4. **No nested menus.** Flat navigation.
5. **Color-coded status.** Hijau = OK, Kuning = pending, Merah = action needed.
6. **Voice feedback on key actions.** "Pickup tercatat" suara saat berhasil.
7. **Loading states explicit.** Sopir paranoid kalau spinner berputar terlalu lama.
8. **Failure feedback explicit.** "Foto gagal upload, akan otomatis kirim ulang."

### Core Driver App Screens (MVP)

```
SCREEN 1: HOME (Today's trips)
├── Today's assignments (list, big cards)
├── Active trip (sticky top kalau ada)
└── Bottom nav: Trips | Profile | Help

SCREEN 2: TRIP DETAIL
├── Customer info, pickup location, drop location
├── Container info
├── Big buttons:
│   - "MULAI TARIK" (start trip — log GPS, mark in_progress)
│   - "FOTO PICKUP" (capture + upload)
│   - "TIBA DI CUSTOMER" (geofence triggered or manual)
│   - "FOTO DELIVERY"
│   - "TANDA TANGAN CUSTOMER" (signature canvas)
│   - "SELESAI" (mark delivered)

SCREEN 3: PROFILE & EARNINGS
├── Hari ini: Rp X earned
├── Bulan ini: Rp Y earned
├── Total trip: N
├── Performance score: 87
└── Kasbon outstanding: Rp Z

SCREEN 4: NOTIFICATIONS
└── Push messages (new assignment, payment received, etc)
```

---

## Flutter Phase 1 Architecture

### Stack

| Layer | Pilihan |
|---|---|
| **Framework** | Flutter 3.x stable |
| **State** | Riverpod 2.x |
| **Routing** | go_router |
| **Local DB** | Drift (SQLite) |
| **HTTP** | Dio + interceptor |
| **Map** | flutter_map (OSM tiles) atau mapbox_gl |
| **GPS BG** | `flutter_background_geolocation` package |
| **Camera** | `camera` package + `image` for compression |
| **Signature** | `signature` package |
| **BLE** | `flutter_blue_plus` (untuk fuel sensor pair) |
| **Push** | Firebase Messaging |

### Flutter BG GPS Foreground Service

```dart
// Initialize foreground tracking
await BackgroundGeolocation.ready(Config(
  desiredAccuracy: Config.DESIRED_ACCURACY_HIGH,
  distanceFilter: 50,        // 50 meter movement = new ping
  stopOnTerminate: false,    // continue when app killed
  startOnBoot: true,
  enableHeadless: true,
  notification: Notification(
    title: "IRN Driver Aktif",
    text: "Tracking trip Anda",
    channelName: "Trip Tracking",
    smallIcon: "drawable/ic_notification"
  ),
));

await BackgroundGeolocation.start();

// Listen
BackgroundGeolocation.onLocation((Location location) {
  // Send to backend or queue
});
```

**Reliability gain:** 95% vs PWA 80%. Worth it saat sudah scaling.

---

## Cost Comparison

### PWA Phase

| Item | Cost |
|---|---|
| Dev (1 web dev × 2 bulan) | Rp 25-40jt |
| Hosting (cloud Biznet) | included MVP cost |
| Push notification (FCM) | Free |
| **TOTAL Phase 0** | **Rp 25-40jt** |

### Flutter Migration Phase

| Item | Cost |
|---|---|
| Dev (1 Flutter dev × 3 bulan) | Rp 60-90jt |
| Re-arsitek state mgmt (Pinia → Riverpod) | included |
| Play Store fee one-time | Rp 400k |
| Apple Developer (kalau iOS — biasanya tidak perlu utk driver) | Rp 1.5jt/year |
| **TOTAL Phase 1 migration** | **Rp 60-90jt** |

---

## Anti-Patterns

❌ **React Native untuk MVP** — middle-ground yang tidak punya keunggulan unik vs Flutter atau PWA.

❌ **Native Kotlin separate dari iOS** — IRN driver semua Android, double codebase = waste.

❌ **PWA tanpa offline-first design** — driver di Tanjung Uncang shipyard 4G dead zone — app crash = trust hilang permanent.

❌ **Track GPS setiap 5 detik** — battery drain, sopir matiin app. 30-60 detik distance-based filter cukup.

❌ **Force update mandatory yang block app** — sopir akan sabotase / cari workaround. Soft prompt + auto-update.

---

## Phase Roadmap Summary

| Phase | Tech | Time | Cost |
|---|---|---|---|
| MVP (0-3 bulan) | PWA Vue 3 | 2 bulan | Rp 30jt |
| Phase 1 (4-6 bulan) | Migrate to Flutter | 3 bulan | Rp 75jt |
| Phase 2 (7-12 bulan) | Flutter advanced (BLE, OCR camera) | feature-by-feature | Rp 30-50jt incremental |

---

## Cross-References

- Backend API design → `01-system-architecture-overview.md` + `02-data-model-schema.md`
- Fuel sensor BLE pairing → `05-anti-fraud-tech-stack.md`
- Map tile choice → `08-mapping-gps-tracking-stack.md`
- Driver UX rationale → `pakar-logistik-batam/05-driver-control-antifraud.md`
- Voice-note workflow + low-literacy UX (NEW skill Phase E) → `senior-ux-architect-id/02-driver-app-ux-deep.md`

---

## Addendum (Day R3, 2026-05-11) — Network Defensibility + Owner Gap Anchor

> **Anchor — Owner Gap #1 + #2:** Driver app primary purpose = data capture yang feed owner visibility dashboard real-time. JANGAN design driver convenience first — design owner-data-feed first, driver UX is constraint optimization.

### Telkomsel Network Reality (WS8 LOCK, 2026-05-11)

| Metric | Value | Source |
|---|---|---|
| Telkomsel 4G coverage Batam | **>97%** | WS8 + Opensignal Q1-Q2 2025 |
| 5G median Batam | **88 Mbps** | Opensignal |
| 4G median Batam | ~35 Mbps | Opensignal |

**Implication for phone-as-GPS:** Technically defensible. **No hardware GPS needed Phase 1** (saves ~Rp 35-45jt CapEx vs 23 vehicles × hardware GPS tracker). Locked CLAUDE.md.

**Failure mode:** RAN congestion during peak-hour (Pelita area, Mukakuning industrial 17:00-19:00 WIB). Driver app **local buffer 5-30 min** sebelum sync — already in PWA Phase 0 spec. Confirmed Telkomsel sufficient for ETA + GPS use cases.

### Indonesian Karyawan Layer UX Implications (LOCK-5)

Driver demographic mix Batam = Melayu / Jawa / Batak / Minang / Bugis. Low text literacy common, **WhatsApp Voice Note literacy HIGH**. Driver app design constraints:

1. **Voice-note workflow** must be 1-tap accessible (status update, issue report)
2. **Icon + Indonesian label + voice playback** > pure text
3. **Face-saving error messages** — "Coba lagi ya Pak" bukan "Error: invalid input"
4. **Bahasa Indonesia primary**, Bahasa daerah Batam terms accepted (cek `pakar-logistik-batam/06-tacit-batam-context.md` glossary)
5. **Anti-fraud-with-dignity** UX — clarification framing "Konfirmasi pengisian solar tadi pagi" BUKAN "Anda mencuri solar?" (see `10-anti-fraud-with-dignity.md` Phase C)

Detailed UX spec → `senior-ux-architect-id/02-driver-app-ux-deep.md` (NEW skill Phase E).
