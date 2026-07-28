# Opportunity Log

> Running log of demand triggers spotted during Stage 2 scanning.
> Add entries as you find them. Review weekly; promote promising ones to
> a full idea card in `ideas/`.

## How to use this log

- **One row per trigger.** A trigger is a forcing function: a regulation
  deadline, a cost/capability threshold crossed, or a workflow pain point
  surfaced in conversation.
- **Keep entries short.** This is a radar screen, not an analysis document.
  Analysis happens when you promote a trigger to an idea card.
- **Tag the trigger type** so you can filter: `REG` (regulation),
  `TECH` (technology shift), `PAIN` (observed workflow pain),
  `TREND` (market/industry trend).
- **Date everything.** Triggers age; a regulation deadline that passed is
  no longer a "why now."
- **Status:** `open` → `exploring` → `promoted` (moved to ideas/) or `parked`.

---

## Log entries

### #1 — CRA vulnerability reporting workflow
- **Date:** 2026-07-28
- **Type:** REG
- **Trigger:** Manufacturers of products with digital elements must report actively exploited vulnerabilities to ENISA within 24hrs. ENISA confirmed no API at launch — manual web form only. Most manufacturers have no internal detection-to-disclosure workflow.
- **Why now:** **11 Sep 2026** — 7 weeks away. Fines up to €15M or 2.5% global turnover.
- **Domains:** Edge/IoT, drones, robotics, industrial, any connected product
- **Asset fit:** Strong — Django/FastAPI for workflow platform; edge knowledge for device context; graph modeling for dependency mapping
- **Status:** open
- **Notes:** Four practitioner readiness gaps identified: no designated reporter, no escalation path, no measured detection-to-disclosure timeline, no SBOM-to-vulnerability link. Existing tools (Anchore, Trivy) do SBOM generation, not the reporting workflow. The gap is workflow orchestration, not scanning.

---

### #2 — CRA SBOM consumption gap
- **Date:** 2026-07-28
- **Type:** REG+PAIN
- **Trigger:** 86% of orgs find SBOM generation challenging; most who receive SBOMs store them but never operationalize them. No pipeline from SBOM → vulnerability alert → triage → action. The artifact decays the moment it is delivered because software components change with every update.
- **Why now:** **11 Dec 2027** for full SBOM obligation, but needed NOW for Sep 2026 vuln reporting. 73% say SBOMs enable better vulnerability management, yet few actually act on them.
- **Domains:** Edge/IoT, industrial, any software product manufacturer
- **Asset fit:** Strong — Python data pipeline for SBOM parsing/monitoring; PostgreSQL for component/dependency storage; graph modeling for dependency trees
- **Status:** open
- **Notes:** Generation tools exist (Syft, Trivy, cdxgen). The gap is consumption + operationalization. SMEs especially underserved — enterprise tools (Black Duck) too expensive. Closely linked to #1 — SBOM operationalization is a prerequisite for the 24hr vulnerability reporting workflow.

---

### #3 — Regulation convergence on drones
- **Date:** 2026-07-28
- **Type:** REG
- **Trigger:** Drones are "products with digital elements" (CRA) AND autonomous/AI-enabled drones are likely high-risk under AI Act. EU Counter-UAS Action Plan proposes registration down to 100g, geo-fencing requirements by 2027. Drone manufacturers face triple compliance: CRA cybersecurity + AI Act risk management + EASA type approval.
- **Why now:** CRA vuln reporting Sep 2026; AI Act high-risk Dec 2027; Counter-UAS geo-fencing 2027. Remote ID mandatory since Jan 2026 for drones >100g.
- **Domains:** Drones, edge, security
- **Asset fit:** Medium — need to learn drone industry specifics, but edge/CV/ML skills transfer directly. CRA workflow from #1 reusable.
- **Status:** open
- **Notes:** Young industry, few compliance tooling vendors. Drone manufacturers are hardware-first companies who struggle with software compliance. Worth investigating: how many EU drone manufacturers exist, and what's their size/budget?

---

