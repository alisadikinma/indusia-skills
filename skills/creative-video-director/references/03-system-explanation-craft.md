# Reference 03 — System Explanation Craft (Anchor File)

## Project Variable Lock

Examples di file ini pakai **PT INDUSIA × IRN** sebagai illustrative default. Pattern + analogy **applicable ke any agency × any client** logistik / B2B Indonesia. Substitute placeholder saat produce.

## Tujuan File Ini

Mengajarkan **bagaimana menjelaskan sistem AI + digital ke audience awam** (owner trucking, akuntan, sopir, PM shipyard) dalam video promo — sehingga mereka **paham + percaya** dalam 60-90 detik, tanpa harus jadi engineer.

Ini file **paling penting** dari skill creative-video-director — di sinilah technical depth diterjemahkan ke language audience.

**Rule utama:**
> *"Buang 80% detail teknis. Ambil 1 mekanisme + 1 outcome konkret. Pakai analogy yang familiar di kepala audience."*

---

## The 3-Layer Translation Model

```
LAYER 1 — DETAIL TEKNIS (smart-fleet-architect's level)
   "Escort TD-150 capacitive sensor RS232 → ESP32-S3 BLE 5.0 → Flutter app
    → MQTT broker → Postgres+PostGIS triangulation rule (GPS stationary
    >10min + fuel drop >5L outside whitelisted zones)"
   
   ↓ TRANSLATE ↓

LAYER 2 — KONSEP HIGH-LEVEL (saya operate di sini untuk video promo)
   "Sensor mini di tangki solar truk + aplikasi di HP sopir = sistem tahu
    kalau ada solar bocor 5 liter saat truk parkir liar."
   
   ↓ TRANSLATE LAGI ↓

LAYER 3 — ANALOGY AWAM (saya pakai untuk hook + memorable line)
   "Kayak gauge bensin di mobil Anda — tapi sistemnya pinter,
    tahu kalau sengaja dikuras bukan dipake."
```

**Untuk video promo, mostly hidup di Layer 2 dengan moment of Layer 3 (analogy hooks).**

---

## Analogy Library — IRN Features → Familiar Mental Model

### Analogy 1 — Driver Mobile App

| Layer | Output |
|---|---|
| Detail teknis | Flutter offline-first PWA dengan IndexedDB queue, foreground service GPS, BLE pair to ESP32, Service Worker sync |
| **High-level** | "Aplikasi di HP sopir Anda — sopir tinggal tap untuk update sampai mana kontainer-nya." |
| **Analogy hook** | **"Kayak Gojek. Sopir Anda install aplikasi, beres."** |
| **Visual hook** | Split screen — kiri Gojek driver app yang familiar, kanan IRN driver app dengan UI mirip |

### Analogy 2 — GPS Tracking via Phone

| Layer | Output |
|---|---|
| Detail teknis | Phone-as-GPS (Gojek pattern), Mapbox tiles + OSM self-host, PostGIS time-series, foreground service Flutter |
| **High-level** | "Lokasi truk Anda live di peta — dari HP sopir, gak perlu pasang alat GPS terpisah." |
| **Analogy hook** | **"Kompetitor pasang GPS Rp X juta di tiap truk. Kami? Sopir Anda install aplikasi. Sama kayak Gojek."** *(NO angka investasi — gunakan "Rp X" sebagai pain reference, bukan harga produk)* |
| **Visual hook** | Split screen — kiri teknisi pasang banyak alat di truk kompetitor (cluttered), kanan sopir IRN tap HP, peta nyala (clean) |

### Analogy 3 — Fuel Sensor (Anti-Kencing-Solar)

