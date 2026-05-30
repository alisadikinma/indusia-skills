# Geminigen API Client Plugin — Design (Brainstorm Complete)

**Status:** Brainstorm complete (Phase 0-5). Ready for `gaspol-plan` handoff.
**Date:** 2026-05-14
**Owner:** Ali Sadikin

## Design

### Goal
Plugin baru `geminigen-api-client` berisi 2 skill:
- `/geminigen-image` — call `POST /generate_image` (nano-banana-pro, nano-banana-2, imagen-4)
- `/geminigen-video` — route ke `/video-gen/veo`, `/video-gen/seedance`, `/video-gen/kling`, `/video-extend/veo`, `/video-extend/seedance`

Bridge gap antara existing prompt-generator skills (`/video-gen`, `/article-images`, `/carousel-gen` — yang output prompt text saja) dengan GeminiGen rendering — tanpa harus manual paste ke UI.

### Architecture Decisions (locked)

| Topic | Decision | Rationale |
|---|---|---|
| Repo placement | Plugin baru `geminigen-api-client` (standalone) | Cross-project reusable; konsisten dengan separation strategic (indusia-skills) vs production tools |
| Skill granularity | 2 skills (image vs video) | User preference: cleaner mental model; shared HTTP client di scripts/ |
| Language | Python | Consistent dengan jobhunter, article-content-writer scripts patterns |
| Async story | Polling default + `--no-wait` opt-out | Webhook not feasible from CLI; History API official fallback per docs |
| Input modes | (1) inline args (2) from-file (sibling .md) (3) from-manifest (JSON) | Covers ad-hoc, pipeline-driven, and batch use cases |
| Ref files | Auto-detect local path vs URL | Single `--ref` flag, transparent to user |
| Output | Auto-download to `./geminigen-output/<type>/...` + print URL | Local artifact always available; URL for sharing |
| Config | 3-tier: CLI arg > env > config file | Standard pattern; matches Portfolio_v2 |
| Error handling | Surface `error_message`, exit codes, exponential backoff on 5xx | No auto-rewrite (too expensive for CLI) |
| Phase 3 (UI design) | SKIPPED | No UI, pure CLI |

### Plugin Layout

```
geminigen-api-client/
├── .claude-plugin/plugin.json
├── skills/
│   ├── geminigen-image/SKILL.md
│   └── geminigen-video/SKILL.md
├── scripts/
│   ├── geminigen_client.py       # shared HTTP + polling + download
│   ├── geminigen_image.py        # entry: image
│   └── geminigen_video.py        # entry: video (sub-router)
├── references/
│   ├── api-image-spec.md
│   ├── api-video-spec.md
│   ├── style-presets.md
│   └── model-decision-tree.md
├── .env.example
├── requirements.txt
└── README.md
```

### Subcommand Surface

```bash
# Image
/geminigen-image "<prompt>" [--model nano-banana-pro|nano-banana-2|imagen-4] \
                            [--aspect 1:1|16:9|9:16|4:3|3:4] \
                            [--style Photorealistic|Cinematic|Anime|...] \
                            [--resolution 1K|2K|4K] [--format jpeg|png] \
                            [--ref <local-or-url>] ... [--ref-history <uuid>] \
                            [--output-dir <path>] [--no-wait]

# Video — VEO
/geminigen-video veo "<prompt>" [--model veo-3.1|veo-3.1-fast|veo-3.1-lite|veo-2] \
                                [--duration 4|6|8] [--resolution 720p|1080p] \
                                [--aspect 16:9|9:16] [--mode frame|ingredient] \
                                [--ref ...] [--output-dir ...] [--no-wait]

# Video — Seedance
/geminigen-video seedance "<prompt>" [--model seedance-2|seedance-2-omni] \
                                     [--mode fast|pro|fast-2|pro-2|fast-vip|pro-vip] \
                                     [--duration 4-15] [--aspect 16:9|9:16|1:1|3:4|4:3|21:9] \
                                     [--ref-image ...] [--ref-video ...] [--ref-audio ...]

# Video — Kling
/geminigen-video kling "<prompt>" [--model kling-video-3-0|...] \
                                  [--mode standard|professional|professional_audio|relax] \
                                  [--aspect 16:9|9:16|1:1] [--duration 3-15] \
                                  [--ref-image ...] [--ref-video ...]

# Extend
/geminigen-video extend veo --ref-history <uuid> "<prompt>"
/geminigen-video extend seedance --ref-history <uuid> "<prompt>"

# Batch / pipeline
/geminigen-image from-file <path>          # parse image prompts from .md
/geminigen-image from-manifest <path>      # JSON batch
/geminigen-video from-file <path>          # parse video-prompts.md scene blocks
/geminigen-video from-manifest <path>      # JSON batch

# Utilities
/geminigen-image check <uuid>              # poll one job status
/geminigen-video check <uuid>
/geminigen-image list [--limit 20]         # recent generations
/geminigen-video list [--limit 20]
```

### Data Integration Map

See full table in Phase 5 section of brainstorm session — every input/output is REAL, no placeholders. Critical: History API endpoint exact path TBD on first run (probe `/history/{uuid}` and `/generations/{uuid}`).

### Open Risks

1. **History API path** — not in PDFs supplied. Mitigation: probe + `--debug` flag during implementation; user may supply additional PDF later.
2. **Long-running video polling** — Veo/Seedance can take 2-5 min. Mitigation: progress bar from `status_percentage`; `--no-wait` for batch workflows.
3. **Rate limit** — nano-banana-pro: 5/min, 100/hr, 1000/day. Mitigation: client-side throttle; respect 429 with backoff.

---

## Implementation Plan

> **For Claude:** REQUIRED SKILL: Use gaspol-execute to implement this plan.
> **CRITICAL:** This plan specifies real integrations. During execution,
> NEVER substitute placeholders for real data sources without explicit
> user approval. If a data source doesn't exist yet, STOP and ask.

### Goal

Build a standalone Claude Code plugin `geminigen-api-client` containing two skills (`/geminigen-image` and `/geminigen-video`) that render actual media from prompts by calling the GeminiGen.ai HTTP API. The plugin bridges the gap between existing prompt-generator skills (which output text only) and GeminiGen rendering, so users no longer need to manually paste prompts into the GeminiGen web UI.

### Architecture Context

**Plugin lives at:** `D:\Projects\claude-plugin\geminigen-api-client\` (sibling to other Mas Ali plugins like `article-content-writer`, `ai-video-promo-engine`).

**No CLAUDE.md in indusia-skills repo** — this plan is for a NEW external plugin. The relevant context sources are:

- **Reference implementation (image):** `D:\Projects\Portfolio_v2\backend\app\Services\ImageGenerationService.php` — proven production code for `POST /generate_image`, including multipart shape, webhook routing, retry FSM. We port the *API call patterns* to Python; the safety-rewrite + queue + DB persistence are server-side and intentionally OUT of scope for CLI skill.
- **API docs (full):** `D:\my-data\knowledge\*.pdf` — 7 PDFs covering image (nano-banana), webhook spec, VEO, VEO-extend, Seedance, Seedance-extend, Kling.
- **Skill convention:** `~/.claude/plugins/cache/alisadikinma-ai-content-suite/article-content-writer/2.3.0/` — Python scripts pattern, SKILL.md frontmatter, `.claude-plugin/plugin.json` shape.
- **Sibling-skill output formats (for from-file parser):**
  - `/article-images` → JSON payload posted to backend (we read it as exported `.json`)
  - `/video-gen` → `video-prompts.md` with scene blocks (NB2 + VEO sections per scene)
  - `/carousel-gen` → carousel prompts `.md` with slide-by-slide blocks

**External API (all endpoints):** see `## Reference Implementation Map` section above for the full request/response spec.

### Tech Stack

