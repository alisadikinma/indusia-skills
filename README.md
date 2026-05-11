# indusia-skills

Claude Code skills collection untuk **B2B AI projects di Indonesia** — dibangun oleh [PT INDUSIA Kecerdasan Digital](https://github.com/alisadikinma) untuk dipakai berulang lintas client.

Setiap skill = domain expert atau tech architect yang bisa dipanggil via `/<skill-name>` di Claude Code. Skills dirancang **tandem** — satu skill mendefinisikan kebutuhan bisnis, skill lain mendefinisikan implementasi teknis, satu lagi orkestrasi konten/kreatif.

## Skills

| Skill | Domain | Persona | Untuk Project |
|---|---|---|---|
| **pakar-logistik-batam** | Container trucking & crane rental di Batam | Konsultan logistik 15+ tahun, paham SITC/Infinity/Maersk, BP Batam FTZ, Bea Cukai SP2, anti-fraud sopir | Logistik & fleet operator Batam/Kepri |
| **smart-fleet-architect** | Fleet tech architecture (phone-as-GPS, ESP32 BLE, UHF RFID, CYMS) | Tech architect senior, Gojek-pattern advocate, IoT pragmatist | Tech overhaul fleet management 20-200 unit |
| **akuntan-indonesia-pro** | Akuntansi & perpajakan Indonesia | Akuntan publik, paham PPh, PPN, depresiasi, FRS UMKM, treatment kasbon | Finance modelling, pitch deck ROI, tax planning |
| **creative-video-director** | Creative orchestrator B2B logistik/fleet/industrial | Creative Director 12+ tahun B2B Indonesia, multi-format (video, carousel, LinkedIn, pitch deck) | Promo video, content campaign, sales material |

## Tandem Architecture

Setiap skill **bisa invoke standalone** untuk konsultasi langsung, ATAU **chained via `creative-video-director`** kalau output-nya untuk content/campaign work.

### Pattern 1 — Direct Invocation (Konsultasi Domain)

```
   USER
    │
    ├──▶ /pakar-logistik-batam        ← domain question (pain, archetype, ops advisory)
    ├──▶ /smart-fleet-architect       ← tech question (schema, hardware, integration)
    └──▶ /akuntan-indonesia-pro       ← finance question (pajak, ROI, closing)
```

### Pattern 2 — Orchestrated (Content & Campaign)

```
   USER
    │
    ▼
   ┌────────────────────────────┐
   │  creative-video-director   │  ← orchestrator (strategic brief layer)
   │  (content & campaign work) │
   └─┬──────────┬──────────┬────┘
     │ consults │ consults │ consults
     ▼          ▼          ▼
 pakar-     smart-     akuntan-
 logistik-  fleet-     indonesia-
 batam      architect  pro
 (DOMAIN)   (TECH)     (FINANCE — pitch deck only)
     │
     │ outputs creative brief, then routes to downstream production plugins:
     ▼
 ┌───────────────────────────────────────────────────────────────────┐
 │  ai-video-promo-engine  →  video-brainstorm → script → image → gen │
 │  ai-image-carousel-prompt-gen  →  carousel-gen                      │
 │  linkedin-post-writer   →  linkedin-gen                             │
 │  pitch-deck-designer    →  pitch-deck-brief → storyline → gen       │
 │  article-content-writer →  article-gen                              │
 └───────────────────────────────────────────────────────────────────┘
```

**Roles:**
- `pakar-logistik-batam` mendefinisikan **pain bisnis** + archetype customer + quotable hooks
- `smart-fleet-architect` mendefinisikan **mekanisme teknis** (high-level untuk video, deep untuk dev)
- `akuntan-indonesia-pro` mendefinisikan **ROI / CapEx / tax treatment** (untuk pitch deck only — JANGAN masuk video promo publik)
- `creative-video-director` orkestrasi 3 specialist di atas → output brief multi-format → route ke production plugin yang fit

**Catatan:** Downstream production plugins (`ai-video-promo-engine`, `linkedin-post-writer`, dll.) **tidak dibundle di repo ini** — install terpisah. Repo ini fokus ke 4 strategic/domain skill yang generic untuk B2B Indonesia.

## Install

### Via Claude Code Plugin Marketplace

```bash
/plugin marketplace add alisadikinma/indusia-skills
/plugin install indusia-skills@indusia-skills
```

### Manual (clone + symlink)

```bash
git clone https://github.com/alisadikinma/indusia-skills.git
# Lalu symlink/copy folder skills/* ke ~/.claude/skills/
```

## Update

```bash
cd indusia-skills
git pull
# Restart Claude Code untuk reload skill
```

## Versioning

Repo ini diversi-kan via `version` field di `.claude-plugin/plugin.json`. Setiap perubahan skill → bump version → tag release.

| Version | Date | Highlights |
|---|---|---|
| 0.1.0 | 2026-05-11 | Initial release — 4 skills: pakar-logistik-batam, smart-fleet-architect, akuntan-indonesia-pro, creative-video-director |

## Naming Convention (Future Skills)

| Prefix | Pattern | Contoh |
|---|---|---|
| `pakar-*` | Domain expert per vertical | `pakar-logistik-batam`, `pakar-perkebunan-sawit`, `pakar-konstruksi-epc` |
| `smart-*-architect` | Tech architect per vertical | `smart-fleet-architect`, `smart-mill-architect`, `smart-warehouse-architect` |
| `akuntan-indonesia-*` | Finance specialist (PSAK + tax ID) | `akuntan-indonesia-pro`, `akuntan-indonesia-umkm` |
| `creative-*-director` | Creative orchestrator per scope | `creative-video-director`, `creative-pitch-director` |

## License

MIT — lihat [LICENSE](LICENSE).

## Author

[Ali Sadikin](https://github.com/alisadikinma) — Founder PT INDUSIA Kecerdasan Digital. Building AI-powered systems untuk perusahaan logistik, manufaktur, perkebunan, dan EPC di Indonesia.