| Layer | Output |
|---|---|
| Detail teknis | Escort TD-150 capacitive sensor RS232 + ESP32-S3 BLE bridge + tamper switch + triangulation rule (GPS stationary + fuel drop + non-whitelisted location) |
| **High-level** | "Sensor mini di tangki solar truk — kalau ada solar bocor 5 liter saat truk lagi parkir liar, sistem otomatis tahu." |
| **Analogy hook** | **"Kayak gauge bensin mobil Anda — tapi pinter. Tahu kalau sengaja dikuras, bukan dipakai jalan."** |
| **Anti-tamper hook** | **"Sopir bisa matiin HP. Tapi sensor mini itu? Punya internet sendiri lewat HP — lampu nyala 24/7. Sopir gak bisa matiin."** *(simplified — sebenarnya BLE-to-phone, tapi visual feel like always-on)* |
| **Visual hook** | Lampu LED kecil di sensor box, animasi data flow ke HP ke peta dispatcher |

### Analogy 4 — Live Map Dispatcher

| Layer | Output |
|---|---|
| Detail teknis | Vue 3 + MapLibre + WebSocket (Django Channels) + real-time GPS pings throttled, geofence overlay, ranking algorithm auto-assign |
| **High-level** | "Dashboard dengan peta — Anda lihat 23 truk Anda dimana, siapa standby, siapa lagi jalan." |
| **Analogy hook** | **"Kayak peta Maxim/Gojek Anda lihat ojol. Tapi semua truk Anda sekaligus, di satu layar."** |
| **AI auto-assign hook** | **"Order baru masuk? Sistem otomatis suggest: 'Pak Hamdardi paling dekat, lagi pulang dari Tg Uncang, terima order ini'."** |
| **Visual hook** | Screen recording dashboard live, marker truk bergerak, push notification "Order baru — assigned ke Pak Hamdardi" |

### Analogy 5 — Auto Surat Jalan + ePoD

| Layer | Output |
|---|---|
| Detail teknis | Driver app capture photo + GPS + canvas signature + auto-email PDF generation (ReportLab) + delivery_event log + auto-invoice line append |
| **High-level** | "Sopir antar kontainer → foto + customer tanda tangan di HP → surat jalan auto-email ke customer + tersimpan di sistem." |
| **Analogy hook** | **"Sama kayak kurir Shopee tibanya — tinggal tap, foto, tanda tangan, beres. Surat jalan? Auto-email ke customer."** |
| **Closing-loop hook** | **"Akhir bulan, Bu Rinda gak perlu cari surat jalan di WA. Semua sudah di sistem. Tinggal generate invoice."** |
| **Visual hook** | Driver POV: tap → foto → tanda tangan → notifikasi "Surat jalan dikirim ke customer email" |

### Analogy 6 — UHF RFID Gate (Multi-Asset Logging)

| Layer | Output |
|---|---|
| Detail teknis | UHF 920-925 MHz EPC Gen 2 reader, multi-tag anti-collision Q algorithm, MQTT push, asset triplet (driver+vehicle+chassis) |
| **High-level** | "Reader di gate depo. Truk lewat — sistem otomatis log: siapa sopirnya, truk mana, chassis nomor berapa, masuk atau keluar." |
| **Analogy hook** | **"Kayak e-Toll Anda. Tap (sebenarnya gak perlu tap — auto-detect 5 meter). Beres."** |
| **Anti-buddy-punch hook** | **"Sopir tap kartu absensi temannya? Gak bisa. Sistem baca tag di lanyard sopir + tag di kabin truk. Mismatch = alert."** |
| **Visual hook** | Truck masuk gate → reader LED hijau → dashboard show event "Pak Pardamean + PM22 + IRN40-027 entered, 14:22:15" |

### Analogy 7 — CYMS (Container Yard Management)

| Layer | Output |
|---|---|
| Detail teknis | Slot addressing Block.Bay.Row.Tier, FIFO optimizer, retrieval planner with restacking suggestion |
| **High-level** | "Cari kontainer ABC-123 di yard? 5 detik di sistem. Tahu di Block A, Bay 3, Tier 2. Tahu yang di atasnya kontainer mana." |
| **Analogy hook** | **"Kayak rak buku Anda yang nomor labeled — gak perlu bongkar 5 baris cari kaos hitam."** |
| **Stacking hook** | **"Operator forklift suggest mana yang harus dipindahin dulu — sistem tahu mana yang segera keluar, mana yang bisa di-stack lama."** |
| **Visual hook** | 2D grid map yard, search "ABC-123" → highlight slot + show stack-above warning |