| Layer | Choice | Why |
|---|---|---|
| Language | Python 3.10+ | Consistent with `jobhunter`, `article-content-writer` script pattern |
| HTTP | `requests` (≥2.31) | Battle-tested multipart, retry support via `urllib3.Retry` |
| CLI parsing | `click` (≥8.1) | Subcommands + auto-help; matches conventions in skill ecosystem |
| Progress UI | `tqdm` (≥4.66) | Polling progress bar from `status_percentage` |
| Concurrent batch | `concurrent.futures.ThreadPoolExecutor` | stdlib, sufficient for I/O-bound API calls |
| Config parsing | stdlib `json` + `os.environ` + `pathlib` | 3-tier resolver, no extra deps |
| Markdown parsing | stdlib `re` (regex blocks) | Sibling-skill outputs are deterministic-format, no full parser needed |
| Testing | `pytest` + `pytest-mock` + `responses` (HTTP mocking) | Standard Python TDD stack |

### Data Integration Map

| Feature | Data Source | Module/Function | Exists? | Action |
|---|---|---|---|---|
| API key resolution | CLI arg `--api-key` > env `GEMINIGEN_API_KEY` > `~/.config/geminigen/config.json` | `geminigen_client.resolve_api_key()` | No | Create — 3-tier fallback |
| HTTP POST submission | `https://api.geminigen.ai/uapi/v1/{endpoint}` | `geminigen_client.submit()` | No | Create — multipart with auto local/URL detection |
| Local file ref upload | `requests.post(..., files={...})` | `geminigen_client.build_multipart()` | No | Create — open file, attach as multipart file |
| URL ref attachment | Form-string field | Same | No | Create — detect `http(s)://` prefix |
| Polling status | `GET /uapi/v1/history/{uuid}` (path TBD — probe on first run) | `geminigen_client.poll()` | No | Create — backoff 5s img / 15s video, max 5min / 15min |
| Status percentage display | `response.status_percentage` field | `geminigen_client.poll()` with `tqdm` | No | Create |
| Download generated media | `requests.get(generate_result)` → write bytes | `geminigen_client.download()` | No | Create — mirror Portfolio_v2 `downloadAndStore()` |
| Error response parsing | `response.error_message` + HTTP status | `geminigen_client.handle_error()` | No | Create — exit codes per error class |
| Retry on 5xx | `urllib3.util.retry.Retry` | `geminigen_client.session()` | Library | Configure — 3 attempts, exponential backoff |
| Rate limit awareness | Client-side throttle: 5/min for nano-banana-pro | `geminigen_client.throttle()` | No | Create — sliding-window counter |
| Image submission entry | `geminigen_image.py` `image()` command | `click.command` | No | Create |
| Video submission entry | `geminigen_video.py` `veo()/seedance()/kling()/extend()` subgroups | `click.group` | No | Create |
| from-file parser (image-prompts.md) | Markdown file with `### Image N` blocks | `parsers.parse_image_prompts_md()` | No | Create per sibling-skill format |
| from-file parser (video-prompts.md) | Markdown file with `### Scene NN` blocks | `parsers.parse_video_prompts_md()` | No | Create per sibling-skill format |
| from-file parser (carousel slides) | JSON file from `/carousel-gen` | `parsers.parse_carousel_json()` | No | Create per sibling-skill format |
| from-manifest validator | JSON file with `jobs[]` array | `parsers.validate_manifest()` | No | Create with jsonschema-free validation |
| Output dir | `./geminigen-output/{type}/{ts}-{uuid}.{ext}` | `geminigen_client.output_path()` | No | Create with `--output-dir` override |
| SKILL.md (image) | `skills/geminigen-image/SKILL.md` | New file | No | Create following article-content-writer pattern |
| SKILL.md (video) | `skills/geminigen-video/SKILL.md` | New file | No | Create following article-content-writer pattern |
| Plugin manifest | `.claude-plugin/plugin.json` | New file | No | Create per article-content-writer schema |
| Reference docs (4) | `references/*.md` | New files | No | Create from PDF synthesis |

**Contract:** Every row marked "No (action: Create)" must be a REAL working integration during execution. NO placeholders. NO `# TODO` stubs. If the History API path probe fails, STOP and ask the user before proceeding.

### Phase Structure

13 phases. Phases 4-7 and Phases 8-10 contain independent work that can be parallelized via `gaspol-parallel`.

---

### Phase 0: Plugin Skeleton

**Estimated time:** 8 minutes

**Files:**
- Create: `D:\Projects\claude-plugin\geminigen-api-client\.claude-plugin\plugin.json`
- Create: `D:\Projects\claude-plugin\geminigen-api-client\.gitignore`
- Create: `D:\Projects\claude-plugin\geminigen-api-client\.env.example`
- Create: `D:\Projects\claude-plugin\geminigen-api-client\requirements.txt`
- Create: `D:\Projects\claude-plugin\geminigen-api-client\pyproject.toml`
- Create: `D:\Projects\claude-plugin\geminigen-api-client\README.md` (seed; finalized Phase 12)
- Create: `D:\Projects\claude-plugin\geminigen-api-client\tests\__init__.py`
- Test: `D:\Projects\claude-plugin\geminigen-api-client\tests\test_skeleton.py`

**Steps:**
1. Write failing test for plugin skeleton sanity: `def test_plugin_manifest_exists(): assert Path('.claude-plugin/plugin.json').exists()` + assertion that the file parses as JSON with required keys (`name`, `version`, `description`). Expected error: `FileNotFoundError`.
2. Run `pytest tests/test_skeleton.py`, confirm it fails for the expected reason.
3. Create `.claude-plugin/plugin.json` with name=`geminigen-api-client`, version=`0.1.0`, description, author, license=MIT. Mirror `article-content-writer/plugin.json` schema.
4. Create `requirements.txt` with pinned versions: `requests>=2.31`, `click>=8.1`, `tqdm>=4.66`, `pytest>=7.4`, `pytest-mock>=3.12`, `responses>=0.24`.
5. Create `.env.example` with `GEMINIGEN_API_KEY=` and a one-line comment pointing to https://api.geminigen.ai/.
6. Create `.gitignore` (Python defaults + `.env` + `geminigen-output/`).
7. Run tests, confirm pass. Commit: `chore: scaffold geminigen-api-client plugin`.

**Verification:**
- [ ] `python -m json.tool .claude-plugin/plugin.json` exits 0
- [ ] `pytest tests/test_skeleton.py` passes
- [ ] `pip install -r requirements.txt` runs clean in a fresh venv
- [ ] No placeholder/TODO comments in new code

---

### Phase 1: Shared HTTP Client — Core (Auth + Multipart + Submit)

**Estimated time:** 14 minutes

**Files:**
- Create: `scripts/geminigen_client.py` (functions: `resolve_api_key`, `build_multipart`, `submit`, `_session`)
- Test: `tests/test_client_core.py`

**Steps:**
1. Write failing test for 3-tier api key resolver: `test_resolve_api_key_prefers_cli_arg_over_env_over_config_file` covering all 3 sources + missing-key error. Expected error: `ImportError: cannot import name 'resolve_api_key'`.
2. Run, confirm fail.
3. Implement `resolve_api_key(cli_arg, env_name='GEMINIGEN_API_KEY', config_path='~/.config/geminigen/config.json')` returning the resolved key or raising `MissingApiKeyError`.
4. Write failing test for `build_multipart`: given prompt + model + ref list mixing local paths (existing file) and URLs, returns a `files` dict for local + form data list for URLs/scalars. Use `tmp_path` for local file fixture. Expected error: function not implemented.
5. Implement `build_multipart(fields: dict, ref_files: list[str], ref_field_name: str='file_urls') -> tuple[dict, list[tuple]]` that auto-detects `http(s)://` prefix vs local path and validates file size against per-endpoint cap (param `max_file_size_mb`).
6. Write failing test for `submit(endpoint, fields, refs, api_key)`: mocks `responses` library to assert POST to `https://api.geminigen.ai/uapi/v1/{endpoint}` with `x-api-key` header and parsed response dict. Verify retry behavior on 5xx (3 attempts).
7. Implement `submit()` using a configured `requests.Session` with `urllib3.Retry(total=3, backoff_factor=1, status_forcelist=[500,502,503,504])`.
8. Run all client tests, confirm pass. Commit: `feat: add geminigen client core (auth + multipart + submit)`.

