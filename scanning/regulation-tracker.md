# EU Regulation Tracker — NPD Scanning

> Last updated: 2026-07-26
> Purpose: Dated map of EU regulatory forcing functions relevant to NPD
> scope (medtech, defense/aerospace, edge/IoT, data engineering, ML/AI).
> Excluded sectors: legal, customer service, accounting, finance.

---

## How to read this

Each regulation is assessed for **trigger strength** — how directly it
creates mandatory, budgeted demand that you can serve with a digital
product *without* needing regulatory certification yourself. Deadlines
in **bold** are upcoming; struck-through dates have passed.

---

## 1. EU AI Act

**Status:** In force since 1 Aug 2024. Phased application, significantly
amended by the Digital Omnibus on AI (Council final approval 29 Jun 2026).

### Key deadlines (post-Omnibus)

| Date | What applies | Passed? |
|------|-------------|---------|
| ~~2 Feb 2025~~ | Prohibited AI practices banned | Yes |
| ~~2 Aug 2025~~ | GPAI transparency obligations; AI literacy duty | Yes |
| **2 Aug 2026** | Article 50 transparency obligations (disclosure of AI interactions) | Imminent |
| **2 Dec 2026** | Watermarking / synthetic content marking for systems already deployed | 5 months |
| **2 Dec 2027** | **Stand-alone high-risk AI systems (Annex III)** — full obligations: risk management, data governance, technical documentation, post-market monitoring, human oversight | 17 months |
| **2 Aug 2028** | High-risk AI embedded in regulated products (Annex I) — e.g., AI in medical devices, machinery | 25 months |

### NPD relevance: HIGH

**Why now:** The Omnibus delay from Aug 2026 to Dec 2027 for high-risk
obligations creates a 17-month window where companies know they must comply
but standards and tooling are still catching up. The market is buying
compliance tooling *now*, not in 2027.

**Opportunity areas (no certification needed):**
- Post-market monitoring platforms for high-risk AI deployers
- AI risk management and documentation tooling (technical documentation
  per Annex IV, risk management systems per Art. 9)
- Automated AI system classification tools (Art. 6 — is this high-risk?)
- Transparency/watermarking tools for GPAI output (Art. 50)
- AI governance dashboards for enterprises (model inventory, compliance status)

**Medtech intersection:** SaMD (Software as Medical Device) developers face
*dual-track* compliance — MDR/IVDR plus AI Act. Tooling that bridges both
frameworks has very few competitors.

---

## 2. European Health Data Space (EHDS)

**Status:** Regulation (EU) 2025/327 entered into force 26 Mar 2025.
EHDS Board established via first implementing act, Apr 2026.

### Key deadlines

| Date | What applies | Passed? |
|------|-------------|---------|
| ~~Jun 2025~~ | Member states appoint National Digital Health Authorities | Yes |
| ~~Jan 2026~~ | Healthcare providers & EHR vendors certify interoperability & security | Yes (patchy) |
| **Mar 2027** | Commission adopts key implementing acts; Health Data Access Bodies established; templates for data access applications | 8 months |
| **Mar 2029** | Primary use: Patient Summaries, ePrescriptions exchanged cross-border; most secondary use rules apply | ~3 years |
| **Mar 2031** | Primary use: medical images, lab results, discharge reports operational | ~5 years |

### NPD relevance: HIGH

**Why now:** 2025–2027 is the "secondary legislation phase" — implementing
acts are being drafted, member states are building data hubs. Tooling demand
is *now*, before the 2029 operational deadline.

**Opportunity areas (no certification needed):**
- De-identification / pseudonymization pipelines for clinical free-text
  (NLP/SLM + domain knowledge of clinical data messiness)
- Data quality and interoperability tooling — mapping legacy EHR formats to
  EHDS-required standards (HL7 FHIR)
- Secure processing environment infrastructure for secondary use
- Data quality and utility labelling tools (required from Mar 2027)
- Health Data Access Body workflow platforms

**Your edge:** 15yr medtech = you know how messy clinical data actually is.
Pure software vendors underestimate this. Less than 3% of health data is
currently used for secondary purposes — massive gap.

---

## 3. Cyber Resilience Act (CRA)

**Status:** Entered into force 10 Dec 2024. Phased application.

### Key deadlines

| Date | What applies | Passed? |
|------|-------------|---------|
| ~~11 Jun 2026~~ | Conformity assessment body notification framework | Yes |
| **11 Sep 2026** | **Mandatory vulnerability reporting** — manufacturers must report actively exploited vulnerabilities to ENISA within 24 hours | 7 weeks |
| **11 Dec 2027** | **Full compliance** — all essential cybersecurity requirements, conformity assessments, technical documentation, CE marking, SBOM obligations | 17 months |

### NPD relevance: HIGH (especially for edge/IoT)