### Analogy 8 — AI Auto-Assign + Admin Upgrade

| Layer | Output |
|---|---|
| Detail teknis | Greedy ranking algorithm (distance + ETA + capacity + fatigue + cashbond + rating), constraint solver OR-Tools, real-time orchestration |
| **High-level** | "Order baru masuk → AI suggest sopir paling dekat + paling fit. Admin tinggal konfirm." |
| **Analogy hook** | **"Kayak Gojek matchmake driver-customer. Tapi untuk truk Anda, untuk rute Anda."** |
| **Admin reduction hook (HATI-HATI framing)** | **"Cici Anda gak perlu lagi cek WA 50 chat per pagi. AI yang ngatur — dia naik kelas, dari data router jadi pengawas sistem."** *(framing: upgrade role, BUKAN PHK)* |
| **Visual hook** | Split: kiri WhatsApp chaos 50 chat, kanan dashboard "AI menyarankan: Pak Hamdardi (paling dekat, lagi pulang) — Konfirm?" Cici klik 1 tombol. |

### Analogy 9 — AI Route Optimizer (Reduce BBM Boros)

| Layer | Output |
|---|---|
| Detail teknis | Multi-stop TSP (Traveling Salesman) approximation, real-time GPS-aware re-routing, OSRM custom truck profile, return-load combo trip |
| **High-level** | "AI hitung rute paling dekat + paling cepat. Kalau sopir Anda lagi pulang dari Tg Uncang dekat order baru, dia yang dapat — gak perlu sopir lain dari kantor bolak-balik." |
| **Analogy hook** | **"Sama kayak GoFood ke-GrabFood — driver paling dekat yang dapat order, bukan driver yang lagi seberang kota."** |
| **BBM saving hook** | **"Sopir Anda gak lagi bolak-balik. Hari ini 4 trip jadi 5. BBM yang sama — revenue 25% naik."** *(angka revenue gain OK kalau outcome — JANGAN angka investasi)* |
| **Visual hook** | Map animation: order pin muncul, sistem highlight 3 sopir terdekat, AI ranking score muncul, suggested driver flash green |

### Analogy 10 — Owner Live Dashboard (Visibility Pain)

| Layer | Output |
|---|---|
| Detail teknis | WebSocket real-time push, dashboard Vue 3 + MapLibre + Pinia state, role-based access (owner sees all, admin sees ops, driver sees own) |
| **High-level** | "Pak Indra, dari HP Anda, lihat sendiri: 23 truk Anda dimana, container apa di mana, sopir mana lagi kerja, cas kena berapa hari." |
| **Analogy hook** | **"Kayak app banking Anda — buka, lihat saldo + transaksi semua. Tapi ini fleet Anda."** |
| **Owner-empower hook** | **"Tanya admin? Gak perlu lagi. Anda lihat sendiri, jam berapa pun, dimana pun."** |
| **Visual hook** | Pak Indra di kursi rumah malam Minggu, buka HP, dashboard live dengan 23 marker truk + container status, smile knowing |

### Analogy 11 — Asset Heat Map (Chassis & Container Tracking)

| Layer | Output |
|---|---|
| Detail teknis | Last-known-location per chassis (UHF gate event + driver app GPS), PostGIS geo-radius query, heat-map rendered MapLibre with cluster + density layers |
| **High-level** | "Setiap chassis dan container Anda — sistem tahu terakhir ada di mana, sudah berapa hari di sana. Tinggal klik nama container → muncul di peta." |
| **Analogy hook** | **"Kayak Find My iPhone — tapi untuk 90 chassis Anda. Hilang? Gak ada. Tahu lokasi terakhir, kapan."** |
| **Idle alert hook** | **"Container ABC-123 di customer X sudah 5 hari? Sistem flag merah: 'Cas customer kena hari ke-2, jemput sekarang'."** |
| **Visual hook** | Map view 90 chassis sebagai dots, color-coded: hijau (active <3 days), kuning (3-7 days), merah (>7 days idle), klik untuk detail |