**Verification:**
- [ ] All 7+ unit tests pass
- [ ] `mypy scripts/geminigen_client.py --strict` reports no errors (or pyright)
- [ ] No placeholder/TODO comments in new code
- [ ] Manual smoke (with real API key in env): `python -c "from scripts.geminigen_client import resolve_api_key; print(bool(resolve_api_key()))"` prints `True`

---

### Phase 2: Shared HTTP Client — Polling + Download + Error Handling

**Estimated time:** 14 minutes

**Files:**
- Modify: `scripts/geminigen_client.py` (add `poll`, `download`, `handle_error`, `output_path`)
- Test: `tests/test_client_async.py`

**Steps:**
1. Write failing test for `poll(uuid, kind='image', timeout=300)`: mock `responses` to return status=1 twice, then status=2 with `generate_result`. Assert function returns the parsed response dict on completion. Expected error: function not implemented.
2. Run, confirm fail.
3. Write failing test for `poll` failure: mock returns status=3 with `error_message`. Assert raises `GeminiGenError` with the message.
4. Write failing test for `poll` timeout: mock returns status=1 indefinitely. Assert raises `PollTimeoutError` after configured timeout.
5. Implement `poll(uuid, kind, timeout=300, interval=5)`. **Important:** Probe `GET /uapi/v1/history/{uuid}` first; if 404, try `GET /uapi/v1/generations/{uuid}`. Cache the discovered path in `~/.config/geminigen/.discovered_history_path`. If both fail, raise `HistoryPathNotFoundError` with actionable guidance ("Share GeminiGen History API doc with maintainer").
6. Write failing test for `download(url, target_path)`: mock returns 1024 bytes of bytes, assert file written with exact bytes. Test with too-small body asserts `DownloadIntegrityError`.
7. Implement `download()` mirroring `Portfolio_v2 ImageGenerationService::downloadAndStore` — 30s timeout, ≥1024 bytes guard, write atomically (`.tmp` + rename).
8. Write failing test for `output_path(kind='video', uuid='abc', ext='mp4')`: returns `Path('./geminigen-output/video/{ts}-abc.mp4')` and creates parent dirs.
9. Implement `output_path()` and `handle_error(resp, exit_code_map)` (maps API error to exit codes 1/2/3 per error class).
10. Run all tests, confirm pass. Commit: `feat: add polling + download + error handling to client`.

**Verification:**
- [ ] All 10+ unit tests pass (sync + async paths)
- [ ] History API path probe is implemented and tested (BOTH candidate paths)
- [ ] No placeholder/TODO comments in new code
- [ ] `pytest tests/ -v` reports all green

---

### Phase 3: Image Entry Point + SKILL.md

**Estimated time:** 12 minutes

**Files:**
- Create: `scripts/geminigen_image.py` (click commands: `image` (default), `check`, `list`)
- Create: `skills/geminigen-image/SKILL.md`
- Test: `tests/test_geminigen_image.py`

**Steps:**
1. Write failing test for `image` command inline-args path using `click.testing.CliRunner`: invokes `python -m geminigen_image image "A sunset" --aspect 16:9 --no-wait --api-key fake`, mocks `submit()` to return `{"uuid": "abc"}`, asserts stdout contains `abc` and exit=0. Expected error: file/module not found.
2. Run, confirm fail.
3. Implement `geminigen_image.py` with `click.group()` and 3 subcommands: `image` (submit + auto-poll unless `--no-wait`), `check <uuid>` (poll once), `list [--limit N]` (calls `GET /uapi/v1/history?limit=N&type=image`).
4. All 5 image-specific options wired: `--model`, `--aspect`, `--style`, `--resolution`, `--format`, plus shared `--ref` (multi), `--ref-history`, `--output-dir`, `--no-wait`, `--api-key`. Enum validation on choices.
5. Write failing test for full sync path: mock submit + poll + download, assert final stdout has local file path AND CDN URL printed.
6. Implement the sync path: submit → poll w/ tqdm progress → download → print both paths.
7. Write `skills/geminigen-image/SKILL.md` following `article-content-writer/skills/article-images/SKILL.md` structure: frontmatter (name + description with triggers), Pipeline Mode section, Usage examples, Reference Files table pointing to `references/api-image-spec.md`, `references/style-presets.md`.
8. Run all tests, confirm pass. Commit: `feat: add geminigen-image skill + entry point`.

**Verification:**
- [ ] `python -m scripts.geminigen_image image --help` shows complete option surface
- [ ] All 5+ image tests pass
- [ ] SKILL.md frontmatter `name` matches plugin manifest skill registry
- [ ] No placeholder/TODO comments in new code
- [ ] Manual: with real API key, `python -m scripts.geminigen_image image "test sunset" --no-wait` returns a UUID

---

### Phase 4: Video Entry Point + VEO Subcommand + SKILL.md Scaffold

**Estimated time:** 12 minutes

**Files:**
- Create: `scripts/geminigen_video.py` (click group: `veo`, `seedance`, `kling`, `extend`, `check`, `list`)
- Create: `skills/geminigen-video/SKILL.md`
- Test: `tests/test_geminigen_video_veo.py`

**Steps:**
1. Write failing test for `veo` subcommand: `CliRunner().invoke(cli, ['veo', 'A serene lake', '--model', 'veo-3.1', '--duration', '8', '--no-wait'])`. Mock submit returns `{"uuid": "xyz"}`. Assert exit=0 and `xyz` in stdout. Expected error: module not found.
2. Run, confirm fail.
3. Implement `geminigen_video.py` with `@click.group()` containing `veo`, `seedance`, `kling`, `extend`, `check`, `list` subcommands. Implement `veo` first with options: `--model` (choices: veo-3.1/veo-3.1-fast/veo-3.1-lite/veo-2, default veo-3.1), `--resolution` (720p/1080p), `--duration` (4/6/8), `--aspect` (16:9/9:16), `--mode` (frame/ingredient), `--ref` (multi, max 2 frame / 3 ingredient — validated), `--output-dir`, `--no-wait`, `--api-key`.
4. Validate ref count: if `--mode frame` then `len(refs) <= 2`; if `--mode ingredient` then `len(refs) <= 3`. Raise `BadParameter` otherwise.
5. Write failing test for ref count validation: 3 refs + `--mode frame` → exits non-zero with helpful message.
6. Write SKILL.md scaffold for `geminigen-video` with frontmatter + sections for each subcommand. Fill veo section now; leave seedance/kling/extend as `_(see Phase 5-6)_` placeholders — these WILL be filled in this same plan.
7. Run all tests, confirm pass. Commit: `feat: add geminigen-video skill + veo subcommand`.

**Verification:**
- [ ] `python -m scripts.geminigen_video veo --help` shows complete option surface
- [ ] Ref count validation works (test asserts error path)
- [ ] All 4+ veo tests pass
- [ ] No placeholder/TODO comments EXCEPT documented section markers in SKILL.md that will be filled in subsequent phases

---

### Phase 5: Video — Seedance + Kling Subcommands

**Estimated time:** 14 minutes

**Files:**
- Modify: `scripts/geminigen_video.py` (add `seedance` + `kling` subcommands)
- Modify: `skills/geminigen-video/SKILL.md` (fill seedance + kling sections)
- Test: `tests/test_geminigen_video_seedance.py`, `tests/test_geminigen_video_kling.py`