### #4 — Data Act "data by design" for connected products
- **Date:** 2026-07-28
- **Type:** REG
- **Trigger:** Connected products placed on EU market after Sep 2026 must enable data sharing by design. Manufacturers need data access APIs, user rights management, and portability baked into products.
- **Why now:** **12 Sep 2026** — 7 weeks away.
- **Domains:** Edge/IoT, industrial, smart energy, connected vehicles
- **Asset fit:** Medium — Django/FastAPI for API layer; edge knowledge for device-side. Less unique differentiation vs. generic API tooling.
- **Status:** open
- **Notes:** Same buyers as CRA (#1, #2) face this deadline simultaneously. Combined CRA + Data Act offering could be compelling. But Data Act alone may be too generic to differentiate.

---

### #5 — Predictive maintenance "action gap"
- **Date:** 2026-07-28
- **Type:** TECH+PAIN
- **Trigger:** Industry spent billions on sensors and IoT, millions on analytics — but the gap between diagnostic insight and maintenance action remains wide. Physics-based digital twins (comparing actual vs. expected behavior under specific conditions) outperform pure data-driven approaches but require physics expertise most software vendors lack.
- **Why now:** Ongoing — accelerating with NIS2 critical infrastructure requirements, industrial decarbonization pressure, and PINNs crossing usability threshold.
- **Domains:** Energy (wind turbines, grid), renewables, industrial manufacturing
- **Asset fit:** Strong potential — physics PhD + PyTorch + signal processing is exactly the profile for physics-informed anomaly detection. PINNs directly applicable. BUT enterprise market crowded (Augury, GE Predix, Tractian).
- **Status:** open
- **Notes:** SME manufacturers with legacy equipment are underserved by enterprise platforms. Edge deployment (Jetson) could differentiate for on-site, privacy-preserving inference. Need to validate: is there a buyer segment not served by existing platforms? Who specifically would pay?

---

### #6 — NIS2 Italy enforcement
- **Date:** 2026-07-28
- **Type:** REG
- **Trigger:** Italy transposed NIS2, entering force Oct 2026. ~10x more entities in scope vs. NIS1. Board-level personal accountability (Art. 20). Ten mandatory risk management measures (Art. 21). 24hr/72hr/1-month incident reporting cascade.
- **Why now:** **1 Oct 2026** — Italy enforcement. Home market advantage.
- **Domains:** Critical infrastructure: energy, transport, health, digital infra
- **Asset fit:** Medium — less domain depth in NIS2. Incident reporting workflow overlaps architecturally with CRA (#1).
- **Status:** open
- **Notes:** Italy = home market, early enforcement. But NIS2 is very broad (governance, risk, supply chain) — hard to differentiate as solo founder without narrowing to a specific vertical or specific workflow. Could be a market access play: use NIS2 Italian enforcement as early validation for a CRA-related product that also satisfies NIS2 reporting.

---

### #7 — AI Act transparency and watermarking
- **Date:** 2026-07-28
- **Type:** REG
- **Trigger:** From Aug 2026, AI systems interacting with people must disclose they are AI. From Dec 2026, AI-generated content must be watermarked / machine-detectable.
- **Why now:** **2 Aug 2026** (disclosure) — imminent; **2 Dec 2026** (watermarking) — 5 months
- **Domains:** Cross-industry: any company using chatbots, generated content, synthetic media
- **Asset fit:** Medium — LLM/NLP skills applicable; watermarking is specialized. AI Act compliance space is crowded at enterprise level (Vanta, OneTrust, Credo AI, €30k-100k/yr). Legalithm free for SMEs through 2028.
- **Status:** open
- **Notes:** Crowded. Less room for differentiation unless narrowing to specific technical requirement (watermarking detection, synthetic media forensics). May park if no niche emerges.

---

### #8 — PINNs crossing usability threshold
- **Date:** 2026-07-28
- **Type:** TECH
- **Trigger:** Physics-Informed Neural Networks now achieve 10³–10⁵x speedup vs. FEM. PyTorch implementations mature. Neural Operators (Fourier Neural Operator) enable real-time physics simulation that was previously compute-prohibitive.
- **Why now:** Capability threshold crossed 2025-2026. Frameworks maturing (DeepXDE, NVIDIA Modulus). But PINNs are a capability, not a product.
- **Domains:** Energy (wind/solar thermal), industrial (structural monitoring), aerospace (non-destructive testing)
- **Asset fit:** Strong — physics PhD + PyTorch = exact profile. Short learning distance.
- **Status:** open
- **Notes:** MUST pair with a demand trigger (#5, #6, or new) to become actionable. Pure tech push without demand is exactly what the process warns against. The question is: who has a physics-simulation problem that is currently too slow or expensive, AND has budget to buy a solution?

---

## Archived / parked triggers

| # | Original date | Trigger summary | Parked reason | Parked date |
|---|--------------|-----------------|---------------|-------------|
| | | | | |
