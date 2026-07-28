# Idea: Drone manufacturer multi-regulation compliance platform

> Created: 2026-07-28 | Status: `raw`
> Origin: opportunity-log entry #3

---

## 1. Trigger (from Stage 2)

**What changed?** Drones are simultaneously subject to CRA (products with
digital elements), AI Act (autonomous systems likely classified as
high-risk), EASA regulations (Remote ID mandatory since Jan 2026 for
drones >100g, geo-fencing requirements by 2027), and the EU Counter-UAS
Action Plan (registration down to 100g, security chip requirements). No
single compliance tool addresses this convergence.

**Why now?** CRA vulnerability reporting Sep 2026; AI Act high-risk
obligations Dec 2027; EASA geo-fencing 2027; Counter-UAS chip
requirements upcoming. Drone manufacturers are hardware-first companies
with limited software compliance maturity.

---

## 2. Combinatorial pairing (from Stage 3)

**Domain insight:** Drone manufacturers are a specific segment of connected-
product manufacturers who face *more* overlapping regulations than most,
but have *less* compliance infrastructure. They think in terms of flight
controllers, firmware, and sensor payloads — not SBOMs, risk management
systems, and conformity assessments.

**Technical combination:** Django/FastAPI (compliance workflow platform) +
PostgreSQL with graph extensions (SBOM dependency tracking + regulatory
requirement mapping) + edge/CV knowledge (understanding drone firmware,
sensor systems, and AI components from the device side).

**What can't a pure software person build here?** Understanding the
technical architecture of drone systems (flight controller firmware,
companion computers, sensor payloads, communication stacks) is necessary
to map regulatory requirements accurately. A compliance platform that
treats a drone like a web app will produce nonsensical SBOM structures
and risk assessments.

---

## 3. Differentiation and moat (from Stage 4)

**Competitive alternatives:**
- **Generic CRA compliance tools** — don't understand drone-specific
  technical architecture or EASA requirements
- **EASA-focused compliance tools** (DeskDrone, AirHub) — focused on
  operator compliance (flight permissions, pilot licensing), not
  manufacturer product compliance
- **Manual process** — consultants + spreadsheets

**Your structural advantage:**
- Vertical specialization: single platform covering CRA + AI Act + EASA
  for drone manufacturers specifically
- Reuse CRA workflow engine from Idea A, narrowed to a vertical with
  domain-specific templates and requirement mappings
- First-mover in an underserved niche

**Why can't incumbents just add this?** Generic CRA tools would need to
build drone-specific knowledge. EASA tools are operator-focused, not
manufacturer-focused. The convergence niche is too small for enterprise
vendors to prioritize.

---

## 4. Revenue line of sight (from Stage 5)

**Buyer:** Head of Engineering, Regulatory Affairs Manager, or CTO at
drone manufacturers. At SMEs (most EU drone companies): founder/CEO.

**Budget line:** Compliance / product certification.

**Cost of not buying:** Multiple parallel non-compliance risks: CRA fines
(€15M), AI Act fines (up to €35M or 7% of turnover), inability to place
product on EU market without CE marking, EASA operational restrictions.

**Can you reach 10 of these buyers?** Need to validate. Entry points:
sUAS News community, EASA drone manufacturer registry, Italian drone
industry association (Roma Drone Conference), LinkedIn drone manufacturer
groups. **Critical pre-question: how many EU drone manufacturers exist
and what is their size/budget?**

**Pricing model intuition:** SaaS subscription tiered by number of drone
models/product lines managed.

---

## 5. De-risking plan (from Stage 6)

**Riskiest assumption:** That there are enough EU drone manufacturers with
sufficient budget to sustain a SaaS product. The drone manufacturing
market may be too small or too fragmented for a vertical compliance tool.

**Next 3 actions to test it:**
1. **Market sizing:** Research how many drone manufacturers operate in the
   EU, their average size, and current compliance spending. Check EASA
   registered manufacturer lists.
2. **3-5 interviews** with drone manufacturer founders/CTOs. Mom Test:
   "How are you handling CRA compliance?" "Do you have an SBOM for your
   firmware?" "How many overlapping regulations are you tracking?"
3. **Validate reuse:** If Idea A (generic CRA workflow) is built first,
   test whether a drone-specific configuration layer is sufficient or
   whether the vertical needs its own product.

**Kill criteria:**
- Fewer than 200 EU drone manufacturers of sufficient size (>€1M revenue)
- Interviews reveal manufacturers outsource all compliance to consultants
  and have no appetite for self-serve tools
- The CRA workflow from Idea A covers 90% of their needs without
  drone-specific customization — meaning the vertical niche isn't
  differentiated enough

---

## Open questions

- Is this a standalone product or a vertical configuration of Idea A?
- What is the actual market size for EU drone manufacturers?
- Do drone manufacturers buy software tools, or do they rely entirely on
  consultants and Notified Bodies?
- Could this extend to other "convergence verticals" (robotics
  manufacturers, smart home device makers)?

## References

- EU Counter-UAS Action Plan COM(2026) 81 — Bird & Bird analysis
- EASA Easy Access Rules for UAS (Jun 2026 revision)
- sUAS News (in RSS feeds)