**Steps:**
1. Write failing test for `seedance` subcommand: invokes with `--model seedance-2-omni --mode pro-2 --duration 10 --aspect 21:9 --ref-image a.jpg --ref-video b.mp4 --ref-audio c.mp3`. Assert correct endpoint hit. Expected error: subcommand not registered.
2. Run, confirm fail.
3. Implement `seedance` with options: `--model` (seedance-2/seedance-2-omni), `--mode` (fast/pro/fast-2/pro-2/fast-vip/pro-vip — validate against model: vip/2 modes are omni-only), `--duration` (4-15, int range), `--aspect` (6 choices), `--ref-image` (multi: max 1 first + 1 last for seedance-2; max 4 ingredient for omni), `--ref-video` (omni only), `--ref-audio` (omni only). Per-file size validation (15MB image, 60MB video, 15s audio).
4. Write failing test for `kling` subcommand: invokes with `--model kling-video-3-0 --mode professional --duration 8 --aspect 9:16 --ref-image x.png`. Assert correct endpoint and prompt min-length=10 enforced.
5. Implement `kling` with options: `--model` (11 choices), `--mode` (standard/professional/professional_audio for 2-6 only / relax for 2-5 only — cross-validate), `--aspect` (16:9/9:16/1:1), `--duration` (3-15), `--ref-image` (max 10MB jpg/png), `--ref-video` (max 100MB mp4/mov/webm, 120s, REQUIRED for motion/edit models).
6. Fill SKILL.md seedance + kling sections with usage examples + option tables.
7. Run all tests, confirm pass. Commit: `feat: add seedance + kling subcommands`.

**Verification:**
- [ ] Both subcommands routed to correct endpoints (`/video-gen/seedance`, `/video-gen/kling`)
- [ ] Per-model mode validation tested (omni-only modes rejected for seedance-2; professional_audio only for kling-video-2-6)
- [ ] Per-file size limits enforced
- [ ] All 8+ tests pass
- [ ] No placeholder/TODO comments in new code

---

### Phase 6: Video — Extend Subcommands (veo + seedance)

**Estimated time:** 8 minutes

**Files:**
- Modify: `scripts/geminigen_video.py` (add `extend` group: `extend veo`, `extend seedance`)
- Modify: `skills/geminigen-video/SKILL.md` (fill extend section)
- Test: `tests/test_geminigen_video_extend.py`

**Steps:**
1. Write failing test for `extend veo --ref-history <uuid> "<prompt>"`: assert POST to `/uapi/v1/video-extend/veo` with `ref_history` field. Expected error: subcommand not registered.
2. Run, confirm fail.
3. Implement `extend` as `@click.group()` containing `veo` and `seedance` subcommands. Each takes positional `prompt` + required `--ref-history <uuid>`. NO other options (model/aspect/resolution inherited from source per docs).
4. Write failing test for `extend seedance`: same shape, different endpoint.
5. Implement seedance extend variant.
6. Fill SKILL.md extend section.
7. Run tests, confirm pass. Commit: `feat: add video extend subcommands`.

**Verification:**
- [ ] Both extend subcommands hit correct endpoints
- [ ] `--ref-history` required, errors helpfully when missing
- [ ] All 4+ tests pass
- [ ] No placeholder/TODO comments in new code

---

### Phase 7: Utility Subcommands (`check` + `list`) — Image & Video

**Estimated time:** 10 minutes

**Files:**
- Modify: `scripts/geminigen_image.py` + `scripts/geminigen_video.py` (`check`, `list` subcommands)
- Modify: both SKILL.md files (utility section)
- Test: `tests/test_check_and_list.py`

**Steps:**
1. Write failing test for `check <uuid>`: mock `poll()` to return status=2 dict, assert tabular stdout including model, status, percentage, generate_result URL. Expected error: subcommand not registered.
2. Run, confirm fail.
3. Implement `check` in both entry-point modules (thin wrapper around `client.poll(uuid, ...)` with one-shot mode `interval=0`).
4. Write failing test for `list --limit 5`: mock `GET /uapi/v1/history?limit=5` (or discovered list path), assert N rows printed.
5. Implement `list`: probe `GET /uapi/v1/history` then `GET /uapi/v1/generations` (parallel to phase 2's path discovery cache). Render via `tabulate`-free stdlib pretty-print (don't add new dep).
6. Add doc note in SKILL.md: list filter `--type image/video` for cross-filter.
7. Run tests, confirm pass. Commit: `feat: add check + list utility subcommands`.

**Verification:**
- [ ] Both `check` and `list` work in image AND video skills
- [ ] List uses cached history path from Phase 2 (no re-probe per call)
- [ ] All 4+ tests pass
- [ ] No placeholder/TODO comments in new code

---

### Phase 8: From-File Parsers (sibling-skill output integration)

**Estimated time:** 14 minutes

**Files:**
- Create: `scripts/parsers.py` (parse_image_prompts_md, parse_video_prompts_md, parse_carousel_json)
- Create: `tests/fixtures/image-prompts-sample.md`, `tests/fixtures/video-prompts-sample.md`, `tests/fixtures/carousel-sample.json` (anonymized real samples from sibling-skill outputs)
- Test: `tests/test_parsers.py`

**Steps:**
1. **First:** Inspect actual format. Read 1 real sample of each:
   - `~/.claude/plugins/cache/alisadikinma-ai-content-suite/article-content-writer/.../skills/article-images/SKILL.md` (find example output format)
   - `~/.claude/plugins/cache/alisadikinma-ai-content-suite/ai-video-promo-engine/.../skills/video-gen/SKILL.md` (find video-prompts.md example)
   - Check `D:\Projects\Portfolio_v2\docs\plans\hero-video\image-prompts.md` for real example
2. Write failing test for `parse_image_prompts_md(path)`: given fixture, returns `list[dict]` with keys `index`, `concept`, `prompt`, `aspect_ratio`, `refs`. Expected error: file not found.
3. Run, confirm fail.
4. Implement `parse_image_prompts_md` using regex block extraction (heading-anchored sections).
5. Write failing test for `parse_video_prompts_md`: given fixture, returns `list[dict]` per scene with keys `scene_index`, `mode` (frame/ingredient), `prompt`, `duration`, `aspect_ratio`, `refs`, `model_hint` (veo vs seedance from doc context).
6. Implement `parse_video_prompts_md`.
7. Write failing test for `parse_carousel_json`: given fixture, returns `list[dict]` per slide.
8. Implement `parse_carousel_json` (trivial JSON load + schema validate).
9. Wire `from-file <path>` subcommand on BOTH skills: detect file type by extension + content, route to correct parser, dispatch batch.
10. Write integration test: `geminigen_image from-file tests/fixtures/image-prompts-sample.md --no-wait` submits N jobs and prints N UUIDs.
11. Run all tests, confirm pass. Commit: `feat: add sibling-skill output parsers + from-file mode`.

**Verification:**
- [ ] 3 parsers each have ≥2 tests (happy path + malformed)
- [ ] from-file integration test passes
- [ ] Fixtures committed (anonymized, no API keys / private info)
- [ ] No placeholder/TODO comments in new code

---

### Phase 9: From-Manifest Batch Mode + Rate Limit + Concurrency

**Estimated time:** 12 minutes

**Files:**
- Modify: `scripts/parsers.py` (add `validate_manifest`)
- Modify: `scripts/geminigen_client.py` (add `RateLimiter` class)
- Modify: `scripts/geminigen_image.py` + `geminigen_video.py` (`from-manifest` subcommand)
- Create: `references/manifest-schema.md`
- Test: `tests/test_manifest_batch.py`

**Steps:**
1. Write failing test for `validate_manifest(path)`: given valid `{"jobs": [{"type":"image","prompt":"...","aspect":"16:9"}, ...]}`, returns parsed list. Given invalid (missing required key), raises `ManifestValidationError` with line number hint. Expected error: function not implemented.
2. Run, confirm fail.
3. Implement `validate_manifest` with stdlib JSON + manual schema check (no `jsonschema` dep — keep light).
4. Write failing test for `RateLimiter(rpm=5, rph=100, rpd=1000)`: 6 rapid calls → 6th sleeps; verify sliding-window math. Expected error: class not defined.
5. Implement `RateLimiter` using sliding-window timestamps (collections.deque). Defaults to nano-banana-pro limits but overridable per model.
6. Write failing test for `from-manifest` batch: 3-job manifest, mocked submits, `--concurrent 2` flag. Assert ThreadPoolExecutor used + rate limiter consulted + all 3 succeed.
7. Implement `from-manifest` subcommand using `concurrent.futures.ThreadPoolExecutor(max_workers=concurrent_arg)` + RateLimiter.
8. Write `references/manifest-schema.md` documenting the JSON schema with examples.
9. Run all tests, confirm pass. Commit: `feat: add manifest batch mode with rate limit and concurrency`.