### Analogy 12 — 3-Hari SLA Auto-Tracker (Cas Recovery)

| Layer | Output |
|---|---|
| Detail teknis | Per-customer SLA config (3/5/7 hari), auto-timer triggered saat container delivered, alert generation pre-breach, integration ke invoicing untuk auto-cas line append |
| **High-level** | "Customer Anda deal 3 hari unloading. Hari ke-3 jam 17:00, sistem auto-alert: 'Container 5 unit di Tg Uncang lewat free-time, cas Anda Rp X — atau jemput sekarang sebelum hari ke-4.'" |
| **Analogy hook** | **"Kayak parkir online Anda — tahu tarif jalan sambil mobil parkir. Container Anda di customer? Tahu cas-nya ke-track, gak hilang."** |
| **Revenue recovery hook** | **"Cas customer Anda yang lupa ditagih — bocor diam-diam Rp jutaan/bulan. Sistem ini stop kebocoran."** |
| **Visual hook** | Timer animation per container card: "Day 2 / 3", warning di hari 3, alert merah di hari 4+ |

### Analogy 13 — Maintenance Schedule Reminder

| Layer | Output |
|---|---|
| Detail teknis | KM-based + time-based maintenance triggers, integrated dengan GPS odometer + asset profile (last service date, next due), auto-notification dispatcher + workshop |
| **High-level** | "Setiap truk + chassis punya jadwal service. Sistem tahu km berapa dan kapan terakhir. Mendekati due → auto-reminder ke admin + workshop." |
| **Analogy hook** | **"Kayak service mobil Anda yang dealer ngingetin via SMS. Tapi ini untuk 23 truk + 90 chassis Anda — sistem yang ingat, bukan otak Anda."** |
| **Cost-saving hook** | **"Service telat = breakdown di jalan = Rp jutaan emergency. Service tepat waktu = Rp ratusan ribu maintenance rutin."** |
| **Visual hook** | Calendar view per asset, badge "Due in 7 days" dengan auto-suggest workshop slot |

### Analogy 14 — Driver In-App Incident Report

| Layer | Output |
|---|---|
| Detail teknis | In-app form: incident type (ban bocor, mesin trouble, kecelakaan, dll), foto upload, GPS auto-location, severity, dispatch ke nearest mekanik / response team |
| **High-level** | "Sopir di jalan ada masalah — ban bocor, mesin trouble, kecelakaan kecil. Buka aplikasi, tap 'Lapor Incident', foto, kirim. Lokasi auto. Dispatcher + mekanik terdekat dapat notif." |
| **Analogy hook** | **"Kayak panggil derek via app — tapi ini sopir Anda yang panggil mekanik internal."** |
| **Response speed hook** | **"Sopir gak harus telepon 5 nomor cari tahu siapa yang available. 1 tombol → mekanik terdekat datang."** |
| **Visual hook** | Driver POV: ban bocor di pinggir jalan → tap incident button → upload foto → notification "Mekanik PT INDUSIA pak Yudi tracking ke lokasi Anda, ETA 25 menit" |

### Analogy 15 — Auto Invoicing (Akuntan Pain)

| Layer | Output |
|---|---|
| Detail teknis | Auto aggregate completed deliveries per customer, PPh 23 auto-calc, e-Faktur Coretax API integration |
| **High-level** | "Akhir bulan, sistem otomatis generate invoice per customer dengan PPh 23 + faktur pajak — Bu Rinda tinggal review + send." |
| **Analogy hook** | **"Kayak QRIS payment Anda — auto-generate sesuai kategori. Tinggal kirim, beres."** |
| **Pain-relief hook** | **"10 hari kerja keras Bu Rinda jadi 1 hari. Sabtu Anda balik."** |
| **Visual hook** | Time-lapse dashboard generate invoice, Bu Rinda click "Send All", smile |

