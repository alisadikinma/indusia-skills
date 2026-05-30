# 01 — Business Model Pattern Library (Beyond SaaS)

The menu. 45+ value-capture patterns, each tagged with **AI fit** and a **when-it-wins** note. Use to borrow proven mechanics after ideation (03). SaaS-subscription is pattern #1 of 45 — treat it as one option, never the default.

**AI-fit legend:** 🟢 strong fit · 🟡 situational · 🔴 usually wrong for AI.

---

## A. Recurring-access models
1. **Seat-based subscription** 🔴 (for agentic) / 🟡 (for tools) — $/user/mo. Wins for human-in-the-loop tools with stable usage. **Fails** when value decouples from logged-in humans (agents do the work, not seats).
2. **Tiered subscription (good/better/best)** 🟡 — feature-gated tiers. Wins for self-serve PLG; weak when value is highly variable per customer.
3. **Platform/base fee + usage** 🟢 — fixed access fee + metered consumption. Wins when you need predictable floor revenue AND fair scaling. The default hybrid for AI.
4. **Capacity reservation / committed-use** 🟢 — customer pre-commits a usage tier at a discount (cloud-style). Wins for smoothing variable AI COGS and locking spend.
5. **Freemium → paid** 🟡 — free tier as funnel. Dangerous with AI: free inference burns real COGS. Gate the expensive calls.

## B. Consumption / usage models
6. **Pay-per-call / per-token / per-inference** 🟢 — meter the unit of compute or output. Wins for API/infra plays; transparent, scales with value if the unit ≈ value.
7. **Per-unit-processed** 🟢 — per image inspected, per document parsed, per invoice, per board. Wins for industrial AI where the unit is countable and tied to throughput. *(canonical for visual-inspection)*
8. **Per-transaction / per-event** 🟢 — bill per completed business event (per booking, per shipment scanned).
9. **Credit / prepaid pool** 🟢 — buy credits, burn across features. Wins for multi-product AI; smooths billing, front-loads cash.
10. **Metered with floor + cap** 🟢 — usage pricing bounded by a minimum (protect margin) and a ceiling (protect customer). Best-practice wrapper for any usage model.

## C. Outcome / performance models
11. **Outcome-based / pay-per-result** 🟢 — bill per resolved ticket, per qualified lead, per successful match, per defect caught. Wins when you can *attribute* the outcome and the buyer is results-skeptical. Highest trust requirement.
12. **Gainshare / savings-share** 🟢 — take a % of measured savings or uplift you create. Wins when value is large and provable (energy saved, scrap reduced, downtime avoided). Capture aligns to value created — the maverick's favorite when attribution is clean.
13. **Performance SLA with bonus/penalty** 🟡 — base fee adjusted by hitting accuracy/uptime targets. Wins with sophisticated buyers; signals confidence.
14. **Per-FTE-saved / labor-replacement pricing** 🟢 — price an AI agent at a fraction of the loaded cost of the human work it replaces. Wins for automation/agents; reframes from "software cost" to "cheaper labor."
15. **Risk-share / outcome guarantee** 🟡 — refund or no-charge if a threshold isn't met. Wins to break adoption inertia; needs tight cost control.

## D. Product-as-a-service & hardware-attach
16. **Equipment-as-a-Service (EaaS)** 🟢 — bundle hardware + AI + maintenance into one recurring fee; no capex for customer. Wins in industrial when buyers resist capex and want guaranteed performance. *(turns a one-off rig sale into recurring)*
17. **Hardware-as-a-Service + software attach** 🟢 — sell/lease the device, recurring for the intelligence layer. Wins when AI needs an edge appliance.
18. **Razor + blades** 🟡 — cheap/at-cost hardware, margin on recurring AI/consumables.
19. **Hardware margin + SaaS attach** 🟡 — classic distributor move: keep hardware markup, add recurring AI software on top. Wins for incumbents with installed base.
20. **Deploy fee + recurring** 🟢 — one-time integration/commissioning fee (covers CAC/setup) + recurring intelligence subscription. Common for on-prem industrial AI.