**Verification:**
- [ ] RateLimiter sleeps correctly under sustained load (timing-based test using `freezegun` or monotonic mock)
- [ ] `from-manifest` works for both image + video manifests
- [ ] Manifest validation rejects malformed input with clear errors
- [ ] All 6+ tests pass
- [ ] No placeholder/TODO comments in new code

---

### Phase 10: Reference Documentation

**Estimated time:** 12 minutes

**Files:**
- Create: `references/api-image-spec.md` (condensed nano-banana docs)
- Create: `references/api-video-spec.md` (condensed VEO/Seedance/Kling/Extend)
- Create: `references/style-presets.md` (16 styles + when-to-use examples)
- Create: `references/model-decision-tree.md` (decision flowchart: when to use which model)

**Steps:**
1. Synthesize `api-image-spec.md` from `D:\my-data\knowledge\image-generation-nano-banana.pdf` content already in plan. Include curl examples + param tables + status codes + rate limits.
2. Synthesize `api-video-spec.md` covering all 5 video endpoints with side-by-side param comparison table.
3. Synthesize `style-presets.md`: for each of 16 styles, give 1-sentence description + 1 use-case + 1 example prompt suffix. Reference `Photorealistic`, `Cinematic`, `Anime General`, `Portrait Cinematic`, `Game Concept`, `Stock Photo`, etc.
4. Write `model-decision-tree.md`:
   - Image: nano-banana-pro for premium/text rendering, nano-banana-2 for speed, imagen-4 for fine textures
   - Video: VEO for narrative/cinematic, Seedance for high quality + start/end frame control, Kling for motion-controlled / lipsync / edit, Extend* when continuing an existing clip
5. NO test phase for these (pure docs). Verification = manual read-through.
6. Commit: `docs: add reference specs for image + video APIs + style + model decision`.

**Verification:**
- [ ] All 4 docs exist with non-trivial content (>50 lines each)
- [ ] Each reference doc has a "When to use" or "Decision criteria" section
- [ ] No placeholder/TODO comments

---

### Phase 11: Live Smoke Tests + README Finalization

**Estimated time:** 15 minutes (includes real API calls)

**Files:**
- Create: `tests/e2e/test_live_image_smoke.py` (gated by `RUN_LIVE_TESTS=1` env)
- Create: `tests/e2e/test_live_video_smoke.py` (gated)
- Modify: `README.md` (full version)

**Steps:**
1. Write live smoke test for image: with `RUN_LIVE_TESTS=1` + real API key, submit minimal prompt (`"A red circle"`), poll to completion, assert file downloaded ≥1KB and valid JPEG header. Expected behavior: skipped without env var.
2. Run with `RUN_LIVE_TESTS=1` once locally to discover actual History API path. If probe fails, STOP and tell user "GeminiGen History API endpoint not at /history/{uuid} or /generations/{uuid} — please share docs."
3. Write live smoke test for video: submit 4s VEO clip with `"slow zoom on a candle flame"`, poll, download mp4, verify ≥1MB. Acceptable to take 2-3 minutes per run.
4. Write final `README.md`:
   - Install (clone + symlink to `~/.claude/plugins/` OR via plugin marketplace command — match indusia-skills README pattern)
   - Quickstart: 3 examples (one image, one video, one from-file)
   - Auth setup (env var)
   - Full subcommand reference (link to SKILL.md)
   - Rate limit + cost notes
   - Troubleshooting (History API path probe, common errors)
5. Run all unit tests + 1 live smoke, confirm pass. Commit: `test: add e2e smoke + finalize README`.

**Verification:**
- [ ] Live image smoke completes end-to-end with a real downloaded file
- [ ] Live video smoke completes (or documented if budget concern — at least one platform tested live)
- [ ] README has working copy-pasteable quickstart
- [ ] History API path discovered + cached
- [ ] No placeholder/TODO comments

---

### Phase 12: Git Init + Marketplace Prep

**Estimated time:** 6 minutes

**Files:**
- Create: `LICENSE` (MIT, match author from plugin.json)
- Create: `CHANGELOG.md` (initial entry for 0.1.0)
- Modify: `.claude-plugin/plugin.json` (set `keywords`, `homepage`, `repository`)

**Steps:**
1. `git init` in plugin root, add `.gitignore` already committed in Phase 0.
2. Create `LICENSE` MIT.
3. Create `CHANGELOG.md` documenting 0.1.0 = initial release with feature list.
4. Update `plugin.json` keywords: `["geminigen", "image-generation", "video-generation", "veo", "seedance", "kling", "nano-banana", "claude-code"]`.
5. Run final `pytest tests/` (skipping e2e), confirm all pass.
6. `git add . && git commit -m "feat: initial release v0.1.0"`.
7. Manual: ask user for GitHub repo URL, set remote, push.

**Verification:**
- [ ] `git log --oneline` shows clean history (1 commit per phase, ~12 commits)
- [ ] All unit tests pass (`pytest tests/ -m 'not live'`)
- [ ] Plugin can be installed via `/plugin install` flow (or manual symlink + Claude Code restart) — verify by typing `/geminigen-image --help` in Claude Code
- [ ] Both skills discoverable from skill list after install

---

### Parallel Execution Opportunities

These phase groups have no inter-dependencies and can be dispatched via `gaspol-parallel` (mode: plan-phases):

| Group | Phases | Reason |
|---|---|---|
| Image vs Video entry points | Phase 3 ‖ Phase 4 | Both depend only on Phase 2; touch separate files |
| Video subcommands | Phase 5 ‖ Phase 6 | Independent click subcommand additions; only Phase 5 touches both seedance + kling but those are separable too |
| Parsers vs References | Phase 8 ‖ Phase 10 | Parsers code is independent from doc synthesis |

Serial dependencies (cannot parallelize): 0 → 1 → 2 → {3,4} → {5,6} → 7 → {8,10} → 9 → 11 → 12.

Total serial estimate: ~141 min. With parallelization: ~95 min (saves ~30%).

---

### Red Flag Self-Check (passed before handing off)

- ✅ Data Integration Map present (24 rows, every "No" marked with create action)
- ✅ Every phase has TDD Step 1 with expected error class
- ✅ Every phase has Verification block
- ✅ References to source files included (Portfolio_v2 ImageGenerationService.php, knowledge PDFs, article-content-writer plugin.json)
- ✅ No vague data sources — every API call has explicit endpoint URL + param shape
- ✅ No placeholder language — even Phase 10 (docs) specifies real content sources
- ✅ Phases bite-sized (6-15 min each, none over 15 min)
- ✅ One open risk surfaced (History API path) with mitigation plan + STOP-and-ask gate

---

### Execution Handoff

**Option 1 — Execute in this session:** Run `/gaspol-dev:gaspol-execute` and start Phase 0.

**Option 2 — Parallel execution:** Run `/gaspol-dev:gaspol-parallel` with `mode: plan-phases` after Phase 2 completes; dispatch {3,4} and later {5,6}, {8,10} as parallel batches.

**Option 3 — Separate session:** The plan file at `docs/plans/2026-05-14-geminigen-api-client-skill.md` has everything needed. Resume any time with `/gaspol-dev:gaspol-execute`.

---

## Execution Log (2026-05-14)

### Phase 0-6: COMPLETED IN THIS SESSION ✅

Plugin scaffold + HTTP client core + image entry + video entry (VEO/Seedance/Kling/Extend). 57/57 unit tests pass. All-mocked via `responses` library — no live API calls yet.

