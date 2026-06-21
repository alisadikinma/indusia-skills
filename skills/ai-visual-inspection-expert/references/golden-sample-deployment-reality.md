# Golden-Sample Deployment Reality (this product) — template-match-first, few-board, eval-without-defects

> Provenance: NotebookLM deep-research pass (2026-06-21) over **48 cited sources** —
> PCB template-matching studies, PatchCore/PaDiM/EfficientAD/SPADE + FR-PatchCore /
> MAIA-D anomaly papers, AIAG MSA / IATF-16949 Attribute Agreement Analysis, AOI
> vendor references (Koh Young, Omron), IPC Class 3 medical, AOI Vision Governance
> (EMS) + NPI/pilot-production. Numbers below are **as stated by those sources** —
> where a figure is vendor/paper-specific, treat it as a reference point, not a law.
> This file is the authority where it conflicts with the generic refs: it is written
> for the `indusia-visual-editor` flow (single-camera 2D optical, golden sample,
> manual labels, scarce defects), not for a YOLO-heavy defect-labeled AOI line.

This reference exists because the other four files describe a *generic, supervised,
defect-labeled* AOI world. **This product is the opposite corner** and the senior
advice has to match it:

- The deployable line is **template-match-first** (`template_match` carries the bulk
  — presence/absence, polarity, gross alignment — and **deploys with no training**).
- Training data is **manually labeled and scarce** (auto-bbox was dropped); defects
  are **rare or absent** (golden boards only).
- There is usually **no labeled defect test set**, so supervised mAP/F1/recall are
  **uncomputable** — eval is honestly *unscored* and Gate 2 must not pretend otherwise.

---

## 1. Template-match-first — when `template_match` is enough, when to escalate

**Default to `template_match` for the fixed-fixture golden-sample case.** Deterministic
pixel/structural comparison (NCC, SSIM, histogram threshold) against the cropped golden
ROI needs **no training data** — it is the genuine cold-start path (crop ROI from the
golden, or import CAD/Gerber, build a reference library).

**Accurate enough — under control.** For stable, high-contrast features (presence/absence,
gross alignment) with **rigid fixturing + stabilized lighting + uniform packaging**,
template matching reaches **F1 ≈ 0.982, recall ≈ 99.87%, overkill ≈ 0.149 false calls/image**.
This is why ~9 in 10 components on a well-fixtured line never need a trained model.

**Per-component threshold tuning (operator/engineer sets this).** Thresholds are set
*per component type* and tuned iteratively on a small initial batch to balance false-call
vs escape:
- basic chip cap → NCC **≥ 0.85**
- critical IC marking → SSIM **≥ 0.92**
- adjust across the first production boards; tighter = more overkill, looser = more escapes.

**Failure modes → escalate to `anomalib` (good-only) or `yolo` (if you have labels).**
Classical matching breaks down and false-call rates climb to **15–30%** when:
- **Specular / reflective parts** (J-leads, BGA, solder): small reflow wetting-angle shifts
  change the reflection → misread as bridge/insufficient. → use RGB/MDMC lighting (Part A)
  + anomaly/learned detector, not a pixel template.
- **Registration / rotation error**: warp (up to ~2.5 mm), micro-vibration, 0.1 mm shift or
  1–2° rotation violates rigid pixel correlation → false calls. → fix fiducial registration
  first (Part A); a learned detector is rotation-tolerant where geometry can't be locked.
- **Legitimate part-to-part variation**: alternate-vendor part, laser-etched vs ink text,
  body color == laminate color, dense markings on black inductors → benign variation flagged.
  → anomaly-on-good (models the *distribution* of acceptable looks) or YOLO semantic features.

A learned/anomaly model evaluates **semantic** features, not exact pixels, so it is natively
tolerant of lighting drift, micro-rotation, and cosmetic font variation — pushing accuracy
**> 95%** and false-call **down to 1–5%** when those failure modes are present. **But it
costs labels or many good boards** (§2) — so escalate *per component*, only where template
matching demonstrably fails, never wholesale.

