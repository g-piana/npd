# Idea: CRA silent watchdog — SBOM-to-ENISA incident response

> Created: 2026-07-28 | Updated: 2026-07-28 | Status: `investigating`
> Origin: opportunity-log entries #1, #2

---

## 1. Trigger (from Stage 2)

**What changed?** The Cyber Resilience Act requires manufacturers of
products with digital elements to report actively exploited vulnerabilities
to ENISA within 24 hours of "becoming aware." ENISA does NOT know what
components manufacturers use — no SBOM registry exists. The obligation is
entirely on the manufacturer to: (a) continuously monitor vulnerability
feeds, (b) match disclosures against their own SBOMs, (c) determine if
active exploitation is occurring, and (d) self-report within 24 hours.

The "becomes aware" trigger is a deliberately low bar. The clock starts
at awareness, not at confirmation. Discovery and notification must happen
in parallel, not in sequence. A company that has no monitoring in place
can claim ignorance temporarily — but Market Surveillance Authorities
will audit the process, and from Dec 2027 continuous vulnerability
monitoring is a formal conformity requirement.

**Why now?** Mandatory vulnerability reporting starts **11 Sep 2026** (7
weeks). Full CRA compliance including SBOM obligations by **11 Dec 2027**.
Fines up to €15M or 2.5% of global turnover. ENISA SRP launches with no
API — manual web forms only.

---

## 2. Product concept — the "silent watchdog"

### The behavioral insight (the Excel problem)

Companies facing unforeseen compliance obligations default to existing
tools (Excel, email) because: (a) learning a new tool while already
stressed adds anxiety, (b) Excel requires zero training, procurement, or
IT approval, and (c) before their first real incident, companies cannot
feel the pain of inadequate tooling.

However, CRA vulnerability reporting is NOT a slow-burn compliance
project like GDPR consent collection. It is an incident-response
scenario with a 24-hour clock, machine-readable data sources (SBOMs,
NVD, KEV feeds), and a judgment call ("is this actively exploited?")
that must happen at 2am under €15M penalty pressure. Excel cannot
VLOOKUP a CycloneDX dependency tree against the NVD.

### Product design: smoke detector, not dashboard

The product runs in the background with near-zero daily interaction:

1. **Setup (one-time):** Customer uploads SBOMs (CycloneDX/SPDX) or
   connects CI/CD pipeline hook. Designates the reporting authority
   (who can trigger the ENISA filing). Configures notification channels.

2. **Silent monitoring (continuous, invisible):** The system ingests
   SBOMs, resolves component identities (CPE/PURL), matches against
   NVD, CISA KEV, ENISA EUVD, and threat intelligence feeds. Nothing
   visible to the user. No daily logins, no dashboards to check.

3. **Wake-up (incident trigger):** When a component match + active
   exploitation signal converges, the system alerts the designated
   authority with: which products are affected, which versions, what
   the vulnerability is, and a pre-filled ENISA early warning draft.
   The clock is explicitly started and tracked.

4. **Guided cascade:** 24hr early warning → 72hr detailed notification
   → 14-day final report. Each stage is pre-populated with available
   information, gaps are flagged, deadlines are counted down.

**Value proposition:** "You upload your SBOMs once. We watch. You do
nothing until something hits. When the alarm goes off at 2am, this is
the difference between a controlled 4-hour response and a chaotic
36-hour scramble ending in a €15M fine."

No new tool to learn. No daily workflow change. The product is invisible
until the moment it's indispensable.

---

## 3. Combinatorial pairing (from Stage 3)

**Domain insight:** Understanding how edge/IoT device firmware works
makes vulnerability triage fundamentally different from web-app
vulnerability management. A CVE in a library used by embedded firmware
has different exploitability than the same CVE in a cloud service.
The SBOM structure for firmware (Yocto layers, vendor blobs, statically
linked dependencies) is messier than for containerized apps.

**Technical combination:**
- **FastAPI** — lightweight API for SBOM ingestion, webhook
  notifications, ENISA report generation
- **PostgreSQL with graph extensions** — dependency tree modeling,
  transitive dependency resolution, component-to-product mapping
- **Python data pipeline** — SBOM parsing (CycloneDX/SPDX), NVD/KEV
  feed polling, vulnerability matching, CPE/PURL identity resolution
- **NLP/entity extraction** — resolving naming inconsistencies between
  SBOM component names and vulnerability database identifiers
- **Django/HTMX** — minimal web interface for incident response flow
  (only used during incidents, not daily)

**What can't a pure software person build here?** The core monitoring
pipeline is buildable by a generalist. The edge/IoT context for triage
(firmware SBOM structure, embedded OS dependencies, binary-level
component identification) requires hardware-adjacent knowledge. The
graph modeling of transitive dependencies at depth benefits from
applied-math understanding of graph traversal and dependency resolution
algorithms. The CPE/PURL identity matching is partly an NLP / fuzzy
matching problem.