**Location:** `D:\Projects\claude-plugin\geminigen-api-client\`

**Files created:**
- `.claude-plugin/plugin.json` — name, version, keywords
- `.gitignore`, `.env.example`, `requirements.txt`, `pyproject.toml`, `README.md` (seed)
- `scripts/__init__.py`, `scripts/geminigen_client.py` (~430 LOC: auth + multipart + submit + polling + history-path probe + download + output_path)
- `scripts/geminigen_image.py` (~200 LOC: image subcommand fully wired; check/list/from-file/from-manifest stubs raise NotImplemented per phase)
- `scripts/geminigen_video.py` (~450 LOC: veo + seedance + kling + extend veo + extend seedance all fully wired; check/list/from-file/from-manifest stubs)
- `skills/geminigen-image/SKILL.md`, `skills/geminigen-video/SKILL.md`
- `tests/test_skeleton.py` (5), `test_client_core.py` (15), `test_client_async.py` (13), `test_geminigen_image.py` (5), `test_geminigen_video_veo.py` (5), `test_geminigen_video_seedance.py` (5), `test_geminigen_video_kling.py` (5), `test_geminigen_video_extend.py` (4)

**Architectural pivot decided mid-execution:**
Mas Ali requested webhook-based async (not polling/cron). Existing direct-to-GeminiGen polling stays as ad-hoc/standalone mode. **Phase 7A-7E inserted below** as new sub-plan for Portfolio_v2 webhook bridge + skill dual-mode router.

### Phase 7 SCRAPPED — REPLACED with Phase 7A-7E

Original Phase 7 (check + list utilities using direct History API polling) is REPLACED. The status check needs to happen via Portfolio_v2 in webhook mode, so `check` and `list` logic now branches by `--backend` flag.

---

## Sub-Plan: Phase 7A-7E (Portfolio_v2 Webhook Bridge + Skill Dual-Mode)

> **For Claude in next session:** Read this sub-plan FIRST. Then read Portfolio_v2 `CLAUDE.md` SECTIONS RELEVANT TO image generation pipeline (sections on ImageGenerationService, BlogPipelineController, webhook routing — NOT the whole 277KB file; targeted grep first). Then proceed with /gaspol-execute starting Phase 7A.

### Goal

Add a "backend mode" to the geminigen-api-client skill that routes submissions through Portfolio_v2 instead of calling GeminiGen directly. Portfolio_v2 acts as the webhook receiver (already has signature verification + download infrastructure), eliminating the need for the CLI skill to poll GeminiGen. Skill queries Portfolio_v2 for status (or it's pushed via Portfolio_v2 to a notification channel — TBD in Phase 7C).

Direct-to-GeminiGen mode (Phase 0-6) STAYS as default for users without Portfolio_v2 access.

### Architecture

```
┌──────────┐  POST /api/automation/geminigen/submit   ┌─────────────┐
│  Skill   │ ───────────────────────────────────────▶ │ Portfolio_  │
│  (CLI)   │ ◀─────────────────────────────────────── │  v2 backend │
└──────────┘   { proxy_uuid, geminigen_uuid }         └──────┬──────┘
     ▲                                                       │
     │ GET /api/automation/geminigen/status/{uuid}           │ multipart POST
     │ (single GET — not polling loop)                       ▼
     │                                                ┌─────────────┐
     │                                                │ GeminiGen   │
     └─ 200 { status, result_url? }                  │   .ai API   │
                                                      └──────┬──────┘
                                                             │ POST webhook
                                                             ▼
                                          ┌──────────────────────────────┐
                                          │ Portfolio_v2 webhook         │
                                          │ /api/automation/geminigen/    │
                                          │   webhook                     │
                                          │ — verifies x-signature        │
                                          │ — downloads media to storage  │
                                          │ — updates geminigen_proxy_jobs│
                                          └──────────────────────────────┘
```

### Phase 7A: Portfolio_v2 — DB Migration for `geminigen_proxy_jobs`

**Estimated time:** 10 min

**Files:**
- Create: `D:\Projects\Portfolio_v2\backend\database\migrations\2026_05_15_000001_create_geminigen_proxy_jobs_table.php`
- Test: existing Laravel test infrastructure (`php artisan test --filter=GeminigenProxy`)

**Schema:**
```php
Schema::create('geminigen_proxy_jobs', function (Blueprint $table) {
    $table->id();
    $table->uuid('proxy_uuid')->unique();           // OUR uuid (returned to skill)
    $table->uuid('geminigen_uuid')->nullable()->unique();  // GeminiGen's uuid
    $table->string('source', 32)->default('skill'); // skill | future-internal-callers
    $table->string('platform', 32);                 // image | video-veo | video-seedance | video-kling | extend-veo | extend-seedance
    $table->string('model', 64);                    // nano-banana-pro | veo-3.1 | etc.
    $table->text('prompt');
    $table->json('params');                         // full submission params (aspect, duration, etc.)
    $table->json('refs')->nullable();               // ref URLs (uploaded ones store URL after S3/local store)
    $table->string('status', 16)->default('pending'); // pending | submitted | processing | completed | failed
    $table->integer('status_percentage')->default(0);
    $table->string('result_cdn_url', 1024)->nullable();
    $table->string('result_local_path', 1024)->nullable();
    $table->string('error_code', 64)->nullable();
    $table->text('error_message')->nullable();
    $table->integer('estimated_credit')->nullable();
    $table->timestamp('submitted_at')->nullable();
    $table->timestamp('completed_at')->nullable();
    $table->timestamps();

    $table->index('status');
    $table->index(['source', 'platform']);
});
```

**Steps:**
1. Read Portfolio_v2 `CLAUDE.md` sections for migration conventions (timestamps, naming, indexes).
2. Read `image_generation_jobs` migration for reference patterns.
3. Write failing test asserting table doesn't exist yet.
4. Create migration file. Run `php artisan migrate`.
5. Confirm table exists in DB.

**Verification:**
- [ ] `php artisan migrate:status` shows the new migration
- [ ] Table has all required columns + indexes
- [ ] No existing tests broken
- [ ] Model class `App\Models\GeminigenProxyJob` created with `$casts` for json fields

---

### Phase 7B: Portfolio_v2 — `GeminiGenProxyService`

**Estimated time:** 25 min

**Files:**
- Create: `D:\Projects\Portfolio_v2\backend\app\Services\GeminiGenProxyService.php`
- Create: `D:\Projects\Portfolio_v2\backend\tests\Unit\GeminiGenProxyServiceTest.php`

**Public surface:**
```php
class GeminiGenProxyService {
    public function submit(string $platform, string $model, string $prompt, array $params, array $refs): GeminigenProxyJob;
    public function status(string $proxyUuid): GeminigenProxyJob;
    public function handleWebhook(string $geminigenUuid, string $event, array $data): bool;
}
```

**Internal logic:**
- `submit()`: builds multipart, calls GeminiGen platform endpoint with our webhook URL injected, creates `geminigen_proxy_jobs` row with status=submitted
- `handleWebhook()`: verifies x-signature (per webhook PDF), finds job by geminigen_uuid, downloads media to `storage/app/public/geminigen-proxy/{proxy_uuid}.{ext}`, updates status=completed/failed
- `status()`: simple DB lookup by proxy_uuid

**Mirror Portfolio_v2 patterns from `ImageGenerationService.php`:**
- HTTP timeout 30s
- Retry FSM (3 attempts) — but for proxy jobs the retry happens at SKILL level, not auto here (keep service stateless w.r.t. retries)
- Log lines tagged `[GeminiGenProxy]`

**Steps (TDD):**
1. Write failing test for `submit('image', 'nano-banana-pro', 'sunset', [...], [])` creates a row.
2. Mock GeminiGen response.
3. Implement submit.
4. Write failing test for handleWebhook on IMAGE_GENERATION_COMPLETED → downloads file + sets status=completed.
5. Mock CDN download.
6. Implement webhook handler.
7. Write failing test for handleWebhook on IMAGE_GENERATION_FAILED → sets status=failed.
8. Implement failure path.

**Verification:**
- [ ] 6+ unit tests pass
- [ ] `php artisan test --filter=GeminiGenProxyService` green
- [ ] No regression on existing ImageGenerationService tests

---

### Phase 7C: Portfolio_v2 — Routes + Controller

**Estimated time:** 25 min

**Files:**
- Create: `D:\Projects\Portfolio_v2\backend\app\Http\Controllers\Api\Automation\GeminigenProxyController.php`
- Modify: `D:\Projects\Portfolio_v2\backend\routes\api.php` (add new route group)
- Create: `D:\Projects\Portfolio_v2\backend\tests\Feature\GeminigenProxyEndpointsTest.php`

**Routes (auth: bearer token via existing `X-Callback-Secret` or new Sanctum scope — confirm with Mas Ali during execution):**
```php
Route::middleware('auth.skill')->prefix('automation/geminigen')->group(function () {
    Route::post('/submit', [GeminigenProxyController::class, 'submit']);
    Route::get('/status/{proxyUuid}', [GeminigenProxyController::class, 'status']);
    Route::get('/result/{proxyUuid}', [GeminigenProxyController::class, 'result']);
});