**Decision rule for pipeline planning:** for each `bom_item`, ask in order —
1. Fixed fixture + stable look + just presence/polarity/alignment? → **`template_match`** (no training).
2. Benign look-variation or specular, but you have ≥10–100 good boards? → **`anomalib`** (good-only, §2).
3. A repeating, *labeled* defect with real annotated examples? → **`yolo`** (supervised).
4. Text/marking vs BOM, or a code? → **`ocr` / `barcode`**.
5. Height/volume (tombstone, lifted lead) or hidden (BGA)? → **out of 2D scope — flag it.**

> Deploy-compat note: `template_match` carries **no model-runtime version risk** — it ships
> as a cropped template + threshold. An `anomalib` model does: the editor trains anomalib 2.x
> (`model.pt`); a prod runtime pinned to anomalib 0.7.4 loads `model.bin` and will **not**
> load it. Template-match is the safe default for promotion; anomaly needs a runtime-version
> gate before it can deploy.

---

## 2. Few-good-sample anomaly — how many DISTINCT boards, and the selection-bias trap

**Minimum nominal samples by model (good-only training):**

| Model | Nominal samples needed | Notes |
|---|---|---|
| **PatchCore** | **10–100** (few-shot **1–5** can match prior SOTA) | most sample-efficient; the project default for the good-only tail |
| **PaDiM** | 10–50 | relies on per-coordinate mean/covariance (sensitive to misalignment) |
| **EfficientAD** | 50–100 | very fast (<2 ms), good edge fit |
| **SPADE** | 100+ | heavy (full memory bank + kNN) |

**The count must be DISTINCT PHYSICAL BOARDS, not frames of one board.**
- **Intra-board variance** = variation *within one* physical board (frame-to-frame offset,
  micro-reflection, multiple viewpoints of the same component). The LS-import "N frames"
  capture exactly this.
- **Inter-board variance** = variation *across many distinct* boards over time (lot-to-lot
  substrate color, warpage, solder-volume fluctuation, vendor/font revisions). This is what
  actually defeats selection bias.