---

## "Show Don't Tell" Pattern

Untuk video, **screen recording 5-10 detik > narasi VO 30 detik**.

| Concept | Show (Better) | Tell (Avoid) |
|---|---|---|
| Driver app simple | 3-button screen, sopir tap → foto → done. Total 5 detik visual | "Aplikasi yang user-friendly dengan UI yang intuitif untuk semua kalangan sopir" |
| Live map | Map zoom in, 23 truk markers moving, click → trip detail popup | "Dashboard real-time dengan visibility lengkap atas seluruh fleet Anda" |
| Anti-fraud alert | Notification ping → "ALERT: Truk PM22 diam 15 menit, fuel turun 7L, lokasi: Jln Pelita" → dispatcher action | "Sistem deteksi otomatis berbagai bentuk pencurian BBM dengan triangulation rule canggih" |
| Auto invoice | Click "Generate Bulan November" → loading 2 detik → 14 invoices ready | "Otomasi end-to-end accounts receivable dengan integrasi e-Faktur" |

---

## Simplification Rules

### Rule 1: Maximum 1 Mechanism per 30 Seconds Video

Jangan stack penjelasan. 1 video = 1 fitur fokus.

```
Video A — "Driver App Simple"
  Pain: surat jalan hilang di WA
  Solution: aplikasi 3 tombol
  Outcome: surat jalan auto-email, ke-track

Video B — "Anti-Kencing-Solar"  
  Pain: sopir nyolong 5L per trip
  Solution: sensor + sistem alert
  Outcome: kerugian solar berhenti

Video C — "Live Dispatcher"
  Pain: admin assign sopir manual via WA
  Solution: auto-assign suggestion
  Outcome: admin time saved, sopir paling fit yang dapat
```

**JANGAN:** "Sistem kami integrate driver app + sensor + dispatcher + invoice + RFID + CYMS dalam satu platform" (= ❌ overwhelming, audience zone-out)

### Rule 2: Numbers untuk Pain, BUKAN Investment

✅ Pain numbers (resonance):
- "5 liter solar per trip"
- "10 hari nutup buku"
- "30 unit truk Anda"
- "75 hari customer baru bayar"

❌ Investment numbers (skip di public promo):
- "Investasi Rp X jt"
- "Payback 18 bulan"
- "ROI 30%"
- "CapEx Rp Y"

### Rule 3: "Awam Test" Sebelum Approve Script

Bisa kah ibu Anda yang **gak paham IT** repeat penjelasan ini ke teman?
- ❌ "Sistem TMS dengan AI-driven triangulation untuk anti-fraud detection" — gak lulus
- ✅ "Aplikasi di HP sopir + sensor mini di truk = tahu kalau ada solar bocor" — lulus

### Rule 4: Buang "Yang", "Adalah", "Merupakan"

Kalimat menjadi tegas dan punchy.

❌ "Sistem yang dibangun adalah platform yang merupakan solusi" → terlalu corporate
✅ "Sistem ini bantu Anda. Cara kerjanya gampang." → punchy, native

---

## Demo Storyboard Template (Untuk PT INDUSIA Case Study Video)

Format video promo PT INDUSIA tentang IRN:

