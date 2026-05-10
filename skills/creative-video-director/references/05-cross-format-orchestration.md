# Reference 05 — Cross-Format Orchestration

## Project Variable Lock

Templates di file ini pakai **PT INDUSIA × IRN** sebagai illustrative default. Substitute `{{agency_name}}`, `{{client_name}}`, `{{client_data}}` untuk project lain. Pattern adaptation cross-format applicable ke any project.

## Tujuan File Ini

Kalau agency mau **leverage** 1 storyline proposal jadi multi-format aset (proposal video + carousel + pitch deck slide + LinkedIn post + email follow-up + WhatsApp share) dengan **brand voice consistent**.

**Master rule:** semua format datang dari **1 storyline central** (3-4 min proposal video). Bukan re-create from scratch per format.

---

## Master Storyline (Source of Truth)

```
ACT I (HOOK):    Pak Indra, kami pelajari IRN — 23 truk, 90 chassis, 4 masalah
ACT II (PAIN):   1. Sopir kencing solar
                 2. Admin chaos WA
                 3. Driver bolak-balik BBM boros
                 4. Surat jalan hilang, invoice telat
ACT III (GUIDE): PT INDUSIA Kecerdasan Digital — AI yang ngerti kabin truk
ACT IV (PLAN):   1. Sensor + AI triangulation
                 2. AI auto-assign + route optimizer
                 3. Driver app + ePoD
                 4. Admin upgrade dari data router → pengawas sistem
ACT V (PEAK):    Hari biasa di IRN — omset naik, kecurangan turun
ACT VI (CTA):    Mari kick-off — tim PT INDUSIA siap demo di kantor IRN
```

Setiap format adalah **lensa berbeda dari storyline yang sama**. Voice doctrine (file 01) konsisten.

---

## Format-by-Format Adaptation Playbook

### Format 1 — PROPOSAL VIDEO (Master, 3-4 min)

**Lokasi:** `04-video-format-decision.md` (lengkap)

**Voice:** Lugas, langsung, dramatic.
**Distribution:** WhatsApp share, email, embed deck.

---

### Format 2 — CAROUSEL (LinkedIn / IG, 8 slide)

**Use case:** Pak Indra atau decision maker IRN scroll LinkedIn — 8-slide carousel akan stop scroll, give enough context untuk minta proposal video full.

**Master 8-slide carousel:**

```
Slide 1 (HOOK):    [Big text 60pt black bg]
                   "23 truk Anda di Batam.
                   4 masalah yang Anda lebih tahu dari kami."
                   [PT INDUSIA logo bottom small]

Slide 2 (PAIN 1):  [Image: jeriken solar di pinggir jalan, gritty]
                   Headline: "Sopir Anda nyolong 5 liter solar/trip"
                   Body: "30% sopir × 22 trip × 5 liter = ~Rp 5jt/bulan
                   bocor. Anda tahu — tapi gak punya bukti."

Slide 3 (PAIN 2):  [Image: HP Cici dengan WA chat banyak]
                   Headline: "Cici Anda dispatch 23 sopir lewat WhatsApp"
                   Body: "5 pagi 50 chat. Foto delivery sopir? Hilang
                   di antara meme dan grup arisan. Invoice telat 5 hari."

Slide 4 (PAIN 3):  [Image: map dengan zigzag inefficient route]
                   Headline: "Sopir Anda bolak-balik. BBM boros."
                   Body: "Sopir pulang dari Tg Uncang, order baru
                   muncul di Hamin. Cici suruh sopir lain dari kantor.
                   2 sopir capek, 1 trip ekstra hilang."

Slide 5 (PAIN 4):  [Image: paper surat jalan tumpukan + Bu Rinda lelah]
                   Headline: "Bu Rinda nutup buku 10 hari. Tiap bulan."
                   Body: "247 surat jalan, 4 hilang, customer dispute,
                   cash telat 2 minggu."

Slide 6 (GUIDE):   [PT INDUSIA logo + photo team / kantor PT INDUSIA]
                   Headline: "PT INDUSIA Kecerdasan Digital"
                   Body: "AI + digital yang ngerti kabin truk —
                   bukan slide deck. 4 masalah Anda, 4 solusi
                   yang kami bangun spesifik untuk IRN."

Slide 7 (SOLUTION SUMMARY): [Quad split — 4 ikon mechanism]
                   1. Sensor + AI: kencing solar berhenti
                   2. AI route + auto-assign: BBM hemat, lebih trip
                   3. Driver app + ePoD: surat jalan auto-email
                   4. Admin upgrade: Cici naik kelas, Bu Rinda Sabtu pulang

Slide 8 (CTA):     [PT INDUSIA contact + QR code + nama]
                   "Ingin lihat sistem kerja di IRN-Anda?
                   Schedule demo: [phone/calendly]"
```

**Voice:** Same doctrine 01. Headline punchy, body 2-3 kalimat max.

**Cinematography:** static images preferred (gambar tidak bergerak — mobile scroll friendly), color grade industrial-doc desaturated.