**Golden-sample selection bias (the project's #1 anomaly hazard).** Train on ~100 images of
**one** golden board → "normal" overfits that board's exact alignment and fillets, producing
**both** failure modes:
- **Overkill**: a benign 0.05 mm shift or an alternate-vendor font on the *next* board
  violates the tight distribution → false call.
- **Escape**: if that one golden board had an unnoticed subtle defect, the model bakes it
  into "good" → the identical defect escapes on every future board.

⇒ **Train anomaly on a minimum of ~10–100 *distinct* physical good boards** to capture
acceptable inter-board variance. **N frames of one board ≠ N good boards.** In this product:
the LS-first import gives viewpoint coverage of ONE board — good for template/registration,
**insufficient** to qualify an anomaly model. Surface this as a Gate-1 training blocker:
"butuh beberapa board fisik good yang berbeda, bukan beberapa frame dari satu board."

**Viewpoint-pooling hazard.** Do **not** pool crops from different camera angles (0° top-down +
45° side) of the same component into one "good" set:
- PaDiM/SPADE: per-coordinate stats get corrupted (two perspectives averaged at one coordinate).
- PatchCore: feature-space overlap lets a top-down query match a side-view memory patch →
  anomaly score artificially lowered → rotated/misaligned/missing parts escape.
- Fix: **train one anomaly pipeline per camera angle**, or use **FR-PatchCore** (feature-level
  registration) to keep spatial mapping. This is why the project's per-frame crop must keep
  frames separate, not merge viewpoints into one bucket.

---

## 3. Qualifying a model WITHOUT a labeled defect test set (this is the eval honesty)

**The honest mathematical limit (state it plainly).** With **only** defect-free boards there
are no positive samples to fill a confusion matrix → **supervised mAP, F1, and recall cannot
be computed.** This is exactly why the project's eval is `scored: false` and Gate 2 stays
locked — that is *correct*, not a bug. Never quote "mAP ≥ 0.80 / F1 ≥ 0.80" as the promote
line for the golden-only flow; those thresholds apply **only** once a real labeled defect test
set exists (v1.5). Until then, qualify with the four measures below.

**(a) Overkill / false-call rate — measurable now.** Run a **held-out set of 100–500 verified
good boards** (must span acceptable inter-board variance). Any flag on them is by definition a
false call. Quantify at component level:
`overkill_PPM = (false_flags / components_inspected) × 1,000,000`. **Production gate: < 500 PPM.**

**(b) Synthetic defect injection — to get a *simulated* recall/mAP.** Inject photorealistic
defects (solder crack, tombstone, missing) into clean real board images — latent-diffusion
methods (ScaleEncoder + FiLM-style spatial modulation) re-render the anomaly to follow the
component's 3D curvature, reflectivity, and shadow, yielding pixel-level masks. This gives a
**simulated** sensitivity before any physical defects exist. Label it *simulated* — it is a
proxy, not a real escape measurement.

**(c) Attribute Agreement Analysis (AAA) — qualify the go/no-go decision (IATF-16949 / AIAG MSA).**
For a qualitative defect/OK call:
- **Sample design:** 20–30 physical parts × 2–3 independent appraisers (or AI setups) × 2–3
  randomized trials, against a verified ground-truth standard.
- **Metric:** Cohen's Kappa (2 raters) / Fleiss's Kappa (3+), agreement corrected for chance.
- **AIAG limits:** **κ ≥ 0.90 = excellent / qualified**; 0.75 ≤ κ < 0.90 = marginal; **κ < 0.75
  = unacceptable.**
- If the AOI outputs a *quantitative* measurement instead, use ANOVA Crossed Gage R&R, target
  **% Study Variation < 10%.**

**(d) Bound the escape rate statistically — the Rule of Three.** You can't observe escapes with
zero physical defects, so bound them: **n** independent inspections with **zero** escapes →
95% upper bound on true escape rate = **3/n**.
- Prove escape < 0.1% (1,000 PPM) @95% → **3,000** consecutive clean inspections.
- High-reliability < 10 PPM → **300,000** inspections.
Use this to set the shadow/pilot run length, and say honestly what level of assurance the
current run actually buys.

---

## 4. Promote governance — shadow / pilot / override (the Gate-2-without-scoring playbook)

This is the concrete shape of the project's Gate-2 supervisor-override design. When training
data is thin and metrics are unscored, promotion is governed by **process**, not by a fabricated
mAP.

1. **First-Article Inspection (FAI):** before volume, the model inspects the first physical board
   and must hit **100% agreement** with the verified manual FAI report.
2. **Shadow mode 7–14 days:** model runs at full line speed but **does not reject** — its flags are
   logged and compared against human decisions to establish baseline overkill + escape.
3. **Pilot 50–500 units — quantified ramp gate before enabling reject:**
   - Defect **escape = 0%** (verified downstream by ICT / functional test),
   - Overkill **< 500 PPM** at component level,
   - Accuracy **> 99.9%** before "flipping the switch."
4. **Operator-in-the-loop:** every flagged board goes to a verification station; operator confirms
   or overrides. A confirmed-false override should feed back into the local edge dataset to refine
   the model (this is the project's feedback → `defect_examples` → retrain loop).
5. **Override + audit trail:** every override writes an **immutable, timestamped** record linking
   board serial, component coordinates, model confidence, operator ID, and the image. A supervisor
   bypass *without* per-component review must trigger a QMS sign-off — this is the audited
   supervisor-override that lets Gate 2 promote responsibly despite `scored: false`.
6. **Process lock + re-validation cadence:** once qualified, weights/params are **locked**; changes
   go through an Engineering Change Order. **Re-validate** (new FAI sign-off + mini-AAA of **10
   boards × 3 trials**) on: board revision (Gerber/routing/substrate), component/vendor change
   (package/font/reflectivity), or upstream equipment recalibration (P&P / reflow).

**Gate-2 advice pattern for this product:** "Model trained, but `scored: false` — supervised mAP/F1
can't be computed without a labeled defect test set. To promote responsibly: held-out-good overkill
< 500 PPM, Attribute Agreement κ ≥ 0.90, escape-rate bound via Rule of Three for the run length you
ran, then shadow 7–14 d → pilot 50–500 u (escape 0%, overkill < 500 PPM) → supervisor override with
audit. Multi-distinct-good-board precondition (§2) must be met before any anomaly component promotes."
