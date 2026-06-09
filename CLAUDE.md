# indusia-skills — Claude Plugin Instructions

## 🕸️ graphify (code-structure brain — HOW)

This repo has a knowledge graph at `graphify-out/` (god nodes, community structure, cross-file relationships) — the **HOW (code structure)** half of the two-brain setup in `~/CLAUDE.md` (the Obsidian vault holds the WHY).

**Rules:**
- **Graph for structure, grep for literals — complementary, not either/or.** Use `graphify query "<question>"` for "where/how is X / what calls Y / which files relate" — it returns a scoped subgraph, far smaller than `GRAPH_REPORT.md` or raw output (local default `graphify-out/graph.json`). Keep grep/Read for literal strings AND code edited since the last `graphify update` (the graph is AST-indexed, not live).
- Cross-project / "don't know which repo" → federated graph: `graphify query "<question>" --graph ~/.graphify/global-graph.json`.
- Read `graphify-out/GRAPH_REPORT.md` only for broad architecture review when query doesn't surface enough.
- **After modifying code, run `graphify update .`** (AST-only, no API cost) so the graph doesn't go stale. Refresh the global brain: `graphify global add graphify-out/graph.json --as indusia-skills`.

## 🧠 Vault Context Link

Skill library — generic INDUSIA agency skills (creative-video-director, pakar-logistik-batam, smart-fleet-architect, akuntan-indonesia-pro, senior-ux-architect-id). Agnostic terhadap klien (IRN, future SME logistik di Batam, etc).

Pre-read kalau perlu konteks:
- `20-Projects/INDUSIA-AI/README.md` — agency positioning, 3-pillar thesis (AI + IoT + Mobile)
- `20-Projects/IRN-Logistik/README.md` — primary reference klien untuk skill validation
- `20-Projects/claude-plugin/README.md` — skill ecosystem overview
- `10-Identity/voice-tone.md` — Bahasa Indonesia voice
- `10-Identity/positioning.md` — INDUSIA USP (auto-loaded global)

## Project Boundary (CRITICAL)

| Layer | Lokasi | Isi |
|---|---|---|
| **Skill library (HERE)** | `D:\Projects\indusia-skills\skills\` | Framework, archetype, hook, narrative patterns. Pakai `{{placeholder}}` syntax. |
| **Project bindings** | `D:\Projects\Indusia-AI-Logistik\project-variables.md` | IRN-specific substitution values |
| **Project locks** | `D:\Projects\Indusia-AI-Logistik\CLAUDE.md` | 3-pillar positioning, hardware locks, skill routing map |
| **Production output** | `D:\project-video\{Project}\output\` | Final substituted deliverables |

**Rule of thumb:** Knowledge tentang *bagaimana* (pattern/framework) → di sini. Knowledge tentang *klien-specific* (nama, jumlah, angka) → `project-variables.md`.

## Anti-patterns

- ❌ Hardcode klien values (e.g., "IRN", "23 fleet", "Pak Indrajaya") di skill files
- ❌ Edit skill output langsung; edit di project output folder
- ❌ Bypass strategic layer (creative-video-director) saat buat video brief
- ✅ Pakai `{{placeholder}}` syntax universal — substitute saat run skill di project context

## Skills Available

Cek `skills/` folder + masing-masing `SKILL.md` untuk full description. High-level:

- `creative-video-director` — strategic brief untuk video promo (archetype, hook, 5-beat)
- `pakar-logistik-batam` — domain advisor logistik kontainer + crane Batam
- `smart-fleet-architect` — hardware/architecture design (fuel sensor, RFID, ESP32)
- `akuntan-indonesia-pro` — Indonesian accounting (PPh 23, e-faktur, dashboard angka)
- `senior-ux-architect-id` — UX screens (driver app, owner dashboard, Asisten chat)
- `ai-business-architect` — maverick AI business-model & business-development strategist (out-of-the-box monetization, pricing, incumbent collaboration: JV/BOT/white-label/gainshare)

## Repository

GitHub: https://github.com/alisadikinma/indusia-skills