---

### Format 3 — LINKEDIN TEXT POST (1100-1300 char)

**Use case:** PT INDUSIA founder/team post organic untuk awareness — Pak Indra atau kolega owner trucking lain di LinkedIn lihat.

**Template:**

```
Hari Senin pagi di Batam — Pak Indra owner perusahaan trucking 
23 unit + 90 chassis kontainer panggil kami.

"Saya gak tahu sopir mana yang nyolong solar. 
Saya gak tahu admin saya kerja efisien atau enggak. 
Saya gak tahu sampai kapan customer bayar 
yang sudah lewat 90 hari."

Setelah analisa 1 minggu, kami nemu 4 pain pattern:

1. Kencing solar — 30% sopir, 5L per trip, ~Rp 5jt/bulan bocor
2. Admin chaos WhatsApp — Cici 5 pagi sudah 50 chat, 
   surat jalan hilang
3. Driver bolak-balik — BBM boros karena sistem assign 
   sopir manual via "siapa yang lagi standby"
4. Invoice telat — surat jalan paper berserakan, 
   nutup buku 10 hari

Solusi yang kami propose: bukan ERP corporate dari Big-4. 
Bukan blockchain. Bukan transformasi digital kosong.

[empty line]
4 mechanism konkret:

→ Sensor mini di tangki solar (BLE pair ke HP sopir, 
   sopir gak bisa matiin)
→ AI auto-assign sopir paling dekat + rute paling cepat 
   (Gojek pattern)
→ Driver app — sopir tap, foto, customer tanda tangan, 
   surat jalan auto-email
→ Admin upgrade dari data router jadi pengawas sistem

[empty line]
Hasil yang kami target untuk IRN: 

✓ Omset naik (trip lebih banyak, BBM hemat)
✓ Kecurangan turun (kencing solar tertangkap)
✓ Cash flow lancar (invoice ke-track)
✓ Tim Anda upgrade — Cici, Bu Rinda kerja strategic

Mau lihat detail? DM kami atau jadwalkan demo: 
[link calendly atau phone]

#LogistikIndonesia #BatamLogistik #AIIndonesia #PTIndusia
```

**Char count:** ~1180 (within 1100-1300 target).

**Hashtag:** 4-5 max, mix industri + brand.

**Voice:** lugas, story-first opener, list compact, CTA direct.

---

### Format 4 — PITCH DECK SLIDE (Embed Video Frame)

**Use case:** PT INDUSIA presentasi formal di kantor IRN. 1-slide intro sebelum play full proposal video.

**Slide layout:**

```
┌──────────────────────────────────────────────────────┐
│                                                       │
│   PT INDUSIA × IRN                                    │
│   ──────────                                          │
│                                                       │
│   AI + Digital untuk Operasional Anda                │
│                                                       │
│   23 truk Anda. 90 chassis. 4 masalah yang Anda      │
│   lebih tahu dari kami. 4 solusi yang kami bangun     │
│   spesifik untuk IRN.                                 │
│                                                       │
│   ┌─────────────────────────┐                        │
│   │  [Video thumbnail click  │  ← embed proposal      │
│   │   to play 3:40 video]    │     video full         │
│   │                          │                        │
│   │      ▶ 3:40              │                        │
│   └─────────────────────────┘                        │
│                                                       │
│                                       PT INDUSIA logo │
└──────────────────────────────────────────────────────┘
```

**Subsequent slides:** detail per pillar (1 slide per pain-solution pair, 4 slides), implementation timeline, team intro, pricing/scope (separate from public-facing video).

---

### Format 5 — EMAIL FOLLOW-UP

**Use case:** Setelah meeting awal dengan Pak Indra, PT INDUSIA email recap.

**Subject:** "PT INDUSIA × IRN — Recap Meeting + Proposal Video (3:40)"

**Body:**

```
Pak Indra Yth.,

Terima kasih atas waktu Anda kemarin di kantor IRN. Kami senang 
bisa diskusikan langsung 4 pain pattern yang Anda alami:

1. Kencing solar — 30% sopir, ~Rp 5jt/bulan bocor
2. Admin chaos WhatsApp — Cici 50 chat per pagi
3. Driver bolak-balik — BBM boros, trip yang hilang
4. Invoice telat — surat jalan hilang, cash terhambat

Berikut proposal video lengkap (3:40 menit) yang merangkum 
4 solusi kami:

[BUTTON: ▶ Tonton Proposal Video]
atau direct link: https://...

Bagian inti:
→ Sensor + AI triangulation untuk anti-kencing-solar
→ AI route + auto-assign sopir paling dekat (Gojek pattern)
→ Driver app + ePoD untuk surat jalan auto-email
→ Admin upgrade — Cici naik kelas

Schedule demo on-site di kantor IRN — ketika Anda siap, 
silakan pilih waktu di:

[BUTTON: 📅 Schedule Demo]

Kami siap visit, bawa hardware sample (sensor + tablet sopir 
demo), dan jalankan 1 truk pilot dalam 1 hari implementasi.

Salam,
[Founder/CEO PT INDUSIA]
[Contact phone WA]

---
Lampiran: PT INDUSIA — Detail Module Pricing (PDF)
```

