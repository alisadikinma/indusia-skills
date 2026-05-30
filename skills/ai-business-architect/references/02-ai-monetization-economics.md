# 02 — AI Monetization Economics

How to choose the value metric and defend margin against real AI cost structure. Grounded in `research-output/nlm-research-report.md` (Bessemer, BCG, McKinsey, Paid.ai, Mostly Metrics, Salesmotion, Jerry Chen). Citations flagged inline.

## 1. Value metric first — always

Pick **what you meter** before **how you charge**. The value metric must scale with the customer's *realized* benefit, not with what is convenient to bill.

The agentic shift (per Paid.ai): value has moved **from human licenses → to autonomous task completion + compute**. Per-seat pricing is dying because agents don't log in and often *reduce* headcount — so seats fail to capture the value of synthetic labor and higher system output. "The Death of Per-Seat Pricing" is a real P&L event, not a slogan.

**New value-unit archetypes (per Paid.ai):**
| Archetype | Meter | Example unit |
|---|---|---|
| Usage-based | technical consumption | API calls, tokens, compute-minutes |
| Outcome-based | business result | resolved ticket, verified lead, defect caught |
| Agent-based | synthetic labor supplied | $/autonomous-agent/month, $/FTE-equivalent |
| Hybrid | platform fee + usage | base + metered + **floor & cap** (per Paid.ai + Salesmotion) |

**Value-metric selection test:** if usage doubles but customer value doesn't, your metric is wrong (you'll over-charge and churn). If value doubles but your revenue is flat, your metric is wrong (you leak captured value). The right metric moves *with* value.

## 2. AI COGS — why gross margins are lower than SaaS

AI delivery is **compute-intensive**; inference + tokens are a material part of COGS (per Paid.ai). This is the single biggest difference from classic SaaS (which is near-zero marginal cost).

**What goes into AI COGS (per Mostly Metrics):**
- **Inference cost** — recurring cost to run the model on every request.
- **API tolls** — fees to third-party model providers (OpenAI, Anthropic, etc.).
- **Infra hosting** — cloud compute + storage for self-hosted models / proprietary data.
- (+ data labeling, retraining, eval, human-in-the-loop QA, edge-device amortization for on-prem).

**The margin trap:** flat, unlimited pricing layered over a *variable* per-call cost is a countdown to negative contribution margin. **Never** offer unlimited usage above a variable COGS without a cap. Tie the price-floor to the cost-floor.

**Edge/on-prem note:** moving inference to an edge appliance (e.g. a Jetson-class device) converts recurring cloud inference COGS into amortized hardware capex — changing the margin curve and enabling EaaS or deploy-fee+recurring models (pattern #16/#20).

## 3. Unit-economics benchmarks

- Target **LTV:CAC ≥ 3:1**, **CAC payback < 12 months** (per Salesmotion) — the efficiency bar for sustainable AI growth.
- **Standard LTV:CAC often *fails* for AI** because high compute COGS "eats license margin" if pricing ignores it (per Paid.ai + Salesmotion / "Why Standard LTV:CAC Fails"). Always compute **gross-margin-adjusted** LTV (use contribution margin, not revenue).
- Contribution margin per unit = price − (inference + API + infra + variable delivery). If this is thin or negative on the entry tier, the model scales into losses.

## 4. Moats & defensibility — the business model must compound

Commoditized models mean defensibility comes from **Systems of Intelligence** (Jerry Chen): AI that *operates the product on the user's behalf*, embedding into daily operations → deep **workflow lock-in**, far stickier than a thin interface (per Jimo).

- **Data flywheel:** product activation captures behavioral signals → fed back → model adapts in real time → performance & retention improve (per Jimo). Each customer makes the product better for the next.
- **Weak moat:** "AI wrappers" / generalist tools lacking proprietary data or unique workflow integration (per Jimo / Expando AI). *"AI generalists are dying"* — the defensible position is **domain-specific**.
- **Strong moat:** domain-specific deployments that create workflow lock-in via complex, multi-stakeholder process management.

**Business-model implication:** prefer models that *feed the flywheel* (usage/outcome that generates proprietary data) and *deepen workflow integration* (platform-fee that funds embedding), over one-shot licenses that leave no compounding asset.

## 5. Pricing-mechanism design checklist

1. Name the value metric (§1). Confirm it tracks realized value.
2. Choose mechanism from `01-business-model-pattern-library.md`; for agentic work, **reject per-seat** by default.
3. Wrap usage with **floor (protect margin) + cap (protect customer)** — committed-use tiers smooth variable COGS (per Paid.ai).
4. Set price-floor ≥ cost-floor per unit. Verify contribution margin > 0 on every tier including entry.
5. If attribution is clean and value is large → consider **gainshare** (% of measured savings) to capture a share of value created (per Acquis).
6. Confirm the model **feeds a moat** (data and/or workflow lock-in). If not, redesign for defensibility.
7. Stress-test in `06-validation-unit-economics.md`.

> Cross-ref: ideation in `03`, deal structures in `05`, GTM in `04`.
