# 06 — Validation & Unit Economics (Red-Team a Model)

Before recommending any model, pressure-test it. Grounded in `research-output/nlm-research-report.md` (Paid.ai, Salesmotion, Mostly Metrics, Jimo). A model that passes ideation but fails here is a trap.

## 1. Unit-economics check

Compute per unit of the chosen value metric:
- **Price** per unit.
- **Variable COGS** per unit = inference + API tolls + infra hosting + variable delivery/QA (per Mostly Metrics). For on-prem, include amortized edge-hardware + commissioning.
- **Contribution margin** = price − variable COGS. **Must be > 0 on every tier, including entry/pilot.**
- **Gross margin direction** — does it improve or decay as usage scales? (Flat-fee over variable cost → decays → trap.)

Benchmarks (per Salesmotion): **LTV:CAC ≥ 3:1**, **CAC payback < 12 months**. Use **gross-margin-adjusted LTV** (contribution margin × lifetime), not revenue — standard LTV:CAC overstates AI health because compute COGS eats license margin (per Paid.ai).

## 2. The 7-point model scorecard

Score each 0–2 (0 = fail, 1 = weak, 2 = strong). < 9/14 → redesign.

| # | Test | Question |
|---|---|---|
| 1 | **Value alignment** | Does the meter scale with realized customer value? |
| 2 | **Margin safety** | Positive contribution margin after inference COGS, on all tiers? |
| 3 | **Margin trajectory** | Does margin hold/improve at scale (floor+cap in place)? |
| 4 | **Moat** | Does each customer/usage compound a moat (data flywheel / workflow lock-in / exclusive distribution)? |
| 5 | **Buyability** | Can the buyer say yes without a 6-month procurement fight? Is there a low-friction land? |
| 6 | **Budget fit** | Does it match the buyer's opex/capex line + risk appetite? |
| 7 | **Power / IP** | After the deal, who owns the customer, the data, and the IP? (esp. partnerships, `05`) |

## 3. AI-specific failure modes to probe

- **Per-seat-on-agentic** — value decoupled from seats; revenue can't grow with output (per Paid.ai). → re-meter to work/outcome/agent.
- **Margin time-bomb** — unlimited usage over variable COGS. → add floor+cap, committed-use.
- **Value-capture gap** — creating large savings, capturing a flat sliver. → gainshare where attribution is clean.
- **Wrapper fragility** — no proprietary data/workflow lock-in (per Jimo). → deepen integration, capture data rights.
- **ALG stickiness risk** — fast value, weak attachment (per Jimo). → embed into workflow + flywheel.
- **Attribution failure** — outcome/gainshare with no agreed baseline or measurement. → fix measurement or fall back to usage.
- **Adoption death** — legacy-system friction + staff education gap (per Salesmotion). → fund change management.

## 4. Kill criteria (define up-front, monitor early)

State, for the recommended model, the metric that signals failure early:
- Contribution margin < 0 on the entry tier at expected usage → **kill / re-price**.
- CAC payback > 18 months after 2 quarters → **motion is wrong** (switch PLG↔partner-led).
- Pilot→paid conversion < target (e.g. < 20% partner-led / < 9% PLG) → **value or packaging broken**.
- No data/workflow lock-in after N customers → **no moat, becoming a discount** → restructure.
- In a partnership: specialist's margin compressing + losing data/IP rights → **disintermediation risk** → renegotiate or exit (`05`).

## 5. Output of validation

For the recommended model, deliver:
1. Unit-economics line (price, COGS, contribution margin, payback).
2. Scorecard total + the weakest dimension and its fix.
3. Top 3 failure modes for *this* model + mitigations.
4. Kill criteria + the early-warning metric for each.

> A recommendation without kill criteria is incomplete. Close every business-model answer with them.