---

## 4. Differentiation and moat (from Stage 4)

**Competitive alternatives:**
- **Do nothing / not look:** The current default for most SMEs. Legally
  risky but behaviorally dominant until the first incident or audit.
- **Manual process:** Check NVD occasionally, grep through component
  lists in Excel, email the CTO. Fails at 2am under time pressure.
- **SBOM generation tools:** Syft, Trivy, cdxgen, DependencyTrack —
  these create the SBOM. They don't monitor it against live threat
  feeds or generate ENISA reports.
- **Enterprise platforms:** Black Duck, Anchore Enterprise — €50k+/yr,
  scanning-focused, require dedicated security team to operate.
- **Consulting firms:** BearingPoint CRA services — human-delivered,
  not scalable, not available at 2am.

**Your structural advantage (product-embedded moats):**
- **Data flywheel:** As more SBOMs flow through the system, the
  component identity matching improves (CPE/PURL resolution, false
  positive reduction, exploitability pattern recognition). Each
  customer's data makes the matching better for all.
- **Zero-friction adoption:** No daily workflow change = no behavior
  change required = no Excel competition. You're not competing with
  Excel; you're providing something Excel cannot do (continuous
  automated monitoring against machine-readable feeds).
- **Network intelligence potential:** Aggregated anonymized data across
  customers reveals which components are most commonly used and which
  vulnerability patterns are emerging — this becomes a unique
  intelligence layer over time.

**Why can't incumbents just add this?** Anchore and Snyk are scanning
companies — their products run in CI/CD and produce reports. The
continuous-monitoring + incident-workflow layer is a different product
category and a different buying moment. BearingPoint is consulting —
productizing it would cannibalize their delivery model. The SBOM
generation tools (Syft, Trivy) are open-source CLI tools with no SaaS
layer. DependencyTrack is the closest conceptually but is a
self-hosted Java application requiring significant setup and
maintenance — not a "set and forget" service.

---

## 5. Revenue line of sight (from Stage 5)

**Buyer:** CTO, Head of Engineering, or Product Security lead at
companies manufacturing connected products. At SMEs (the primary
target): often the founder or CTO directly.

**Budget line:** Compliance / regulatory (strongest — CRA is mandatory).
Could also pull from security or insurance budget (lower premiums for
documented incident response capability).

**Cost of not buying:** Fines up to €15M or 2.5% of global turnover.
But more urgently: the first real incident without tooling means a
scramble that either misses the 24-hour deadline (regulatory
consequence) or produces a sloppy report (reputational consequence).
The cost of the product vs. the cost of one missed deadline is the
sell.

**Can you reach 10 of these buyers?** Need to build. Entry points:
- CRA-focused LinkedIn groups and content
- Industrial Cyber community (in RSS feeds)
- ENISA SRP onboarding events (Jun-Sep 2026)
- Italian IoT/edge manufacturer associations
- Maker/hardware communities (Hackaday, embedded forums) for
  smaller connected-product companies

**Does the product deliver value without you?** Yes. The monitoring
pipeline runs automatically. The incident workflow is self-guided.
No domain expertise needed from the buyer or the founder to operate.

**Pricing model intuition:** SaaS subscription tiered by number of
products (SBOMs) monitored. Free tier for 1 product (land — get the
SBOM uploaded, start monitoring). Paid for 2+ products and advanced
features (team notifications, ENISA report pre-fill, audit trail).
Price point: €200-€500/month for SMEs (comparable to a security tool,
not a consulting engagement).

---

## 6. De-risking plan (from Stage 6)

**Riskiest assumption:** That SME manufacturers will proactively adopt
a monitoring tool *before* their first incident. The behavioral default
is "do nothing until burned." The counter-argument: the Sep 2026
deadline creates a specific window where buyers are thinking about CRA
for the first time and looking for solutions. After the deadline passes
without incident, the urgency drops — until the first fine makes
headlines.

**Secondary risk:** That the "becomes aware" ambiguity becomes a
feature, not a bug, for manufacturers — they choose not to monitor
specifically so they can claim they never became aware. If this
becomes the dominant market behavior, the addressable market shrinks
to only the most compliance-conscious companies.

**Next 3 actions to test it:**
1. **5 Mom Test interviews** with CTOs/engineering leads at SME
   connected-product manufacturers. Key questions:
   - "Walk me through the last time a CVE was reported against a
     component in one of your products. How did you find out?"
   - "How long would it take you right now to determine if libcurl
     8.4.0 is in any of your products?"
   - "Have you heard of the CRA September deadline? What's your plan?"
   - "If a critical vulnerability hit at 2am on a Friday, who would
     handle it and how?"
   Listen for: time-to-determine, manual vs. automated, who decides,
   what tools they use today. Do NOT describe the product.
2. **Scan CRA communities** for the "do nothing" vs "we need tooling"
   split. Look for: complaints about readiness, questions about the
   reporting process, signals of the behavioral default.