**Why now:** Sep 2026 vulnerability reporting is *imminent*. Dec 2027 full
compliance requires SBOM (Software Bill of Materials) for every product with
digital elements. Most manufacturers of connected products, including edge
devices, have no tooling for this.

**Opportunity areas (no certification needed):**
- SBOM generation, management, and monitoring platforms
- Automated vulnerability reporting workflows (24hr/72hr/14-day cascade)
- Secure-by-design compliance tooling for hardware manufacturers
- CRA compliance dashboards with evidence collection for conformity assessment
- Edge-device specific security monitoring (Jetson, embedded Linux, Arduino-class)

**Your edge:** Jetson/Arduino/edge expertise means you understand the
embedded device world where CRA compliance is hardest. Pure cybersecurity
vendors don't understand hardware; hardware people don't understand
compliance workflows.

---

## 4. NIS2 Directive

**Status:** Transposition deadline was 17 Oct 2024. As of Jul 2026,
~2/3 of member states have completed transposition. Italy transposed
Dec 2025, enters force Oct 2026. France still finalising. Enforcement
accelerating.

### Key facts

| Item | Detail |
|------|--------|
| Scope | ~160,000 entities across EU (vs ~15,000 under NIS1) |
| Italy | Law in force from 1 Oct 2026 |
| Reporting | 24hr early warning, 72hr incident report, 1-month final report |
| Art. 20 | Personal board-level accountability for cybersecurity |
| Art. 21 | 10 mandatory risk management measures |

### NPD relevance: MEDIUM-HIGH

**Why now:** Massive scope expansion (10x more entities than NIS1) means
tens of thousands of organizations need cybersecurity risk management,
incident reporting, and board-level governance tooling for the first time.
Italy's Oct 2026 enforcement date is imminent.

**Opportunity areas:**
- Cybersecurity risk management platforms aligned to Art. 21 measures
- Incident reporting workflow tools (24hr/72hr/1-month cascade)
- Board-level cybersecurity governance dashboards (Art. 20 accountability)
- Supply chain security assessment tools

**Note:** NIS2 overlaps significantly with CRA for connected products.
A combined CRA+NIS2 compliance offering could be compelling.

---

## 5. EU Data Act

**Status:** Applicable since 12 Sep 2025 (most obligations).

### Key deadlines

| Date | What applies | Passed? |
|------|-------------|---------|
| ~~12 Sep 2025~~ | Core rights/obligations in force; data access; cloud switching | Yes |
| **12 Sep 2026** | "Data by design" — connected products placed on market must enable data sharing by design | 7 weeks |
| **12 Sep 2027** | Unfair contractual terms rules extend to pre-2025 contracts; full portability standards | 14 months |

### NPD relevance: MEDIUM

**Why now:** Sep 2026 "data by design" deadline applies to any new
connected product. Manufacturers of IoT/edge devices need tooling to make
their products Data Act compliant.

**Opportunity areas:**
- Data access APIs and portability tooling for connected device manufacturers
- Data sharing consent/rights management for IoT ecosystems
- Cloud switching facilitation tools

**Your edge:** Edge computing expertise (Jetson/Arduino) + data engineering
= you understand both the device side and the data infrastructure side.

---

## 6. MDR / IVDR (Medical Device Regulation)

**Status:** MDR fully applicable since May 2021. IVDR since May 2022.
Major reform proposal published 2026. EUDAMED modules becoming operational.

### Key deadlines

| Date | What applies | Passed? |
|------|-------------|---------|
| ~~May 2026~~ | EUDAMED first 4 modules declared fully functional (Nov 2025) | Yes |
| **28 Nov 2026** | Legacy devices must be registered in EUDAMED | 4 months |
| **Dec 2027** | Final transition deadline for high-risk MDR devices | 17 months |
| **Dec 2028** | Medium-risk MDR devices; Class C IVDR devices | 29 months |
| **Aug 2027–2028** | AI Act obligations begin applying to AI-enabled medical devices | 13–25 months |

### NPD relevance: HIGH (for tooling, not devices)

**Why now:** The medtech industry is being squeezed from three directions
simultaneously: MDR transition deadlines, EUDAMED mandatory registration,
*and* AI Act obligations for SaMD. Most manufacturers are overwhelmed.

**Opportunity areas (compliance tooling, NOT medical devices):**
- Post-market surveillance data management platforms (PMCF, PSURs, vigilance)
- EUDAMED registration and UDI management tooling
- Clinical evaluation report automation (literature search, evidence synthesis)
- Dual-track MDR + AI Act compliance management for SaMD developers
- Technical documentation generation/management

**Your edge:** 15 years inside the industry. You know the pain of PMS,
clinical evaluations, and Notified Body interactions from the inside. This
is your strongest domain advantage across all regulations.

---

## 7. CSRD (Corporate Sustainability Reporting Directive)

