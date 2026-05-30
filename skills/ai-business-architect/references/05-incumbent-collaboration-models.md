# 05 — Incumbent × AI-Specialist Collaboration Models

The structures for when an **asset-rich-but-AI-poor incumbent** ({{incumbent_partner}} — distribution, clients, installed hardware, capital, brand) partners with an **AI specialist** ({{ai_partner}} — capability, IP, delivery speed). This is the LKH-class pattern. Grounded in `research-output/nlm-research-report.md` (EY, Acquis, Deloitte BOTT, Aluko & Oyebode, Jim Bergman, Bristows). Kept client-agnostic — bind specifics in `project-variables.md`.

## 1. What each side brings (and fears)

| | Brings | Fears |
|---|---|---|
| **{{incumbent_partner}}** | distribution, customer trust, installed base, capital, domain access, regulatory standing | disintermediation, IP leakage, channel conflict, betting on unproven tech |
| **{{ai_partner}}** | AI capability, IP, speed, talent | losing IP, being a cheap subcontractor, customer-relationship capture by partner, non-payment |

> Design the deal around what the incumbent **fears**, not just what it gains. Implementation partnerships work because the specialist accelerates **time-to-value** for the incumbent's deployment (per Acquis) — sell speed and de-risking.

## 2. The five collaboration archetypes (low → high commitment)

| Archetype | Structure | Who owns customer / IP | Best when | Watch-outs |
|---|---|---|---|---|
| **Channel / Reseller / VAR** | Incumbent resells specialist's AI to its base for a margin | Incumbent owns customer; specialist keeps IP | Fast distribution, low commitment, prove demand | Thin margin, specialist invisible, no data access |
| **White-Label / OEM license** | Incumbent rebrands AI as its own; pays license/royalty | Incumbent fronts brand; specialist owns core IP | Incumbent's brand = the trust asset | Price the license vs lost direct margin; protect core IP (per Bristows OEM pricing) |
| **Profit-Share on Own-Ops** | Deploy into incumbent's *own* operations first; specialist takes % of savings created | Shared; internal data | Low-risk proof-ground before external sale | Attribution must be measurable; align on baseline |
| **Build-Operate-Transfer (BOT/BOTT)** | Specialist builds + operates the capability, then transfers ownership to incumbent after ROI proven | Transfers to incumbent over time | Incumbent ultimately wants to own the capability | Define transfer trigger, price, earn-out, knowledge-transfer (per Deloitte BOTT, Acquis) |
| **Joint Venture / NewCo** | Co-owned entity; each contributes assets (capital/distribution vs IP/capability) for equity | NewCo owns it; governed by JV agreement | Large, strategic, long-horizon, shared upside | Governance, deadlock, contribution valuation, exit (per EY) |

**Sequencing tip:** de-risk by *laddering* — start with **profit-share-on-own-ops** or a **paid pilot/channel** to prove value, then escalate to **white-label/BOT/JV** once trust and numbers exist. Don't open with a JV.

## 3. Revenue & value-split logic

- **Channel/white-label:** margin or royalty % — benchmark against the direct margin the specialist forgoes for the distribution gained.
- **Gainshare/profit-share:** % of *measured* savings/uplift; requires an agreed baseline and clean attribution (per Acquis). The maverick capture when value is large and provable.
- **JV:** equity split reflects *contributed asset value* — distribution + capital vs IP + capability. Value the IP contribution explicitly (avoid the specialist being diluted to a vendor).
- **Incentive layering** (per Jim Bergman): gainsharing + milestone-based payments + tiered reward structures (Bronze/Gold) to sustain partner performance over time.

## 4. IP — the #1 litigation trigger (per Aluko & Oyebode)

Define explicitly in every agreement:
1. **Background IP** — pre-existing, owned by each party before the collaboration. (Specialist's core models/engine = stays Background IP.)
2. **Foreground IP** — created specifically during the collaboration. Pre-agree ownership (joint? specialist-owned with incumbent license? work-for-hire?).
3. **Derivative / Improvement rights** — rights over modifications/improvements to Background IP made during the project.

**Open-source warning (per Aluko & Oyebode):** using GPL-style copyleft without review can **contaminate** proprietary software and trigger source-disclosure obligations. Run a license review before shipping into a partner deployment.

**Data rights:** specify who owns/accesses the data the deployment generates — it often outvalues the fee (feeds the moat, `02`). The party that keeps the data flywheel keeps the long-term power.

## 5. Integration & adoption risk (per Jimo, Salesmotion)

Incumbent deployments fail on **legacy-system friction** and the **staff education gap**, not on model accuracy. Budget explicit **change management** (training, champions, phased rollout). Bake it into scope and price.

## 6. Decision guide

- Need distribution fast, low commitment → **Channel** (then white-label).
- Incumbent's brand is the asset, specialist guards IP → **White-Label/OEM**.
- Want a no-risk proof before external GTM → **Profit-Share-on-Own-Ops**.
- Incumbent wants to eventually own the capability → **BOT/BOTT**.
- Strategic, large, shared-upside, long horizon → **JV/NewCo**.

> Always pair with `04` (GTM/deal-structuring) and `06` (stress-test the chosen structure's economics & power balance).