// Webhook — separate, signature-verified middleware (no skill auth)
Route::post('/automation/geminigen/webhook', [GeminigenProxyController::class, 'webhook']);
```

**Steps (TDD):**
1. Write failing feature test for `POST /api/automation/geminigen/submit` returning 200 with proxy_uuid.
2. Implement submit endpoint calling GeminiGenProxyService.
3. Write failing test for `GET /api/automation/geminigen/status/{uuid}` returning current state.
4. Implement status endpoint.
5. Write failing test for webhook endpoint signature verification.
6. Implement webhook endpoint with HMAC verification per webhook PDF.
7. Configure webhook URL in GeminiGen Service Integration dashboard (manual, document in README).

**Verification:**
- [ ] 5+ feature tests pass
- [ ] Webhook signature verification rejects bad signatures
- [ ] Auth middleware rejects calls without proper token
- [ ] No regression on existing API tests

---

### Phase 7D: Skill — `portfolio_client.py`

**Estimated time:** 20 min

**Files:**
- Create: `D:\Projects\claude-plugin\geminigen-api-client\scripts\portfolio_client.py`
- Create: `D:\Projects\claude-plugin\geminigen-api-client\tests\test_portfolio_client.py`

**Public surface:**
```python
def submit_via_portfolio(platform, model, prompt, params, refs, portfolio_url, portfolio_token) -> dict
def get_status_via_portfolio(proxy_uuid, portfolio_url, portfolio_token) -> dict
def download_via_portfolio(proxy_uuid, target_path, portfolio_url, portfolio_token) -> str
```

**Steps (TDD):**
1. Write failing tests for each function with `responses` mocking Portfolio_v2 endpoints.
2. Implement using same `requests` patterns as geminigen_client.py.
3. Status check is single GET (no polling loop in this module).
4. Re-add `--backend portfolio` flag in image + video entry points (Phase 7E).

**Verification:**
- [ ] 5+ unit tests pass
- [ ] No new dependencies added

---

### Phase 7E: Skill — Dual-Mode Router in image + video entry points

**Estimated time:** 25 min

**Files:**
- Modify: `D:\Projects\claude-plugin\geminigen-api-client\scripts\geminigen_image.py`
- Modify: `D:\Projects\claude-plugin\geminigen-api-client\scripts\geminigen_video.py`
- Create: `D:\Projects\claude-plugin\geminigen-api-client\tests\test_dual_mode.py`

**New flags (both skills):**
```
--backend [direct|portfolio]      # default: direct (existing behavior)
--portfolio-url <url>             # base URL of Portfolio_v2 instance
--portfolio-token <token>         # bearer token
```

**Auto-detect:** If `PORTFOLIO_API_URL` env set → default --backend=portfolio.

**Flow when --backend=portfolio:**
1. submission goes to `portfolio_client.submit_via_portfolio` instead of `geminigen_client.submit`
2. Returns proxy_uuid (Portfolio_v2's UUID, NOT GeminiGen's)
3. Sync mode: skill polls Portfolio_v2 (single GET every 5s — Portfolio_v2 already has final result from webhook, so this is "wait for webhook to land" not "ask GeminiGen for status")
4. No-wait mode: print proxy_uuid, exit
5. `check <proxy_uuid>` routes to portfolio_client too when --backend=portfolio

**Steps (TDD):**
1. Write failing tests asserting `--backend portfolio` routes to portfolio_client mocks.
2. Implement router in both entry points.
3. Tests for `check <uuid> --backend portfolio` route correctly.
4. Tests for env auto-detect of backend.
5. Update SKILL.md with backend mode docs.

**Verification:**
- [ ] 8+ new tests pass; existing 57 tests still green
- [ ] `--backend portfolio` mode works end-to-end against a mocked Portfolio_v2
- [ ] Direct mode (default) unchanged
- [ ] README updated with backend mode setup steps

---

### Then Continue: Phase 8-12 (Original Plan) — Backend-Aware

After Phase 7A-7E is complete, the remaining phases (parsers, manifest, references, smoke tests, git init) execute as in the original plan, but with awareness that there are TWO backends now:

- `from-file` and `from-manifest` accept `--backend` flag too
- Reference docs add a "Backend Mode" section to README + SKILL.md
- Live smoke test (Phase 11) ideally covers BOTH backends (or just `--backend direct` is fine, since portfolio mode requires running Portfolio_v2 locally — too heavyweight for smoke)
- Git init for both repos (skill + Portfolio_v2) at Phase 12

### Updated Total Estimate

- Phase 0-6: DONE (was ~95 min, took 1 session)
- Phase 7A-7E: ~105 min (5 phases × ~20 min each)
- Phase 8-12: ~75 min (from original plan)
- **Remaining work: ~180 min**

### Recommended next-session prompt

```
/gaspol-dev:gaspol-execute

Continue plan at docs/plans/2026-05-14-geminigen-api-client-skill.md. 
Phase 0-6 done (skill direct-to-GeminiGen mode complete, 57/57 tests). 
Now execute Phase 7A-7E (Portfolio_v2 webhook bridge + skill dual-mode), 
then Phase 8-12 (parsers, manifest, refs, smoke, git init).

CRITICAL pre-work for Phase 7A: read Portfolio_v2 CLAUDE.md sections 
relevant to image-generation pipeline (grep for ImageGenerationService, 
BlogPipelineController, image-webhook). Do not blindly write Laravel code 
without verifying conventions first.
```

---

## Brainstorm Session Notes (historical)

---

## 1. Problem Statement

Existing prompt-generator skills (`/video-gen`, `/article-images`, `/carousel-gen`) hanya OUTPUT prompt text. Belum ada tool yang langsung memanggil GeminiGen.ai API dari Claude Code untuk render image/video aktual. User harus manual paste prompt ke GeminiGen UI.

**Goal:** Dedicated skill yang menerima prompt (image atau video VEO 3.1 / Seedance 2.0) → call GeminiGen API → return media file (URL atau download).

---

## 2. Decisions Locked (Phase 1-2)

| Decision | Choice | Rationale |
|---|---|---|
| **Repo placement** | Plugin baru `geminigen-api-client` (standalone) | Konsisten dengan pemisahan strategic-domain (indusia-skills) vs production tools (ai-content-suite, ai-video-promo-engine). Cross-project reusable. |
| **Scope/phasing** | PAUSE — user akan share dokumentasi API video GeminiGen dulu | Hindari placeholder/guessing endpoint VEO/Seedance. Setelah docs masuk, lanjut full scope image + video. |

---

## 3. Reference Implementation Map (Portfolio_v2)

### Image API (CONFIRMED, proven in production)

**Source:** `D:\Projects\Portfolio_v2\backend\app\Services\ImageGenerationService.php` (1027 lines)

| Aspect | Spec |
|---|---|
| Base URL | `https://api.geminigen.ai/uapi/v1` |
| Endpoint | `POST /generate_image` |
| Auth | Header `x-api-key: {GEMINIGEN_API_KEY}` |
| Body | `multipart/form-data` |
| Required params | `prompt` (string), `model` (default `nano-banana-pro`), `aspect_ratio` (default `16:9`), `style` (default `Photorealistic`) |
| Reference images | `file_urls` (one multipart entry per URL — NOT JSON array) |
| Async callback | `webhook` / `webhook_url` / `callback_url` (all same value) |
| Response | `{ uuid, status, generate_result? }` — status=2 means sync-complete; otherwise webhook fires |
| Webhook events | `IMAGE_GENERATION_COMPLETED` (`data.media_url`) / `IMAGE_GENERATION_FAILED` (`data.error_message`) |
| Timeout | HTTP 30s |
| Safety errors | `PUBLIC_ERROR_PROMINENT_PEOPLE_UPLOAD`, `PUBLIC_ERROR_MINOR`, etc. → triggers Sonnet rewrite |
| Retry budget | 3 attempts per segment (`MAX_SEGMENT_ATTEMPTS`) |
| Default model | `nano-banana-pro` (env `DEFAULT_IMAGE_MODEL`) |