```
═══════════════════════════════════════════════════════════
TIMING       BEAT             VISUAL              VO/COPY
═══════════════════════════════════════════════════════════
0-3s         HOOK (Pattern    [Visual: ECU       "Sopir IRN
             Interrupt +      sopir di kabin]    nyolong solar
             Data Hook)                          5 liter,
                                                 22 trip per
                                                 bulan."
─────────────────────────────────────────────────────────────
3-7s         FORESHADOW       [Visual: kalkulator "30 sopir Pak
                              animasi]            Indra. Coba
                                                 hitung."
─────────────────────────────────────────────────────────────
7-15s        AGITATE          [Visual: B-roll    "Bu Rinda di
             (Old Way Pain)   surat jalan        kantor Jumat
                              berserakan, WA     malam, masih
                              chat penuh,        nutup buku
                              admin lelah]       November.
                                                 10 hari."
─────────────────────────────────────────────────────────────
15-30s       GUIDE+PLAN       [Visual: PT       "PT INDUSIA
             (PT INDUSIA      INDUSIA logo,     bantu IRN solve.
             enters)          dashboard demo,   AI + digital
                              app sopir]        yang ngerti
                                                 lapangan, bukan
                                                 cuma slide
                                                 deck."
─────────────────────────────────────────────────────────────
30-50s       MECHANISM        [Screen recording  "Sopir Anda
             (1 fitur fokus)  driver app:        install aplikasi
                              tap foto tanda    di HP. Sensor
                              tangan]           mini di tangki
                                                 solar. Live map
                                                 di kantor.
                                                 Beres."
─────────────────────────────────────────────────────────────
50-65s       PEAK             [Visual: Bu Rinda  "3 bulan kemudian:
             (RESULT)         senyum, dashboard  IRN omset naik.
                              menunjukkan green  Kecurangan turun.
                              metrics, sopir    Bu Rinda bisa
                              terima bonus]     pulang Sabtu."
─────────────────────────────────────────────────────────────
65-75s       CTA + WON DAY    [Visual: PT       "Mau hasil
                              INDUSIA logo,     seperti IRN?
                              contact CTA]      Jadwalkan demo:
                                                 [link di bio]"
═══════════════════════════════════════════════════════════
```

---

## Critical: PT INDUSIA's Authority Positioning

PT INDUSIA harus terdengar seperti **agency AI Indonesia yang ngerti lapangan**, bukan agency IT generik.

| Sound Like | Don't Sound Like |
|---|---|
| "Kami pernah lihat 50 sopir IRN — kami tahu modus kencing solar" | "Kami menyediakan solusi end-to-end logistik" |
| "Aplikasi yang kami bangun, sopir IRN pakai dari hari pertama" | "User-friendly platform untuk transformasi digital" |
| "Sistem ini AI? Iya. Tapi yang penting: solve pain di kabin truk" | "AI-powered ecosystem dengan ML capabilities" |

**Authority signals untuk PT INDUSIA:**
- Specific case study mention ("IRN Batam — 23 truk, 90 chassis")
- Specific numbers ("kencing solar 5L/trip × 22 trip")
- Specific tech tapi grounded ("HP sopir + sensor mini di tangki")
- Specific industry term ("kontainer", "cas", "kasbon", "supir") — proves "kami ngerti"

**Avoid:**
- Generic claim "leading AI agency in Indonesia"
- Stock footage business-people-shaking-hands
- Slide deck dengan diagram complicated
- Voiceover corporate announcer

---

## Output: Brief Snippet untuk smart-fleet-architect

Saat saya butuh konsultasi smart-fleet-architect untuk fitur explanation, format prompt:

```
/smart-fleet-architect

Konteks: video promo PT INDUSIA tentang case study IRN.
Audience: owner trucking lain seperti Pak Indra (NOT engineer).

Saya butuh untuk fitur [X]:
1. Cara kerja dalam 1 kalimat awam (untuk VO 5 detik)
2. Analogy familiar (Gojek, e-Toll, kurir Shopee, dll) yang fit
3. Visual hook — apa di-show di video (1-2 detik shot)
4. Anti-claim — apa yang TIDAK boleh saya promise

Format: 4 bullet points pendek. Tidak butuh SQL/API/code.
```

---

## Cross-References

- Brand voice doctrine PT INDUSIA → `01-brand-voice-doctrine.md`
- Archetype routing per audience → `02-archetype-routing-hooks.md`
- Format decision (kapan video vs carousel) → `04-video-format-decision.md`
- Cross-format orchestration → `05-cross-format-orchestration.md`
- Anti-pattern veto list → `06-creative-vetos.md`
- Original storytelling theory (canonical) → `ai-video-promo-engine/reference/storytelling_script_gen/F1-F11`