**Status:** Significantly narrowed by Omnibus I (Dec 2025 agreement,
directive published Feb 2026). Now applies only to companies with
>1,000 employees AND >€450M turnover. Revised ESRS adopted Jul 2026.

### Key deadlines

| Date | What applies | Passed? |
|------|-------------|---------|
| ~~2024–2025~~ | Wave 1 companies (former NFRD reporters) reporting | Yes |
| **2025–2026** | Wave 1 transition exemption — may skip CSRD filings | Current |
| **2028** | Next mandatory wave — reports for FY2027 under simplified ESRS | 18 months |

### NPD relevance: LOW for you

**Why now (weak):** Scope narrowed dramatically. The remaining in-scope
companies are large enterprises that will use big consulting firms, not
solo-founder products. The Omnibus reduced the addressable market
significantly.

**Parking recommendation:** Unless you identify a specific niche where your
technical skills add value (e.g., automated Scope 3 data collection from
IoT/manufacturing), deprioritize CSRD.

---

## Priority ranking for NPD ideation

Based on trigger strength, deadline urgency, standalone product potential,
and breadth of addressable market (not limited to medtech).

| Priority | Regulation | Trigger strength | Standalone product fit | Key window | Target industries |
|----------|-----------|-----------------|----------------------|------------|-------------------|
| 1 | **CRA** (edge/IoT) | Very strong | Excellent — SBOM/vuln tooling is self-serve | Sep 2026–Dec 2027 | IoT, edge, drones, robotics, industrial |
| 2 | **EU AI Act** (cross-industry) | Very strong | Excellent — risk/doc/monitoring platforms | Now–Dec 2027 | Any AI deployer: energy, security, industrial, transport |
| 3 | **NIS2** | Strong | Good — compliance platforms scale across sectors | Now–2027 | Energy, transport, healthcare, manufacturing, digital infra |
| 4 | **Data Act** (IoT/edge) | Medium-strong | Good — data access tooling for device makers | Sep 2026–2027 | IoT, smart energy, connected vehicles, industrial |
| 5 | **EHDS** | Strong | Medium — deep health data knowledge still helps | Now–2029 | Healthcare (familiar territory but don't over-index) |
| 6 | **MDR/IVDR** | Strong | Medium — risk of consulting-shaped products | Now–2028 | Medtech only (narrow) |
| 7 | **CSRD** | Weak (post-Omnibus) | Low | Deprioritize | Large enterprises only |

**Ranking rationale:** CRA and AI Act rise to the top because they hit the
broadest range of industries (not just medtech), the problems are
well-suited to standalone tooling, and the physics/edge/ML stack applies
directly. MDR/IVDR drops because it's narrow (medtech-only buyers) and the
products tend to require domain expertise to operate — the consulting trap.

---

## Cross-cutting observations

1. **The Omnibus effect:** The Digital Omnibus has pushed AI Act high-risk
   deadlines out by 16 months, but this creates *more* demand for tooling,
   not less — companies now have runway to buy and implement solutions.

2. **Regulation convergence across industries:** The strongest opportunities
   sit where 2+ regulations collide on the same buyer. This is NOT limited
   to medtech. Examples across sectors:
   - **Drone manufacturers:** CRA (cybersecurity) + AI Act (autonomous
     systems = high-risk) + Data Act (connected product data sharing)
   - **Energy/grid operators:** NIS2 (critical infrastructure) + AI Act
     (predictive maintenance AI) + Data Act (smart meter data)
   - **Industrial robotics:** CRA (connected products) + AI Act (high-risk
     if safety-critical) + Machinery Regulation
   - **Security/surveillance:** AI Act (biometrics = high-risk/prohibited)
     + NIS2 (if critical infrastructure)

3. **Italy timing:** Italy transposed NIS2 early (enters force Oct 2026)
   and is your home market. Good for early-mover validation in any sector.

4. **Standalone product test:** CRA and AI Act produce the best standalone
   product opportunities because compliance is procedural and repeatable.
   SBOM management, vulnerability reporting, AI risk documentation — these
   are workflows, not consulting engagements. The buyer self-serves.

---

## Sources

- EU Commission Digital Strategy: digital-strategy.ec.europa.eu
- AI Act implementation: artificialintelligenceact.eu
- Gibson Dunn, DLA Piper, Addleshaw Goddard analyses of AI Omnibus (May–Jun 2026)
- CSA Labs analysis of AI Omnibus (Jul 2026)
- Kennedys Law EHDS analysis (Mar 2026)
- ECSO NIS2 Transposition Tracker (May 2026)
- Wavestone NIS2 country tracker (Feb 2026)
- EU Commission CRA implementation page
- Normative, KPMG, Deloitte CSRD/Omnibus analyses (2026)
- MedDeviceGuide MDR/IVDR 2026 analysis
- ElendiLabs MDR/IVDR/AI Act convergence analysis (Jul 2026)