## E. Licensing & IP models
21. **White-label / OEM license** 🟢 — partner rebrands your AI as theirs; you take a license/royalty. Wins when a distribution-rich partner owns the customer. *(key incumbent-collab pattern — see 05)*
22. **Per-deployment / per-site license** 🟡 — fixed fee per factory/site/line. Wins for on-prem where usage metering is hard or unwanted.
23. **Royalty / rev-share on partner sales** 🟢 — % of partner's revenue from your embedded AI.
24. **Model-as-asset / fine-tune licensing** 🟡 — license a domain-tuned model; charge for the trained artifact + updates.
25. **Capability leasing** 🟡 — time-boxed exclusive access to a capability for a vertical/region.

## F. Platform, marketplace & network models
26. **Marketplace take-rate** 🟢 — % of GMV you intermediate. Wins when you match supply/demand the AI makes more efficient.
27. **Two-sided platform** 🟡 — subsidize one side, monetize the other.
28. **Aggregation / demand-capture** 🟡 — own the demand, tax the supply.
29. **Embedded-finance attach** 🟡 — monetize payments/lending/insurance on top of an AI workflow (e.g., QRIS/payment take-rate inside an operations app).
30. **Data network effect / data co-op** 🟢 — customers contribute data, all get a better model; you monetize access to the collective intelligence. Wins for a defensible flywheel.

## G. Data & intelligence models
31. **Data monetization (aggregate/anonymized)** 🟡 — sell benchmarks/insights derived from pooled usage. Wins later, once data scale exists. Watch consent/regulation.
32. **Insights/benchmark subscription** 🟡 — "how do you compare to peers" reporting layer.
33. **Decision-API** 🟢 — sell the decision (approve/deny, route, price) not the raw model. High value-per-call.

## H. Services-led & hybrid models
34. **Productized service (fixed-scope, fixed-price)** 🟢 — packaged AI delivery (audit, pilot, deployment) as a SKU. Wins as a low-friction LAND that funds the recurring EXPAND.
35. **Retainer / fractional-AI-team** 🟢 — monthly capability access. Wins for SMEs without in-house AI; predictable, relationship-deep.
36. **Build-Operate-Transfer (BOT)** 🟢 — you build & run the AI capability, then transfer ownership to the client/partner over time for a fee + earn-out. Wins with incumbents who ultimately want to own it. *(core incumbent pattern — see 05)*
37. **Consulting + IP-rights upsell** 🟡 — advisory that converts into licensed product.
38. **Co-development / co-investment** 🟡 — shared build cost, shared IP/revenue.

## I. Partnership & corporate-structure models
39. **Joint Venture / NewCo** 🟢 — a new entity co-owned by {{ai_partner}} + {{incumbent_partner}}; each contributes assets (capital/distribution vs IP/capability) for equity. Wins for large, strategic, long-horizon bets. *(see 05)*
40. **Channel / reseller / VAR** 🟢 — incumbent resells your AI to its base for a margin; you keep IP. Fast distribution, low control. *(see 05)*
41. **Revenue-share alliance** 🟢 — no new entity; contractual split on jointly-won deals.
42. **Franchise / licensed-operator** 🟡 — license the whole playbook+brand to local operators per territory.
43. **Profit-share-on-savings (incumbent's own ops)** 🟢 — deploy into the partner's own operations, take a cut of the savings created internally before selling externally. Wins as a low-risk proof-ground. *(see 05)*

## J. Pricing levers (composable onto any model above)
44. **Value-based price discrimination** — same capability, different price by segment/value realized (enterprise vs SME vs region).
45. **Land-and-expand laddering** — deliberately cheap entry SKU (pilot/per-unit) engineered to expand into platform/recurring once value is proven.

---

## How to combine (most AI winners are hybrids)
- **Industrial vision** → `20 deploy-fee + 07 per-unit + 16 EaaS option` ; moat via `30 data` + integration lock-in.
- **Agentic automation** → `14 per-FTE-saved` or `11 outcome` + `03 platform-fee floor`.
- **AI for an asset-rich incumbent** → `21 white-label` or `36 BOT` or `39 JV`, often starting at `43 profit-share-on-own-ops` to de-risk.
- **Content/creative AI** → `09 credits` + `03 base fee`; expansion via `34 productized service`.

**Rule:** name the **value metric** (what scales with benefit) before the mechanism. Then wrap any usage model with **floor + cap** (#10) to protect both margin and customer. Cross-check margin in `02-ai-monetization-economics.md`.