**Voice:** semi-formal Indonesian B2B, langsung, structure clear, 2 CTA buttons.

---

### Format 6 — WHATSAPP SHARE SNIPPET

**Use case:** Pak Indra share video proposal ke saudara / kolega owner trucking di WhatsApp.

```
[Forwarded message]

Bro, ini agency yang kemarin bantu IRN. AI + digital 
untuk trucking, ngerti lapangan banget. 

Solve sopir nyolong solar, admin chaos, BBM boros, 
invoice telat. Proposal video 3:40 menit:

[link video]

Mereka kontak: [PT INDUSIA WA number]
```

**Voice:** Sangat casual, peer-to-peer. PT INDUSIA tidak menulis ini — Pak Indra yang share. Tapi PT INDUSIA bisa SUGGEST template ini ke Pak Indra setelah deal.

---

### Format 7 — ARTICLE LONG-FORM (5-10 min read, untuk SEO + thought leadership)

**Use case:** PT INDUSIA's blog/Medium artikel tentang IRN case study (versi general industri, tanpa nama IRN spesifik kalau confidentiality required).

**Outline:**

```
H1: Bagaimana AI + Digital Bantu Perusahaan Trucking Batam 
    Solve 4 Pain Lapangan

H2: Konteks: Trucking Batam Hari Ini
  - 23k+ kontainer impor per bulan via Batu Ampar
  - Owner 20-50 unit fleet stuck di margin tipis
  - Pain pattern yang berulang

H2: Pain Pattern #1 — Kencing Solar
  - Story konkret
  - Mechanism (root cause)
  - Quantified impact

H2: Pain Pattern #2 — Admin Chaos WhatsApp
H2: Pain Pattern #3 — Driver Bolak-Balik BBM Boros
H2: Pain Pattern #4 — Surat Jalan Hilang & Invoice Telat

H2: Solusi PT INDUSIA — AI + Digital yang Ngerti Lapangan

  H3: Solusi 1 — Fuel Sensor + AI Triangulation
  H3: Solusi 2 — AI Auto-Assign + Route Optimizer
  H3: Solusi 3 — Driver App + ePoD
  H3: Solusi 4 — Admin Upgrade

H2: Hasil yang Kami Target

H2: Mau Diskusi untuk Bisnis Anda?
```

**Voice:** lebih structured + edukatif daripada video. Boleh masukkan beberapa angka investasi karena audience disini = thought leadership reader, not cold audience.

---

## Voice Consistency Checklist (Cross-Format)

Setiap format produced harus pass test ini:

- [ ] Bahasa Indonesia + istilah Batam local konsisten?
- [ ] PT INDUSIA = speaker, IRN = subject?
- [ ] Pain numbers OK (5L solar, 50 chat, 10 hari closing)?
- [ ] Investment numbers absent atau minimal? (Hanya OK di pitch deck pricing slide + email pricing PDF lampiran)
- [ ] No banned vocab (sinergi, optimalisasi, dll)?
- [ ] No stock footage cliche (orang shake-hand, computer-with-graph generic)?
- [ ] Brand voice authority signal hadir? ("Kami ngerti kabin truk")
- [ ] Anti-feature filter applied (gak over-promise blockchain, AI-magic, dll)?
- [ ] CTA appropriate untuk format? (B2B proposal direct phone, social link in bio)
- [ ] Specific IRN data referenced? ("23 truk Anda" not generic "armada Anda")

Kalau >2 ❌, kembalikan ke creator.

---

## Cross-Format Production Pipeline

```
1. PT INDUSIA marketing brief
       │
       ▼
2. /creative-video-director — generate master storyline
       │
       │  output: master storyline JSON + brand voice locks
       ▼
3. /creative-video-director — invoke /video-brainstorm 
   dengan storyline pre-filled
       │
       ▼
4. /video-brainstorm → /video-script → /video-image → /video-gen
       │
       │  output: 3-4 min proposal video
       ▼
5. PARALLEL (untuk format lain):
   ├─ /carousel-gen → carousel 8-slide LinkedIn
   ├─ /linkedin-convert → LinkedIn text post
   ├─ /pitch-deck-storyline → pitch deck slide
   └─ /article-gen → blog long-form
       │
       ▼
6. /linkedin-validate + /article-validate + manual review
       │
       ▼
7. Asset library locked → distribute via PT INDUSIA channels
```

---

## Cross-References

- Master video storyboard → `04-video-format-decision.md`
- Brand voice doctrine (single SoT) → `01-brand-voice-doctrine.md`
- Archetype routing per audience format → `02-archetype-routing-hooks.md`
- System explanation craft (analogy library) → `03-system-explanation-craft.md`
- Anti-pattern veto → `06-creative-vetos.md`
- Production pipeline → `00-orchestration-playbook.md`