### Video & Image API — Full Spec (RESOLVED 2026-05-14 from D:\my-data\knowledge/*.pdf)

**Common:**
- Base URL: `https://api.geminigen.ai/uapi/v1`
- Auth: `x-api-key` header
- Body: `multipart/form-data`
- Status codes: 1=processing, 2=completed, 3=failed
- Async: webhook (`IMAGE_GENERATION_COMPLETED/FAILED`, `VIDEO_GENERATION_COMPLETED/FAILED`) OR History API polling
- Webhook signature: HMAC-SHA256 via `x-signature` header (RSA PKCS1v15 verify with public key)
- Webhook retry: 3x on non-200 response, 1hr delay between

**Endpoints (6 total):**

| Endpoint | Models | Key Params |
|---|---|---|
| `POST /generate_image` | nano-banana-pro, nano-banana-2, imagen-4 | prompt, model, aspect_ratio (1:1/16:9/9:16/4:3/3:4), style (16 options inc. Photorealistic/Cinematic/Anime/etc), output_format (jpeg/png), resolution (1K/2K/4K), files[]/file_urls[]/ref_history |
| `POST /video-gen/veo` | veo-3.1, veo-3.1-fast, veo-3.1-lite (with audio), veo-2 | prompt, model, resolution (720p/1080p), duration (4/6/8), aspect_ratio (16:9/9:16), mode_image (frame/ingredient), ref_images[] (max 2 frame / 3 ingredient) |
| `POST /video-gen/seedance` | seedance-2, seedance-2-omni | prompt, model, mode (fast/pro/fast-2/pro-2/fast-vip/pro-vip — omni only), duration (4-15s), aspect_ratio (16:9/9:16/1:1/3:4/4:3/21:9), ref_images[] (first/last for seedance-2; up to 4 ingredient for omni, max 15MB each), ref_videos (omni: mp4/webm 60MB 15s), ref_audios (omni: mp3/wav 15s) |
| `POST /video-gen/kling` | 11 variants: kling-video-3-0/2-6/2-5/2-1-5s/2-1-10s/o1/motion-3/motion/3-0-edit/o1-edit/lipsync | prompt (min 10 chars), model, mode (standard/professional/professional_audio for 2-6/relax for 2-5), aspect_ratio (16:9/9:16/1:1), duration (3-15s default 5), ref_videos (mp4/mov/webm 100MB 120s), ref_images (jpg/png 10MB) |
| `POST /video-extend/veo` | — | prompt, ref_history (UUID of original Veo video). Model/aspect/resolution inherited from source. |
| `POST /video-extend/seedance` | — | prompt, ref_history (UUID of original Seedance video). All settings inherited. |

**Rate limits (image only):** nano-banana-pro: 5 rq/min, 100 rq/hour, 1000 rq/day. Other models: no documented limit.

**Reference file modes:**
- Local file: `--form 'ref_images=@/path/to/file.jpg'` (multipart upload)
- URL: `--form 'ref_images=https://cdn.example.com/file.jpg'` (string)

**Response shape (all endpoints, consistent):**
```json
{
  "id": 2588,
  "uuid": "c558a44c-c91c-11f0-98b4-0242ac120004",
  "user_id": 3,
  "model_name": "veo-3.1",
  "input_text": "...",
  "type": "video",       // or "image_generation"
  "status": 1,            // 1=processing, 2=completed, 3=failed
  "status_desc": "",
  "status_percentage": 1,
  "error_code": "",
  "error_message": "",
  "estimated_credit": 20,
  "media_type": "video",
  "created_at": "...",
  "delay_seconds": 0,
  // when status=2 for image:
  "generate_result": "https://cdn.geminigen.ai/images/img_xxx.jpg",
  "thumbnail_small": "..."
  // when status=2 for video — TBD: doc shows generate_result for image only; video likely returns URL in webhook media_url or separate field. Need test or check History API doc.
}
```

**History API:** `https://docs.geminigen.ai/resources/history-apis#get-specific-generation-history` — used to poll status by UUID. **CRITICAL** because skill is CLI (no webhook receiver). Exact endpoint path/params not in PDFs supplied — to confirm during implementation (probably `GET /history/{uuid}` or `GET /generations/{uuid}`).

**Credit cost example:** Seedance 10s × pro-2 = 200 credits. Video uses more credits than image. Estimated cost surfaced in response field `estimated_credit`.

---

## 4. Open Architectural Questions (deferred to Phase 2 cont.)

These need answers AFTER video API docs are in:

1. **Sync vs Async (CLI runtime constraint)**
   - Portfolio_v2 uses webhook → Laravel queue → DB persist. **Skill = CLI, can't host webhook.**
   - Options: (a) polling endpoint, (b) sync wait on HTTP, (c) fire-and-forget + separate `/geminigen-check <uuid>` skill, (d) backend-bridged (skill POSTs to Portfolio_v2 endpoint which proxies + queues — but couples skill to Portfolio_v2)
2. **Input source format**
   - Single prompt arg? Parse `video-prompts.md` / `image-prompts.md`? Read JSON manifest from sibling skill output?
3. **Output destination**
   - Print URL to stdout? Download to local folder? Both?
4. **Reference image handling**
   - Local files (need upload to public URL first)? Already-public URLs only? Both?
5. **Config storage**
   - API key from env (`GEMINIGEN_API_KEY`)? Skill config file? Both with fallback?
6. **Cost/safety guardrails**
   - Concurrent job cap? Per-day spend limit? Dry-run mode?
7. **Multi-prompt batching**
   - One-at-a-time or parallel dispatch with consolidated wait?

---

## 5. Phase 0.5 KB Cross-Project Query

- KB configured ✅, but `git pull` failed (SSH publickey error — offline-friendly fallback).
- Grepped `INDEX.md` for image/video/gemini/veo/seedance/api-integration — **no precedent found**.
- Closest hit: `adr-2026-04-28-carousel-engine-publisher-separation.md` (carousel engine architecture, not API client).

---

## 6. Phase 1.5 Novelty Assessment

- **Image side:** PRECEDENT — Portfolio_v2 reference implementation is comprehensive.
- **Video side:** NEW — no precedent in Portfolio_v2, no KB entry, no existing skill.
- **CLI-as-API-client runtime:** SEMI-NEW — different from server-side Laravel pattern; need to redesign async story.

---

## 7. Next Steps (when resumed)

1. User shares GeminiGen video API documentation (curl example, param list, response shape).
2. Resume Phase 2: Approaches design — image MVP architecture + video architecture side-by-side.
3. Phase 2.5: Design gate (likely SKIP — pure backend/API skill, no UI).
4. Phase 4: Implementation feasibility — validate sync/polling/webhook story works without backend dependency.
5. Phase 5: Data Integration Map + present full design.
6. Phase 6: Hand off to `gaspol-plan` for implementation plan.