3. **Technical spike:** Build a minimal SBOM ingestion + NVD matching
   pipeline (Python, FastAPI, PostgreSQL) to validate the core technical
   assumption — that automated matching is significantly faster and
   more accurate than manual grep. Use a real CycloneDX SBOM from an
   open-source project. Time: 1-2 days with current stack.

**Kill criteria:**
- Fewer than 3/5 interviewees see CRA reporting as a problem worth
  solving proactively (vs. "we'll deal with it if it happens")
- The technical spike reveals that SBOM-to-CVE matching is already
  well-solved by existing open-source tools (DependencyTrack +
  OWASP feeds) and the workflow layer adds insufficient value
- A well-funded startup has launched a similar "silent monitoring"
  product in the last 6 months


# 7. Addressable market

The CRA applies to any "product with digital elements" — hardware or software — that is **placed on the EU market**. This means sold, distributed, or made available to another party. The same logic applies to everything from inexpensive consumer gadgets to B2B software to complex industrial systems. Specifically:

- **Consumer electronics**: smart home devices, routers, cameras, wearables
- **Industrial equipment**: PLCs, industrial controllers, OPC-UA connected devices, edge gateways, SCADA components — these are explicitly in scope, and SBOMs must cover embedded RTOS, communication stacks, protocol implementations, and HMI software components
- **Standalone software**: desktop applications, mobile apps, operating systems, firmware — anything downloadable/installable with a data connection
- **B2B software**: enterprise software, SDKs, libraries, development tools — if sold or distributed to another party
- **Edge devices and robotics**: Jetson modules, Arduino-based products, robotic controllers — anything with connectivity that's placed on the market
- **Commercial open-source**: open-source projects with commercial distribution models

**What's OUT of scope:**

- **Pure SaaS** accessed only through a browser with no downloadable component — pure cloud-native SaaS is generally excluded unless it is part of, or necessary for, the product's core functionality (these fall under NIS2 instead)
- **Medical devices** already regulated under MDR/IVDR
- **Automotive** components under type-approval cybersecurity rules
- **Aviation systems** under EASA frameworks
- **Internal-use-only software** — placing on the market is not considered to take place when a product is manufactured exclusively for one's own use. So your internal manufacturing process software, internal CI/CD tools, internal dashboards — these are out, as long as they're never sold or distributed

**The critical nuance for your product's market sizing:**

The interesting boundary is the **B2B industrial** world. A company that manufactures an OPC-UA connected industrial controller and sells it to factories — that controller is a "product with digital elements" fully in CRA scope. The manufacturer needs an SBOM, needs vulnerability monitoring, needs the 24-hour reporting capability. Same for: edge gateway manufacturers, industrial sensor makers, robotics companies, smart meter manufacturers, building automation equipment makers.

These are exactly the companies most likely to be unprepared. They think of themselves as hardware manufacturers, not software companies. Many have firmware written 10 years ago with dependencies nobody tracked. Their SBOM is nonexistent. They have no PSIRT, no vulnerability monitoring process, and definitely no 24-hour reporting workflow.

**This actually expands and sharpens the target market for Idea A.** The sweet spot isn't just IoT startups making consumer gadgets — it's the broad middle of European B2B manufacturers making connected industrial products: sensors, controllers, gateways, edge devices, robotics components, smart building equipment, energy management systems. These are companies with 50–500 employees, €5M–€100M revenue, deep engineering teams but zero security process maturity. There are thousands of them across the EU. And they have compliance budgets because they already deal with CE marking, machinery directives, and industry standards.
 
 The only segment to exclude from your targeting is: pure SaaS companies (out of CRA scope, under NIS2 instead), medical device manufacturers (exempt, under MDR), automotive (exempt, under type-approval), and companies building tools purely for internal use.
 
---

## Open questions

- Is the "do nothing" behavioral default strong enough to shrink the
  addressable market below viability? Or does the Sep 2026 deadline
  create enough urgency to break through?
- Should the MVP skip the ENISA report pre-fill (which requires
  understanding the SRP form structure, which isn't fully published
  yet) and focus purely on the monitoring + alerting layer?
- Could the product be positioned as "CRA insurance" — framing the
  cost against the potential fine, similar to how cybersecurity
  insurance is sold?
- Is there a wedge via the Italian NIS2 enforcement (Oct 2026)? Same
  incident reporting pattern (24hr/72hr/1-month), home market advantage.
- What happens when ENISA eventually publishes an API for the SRP?
  Does that commoditize part of the value, or does it make the
  monitoring layer more valuable (now you can automate end-to-end)?

## References

- CRA Article 14 text: european-cyber-resilience-act.com/Article_14
- Cycode analysis of readiness gaps (May 2026)
- Element Article 14 reporting guide (Jul 2026)
- CVD Portal SME guide to incident reporting (May 2026)
- SBOM.md in references/ — tooling gap analysis
- ENISA SRP page
