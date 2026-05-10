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

```
       USER (founder/PM/marketer)
                │
                ▼
   ┌────────────────────────────┐
   │  creative-video-director   │  ← orchestrator
   │  (content & campaign work) │
   └─┬──────────┬──────────┬────┘
     │          │          │
     ▼          ▼          ▼
 pakar-     smart-     akuntan-
 logistik-  fleet-     indonesia-
 batam      architect  pro
 (DOMAIN)   (TECH)     (FINANCE)
```

- `pakar-logistik-batam` mendefinisikan **pain bisnis** + archetype customer
- `smart-fleet-architect` mendefinisikan **mekanisme teknis** (high-level untuk video, deep untuk dev)
- `akuntan-indonesia-pro` mendefinisikan **ROI / CapEx / tax treatment** (untuk pitch deck only)
- `creative-video-director` orkestrasi semua → output brief multi-format

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
