==Under the EU **Cyber Resilience Act (CRA)**, the 24-hour reporting mandate for actively exploited vulnerabilities goes into effect on **September 11, 2026**==. This rapid response timeline makes the **Software Bill of Materials (SBOM)** an operational necessity. Without an SBOM, it is virtually impossible to map a newly discovered vulnerability to your products within 24 hours. [[1](https://www.securitytoday.de/en/2026/03/16/sbom-practical-check-how-your-company-implements-the-software-bill-of-materials-by-september-2026/), [2](https://digital-strategy.ec.europa.eu/en/policies/cra-reporting), [3](https://sbomify.com/compliance/eu-cra/)]

---

What is an SBOM?

An **SBOM** is a comprehensive, machine-readable inventory of all software components, dependencies, and hierarchical relationships within a product. Under the CRA, it acts as an operational security document rather than a bureaucratic filing. [1](https://www.enisa.europa.eu/sites/default/files/2026-06/SBOM%20Adoption%20State%20of%20Play%202026.pdf) , [2](https://www.wirtek.com/blog/how-to-build-an-sbom-for-cyber-resilience-act-compliance), [3](https://getreadycompliance.eu/sbom-cra-software-bill-of-materials/)


- **Standard Formats:** It must utilize standard industry formats like [CycloneDX](https://cyclonedx.org/) or [SPDX](https://spdx.dev/). [1](https://goregulus.com/cra-requirements/cra-sbom-requirements/)
- **Core Content:** It contains metadata including component names, versions, license identifiers, cryptographic hashes, and supplier origins. [1](https://sbomgenerator.com/blog/eu-cra-requirements), [2](https://www.securitytoday.de/en/2026/03/16/sbom-practical-check-how-your-company-implements-the-software-bill-of-materials-by-september-2026/)
- 
- **Depth:** It must cover at least top-level dependencies, though regulatory guidelines strongly push for full transitive dependency tracking. [1](https://www.enisa.europa.eu/sites/default/files/2026-06/SBOM%20Adoption%20State%20of%20Play%202026.pdf), [2](https://www.wirtek.com/blog/how-to-build-an-sbom-for-cyber-resilience-act-compliance)

Who Generates it and How?

The **manufacturer** of any "product with digital elements" (ranging from IoT hardware to SaaS/enterprise software) bears sole legal responsibility for generating the SBOM. [1](https://www.enisa.europa.eu/sites/default/files/2026-06/SBOM%20Adoption%20State%20of%20Play%202026.pdf), [2](https://sbomify.com/2024/07/10/understanding-the-eu-cyber-resilience-act-the-role-of-sboms-in-enhancing-cybersecurity/), [3](https://craevidence.com/cra-compliance/enisa-srp-onboarding)


- **How it is generated:** It is produced automatically by integrating **Software Composition Analysis (SCA)** or specialized SBOM generation tools directly into the **CI/CD build pipeline**. Every time code is compiled or packaged, a new SBOM is minted to mirror that specific production release. [1](https://socfortress.medium.com/enisa-sbom-adoption-in-2026-from-security-best-practice-to-regulatory-imperative-part-ii-c36fa696e7ee), [2](https://www.wirtek.com/blog/how-to-build-an-sbom-for-cyber-resilience-act-compliance), [3](https://sbomify.com/compliance/eu-cra/), [4](https://finitestate.io/blog/eu-cra-sbom-technical-documentation-guide), [5](https://checkmarx.com/learn/supply-chain-security/understanding-software-bill-of-materials-sbom/)
- **Third-Party Handling:** Manufacturers must perform due diligence on upstream suppliers, demanding SBOMs from them or scanning third-party binaries independently to merge them into the final product SBOM. [1](https://www.opswat.com/blog/eu-cyber-resilience-act-cra-a-roadmap-to-software-supply-chain-and-sbom-compliance), [2](https://socfortress.medium.com/enisa-sbom-adoption-in-2026-from-security-best-practice-to-regulatory-imperative-part-ii-c36fa696e7ee)

Who Receives it and What Do They Do With It?

Unlike some U.S. executive orders, **the CRA does not mandate making SBOMs public** to end-users. [1](https://www.enisa.europa.eu/sites/default/files/2026-06/SBOM%20Adoption%20State%20of%20Play%202026.pdf), [2](https://getreadycompliance.eu/sbom-cra-software-bill-of-materials/)

| Receiver                                   | What They Do With It                                                                                                                                                                                                                           |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Internal Security & Engineering Teams**  | Continuously ingest the SBOM into automated vulnerability management platforms. They monitor it against live threat intelligence feeds (like the NVD or KEV) to catch actively exploited bugs and kickstart the 24-hour ENISA reporting clock. |
| **Market Surveillance Authorities (MSAs)** | National authorities receive the SBOM **only upon a reasoned request** during compliance checks or incident audits to verify the product's secure architecture.                                                                                |
| **Enterprise B2B Customers** _(Optional)_  | Though not legally forced by the CRA text, commercial buyers frequently demand the SBOM during procurement to manage their own digital supply chain risks.                                                                                     |

_Note: You do not attach the raw SBOM file to your 24-hour ENISA notification. Instead, you use your internal SBOM to instantly pinpoint which product versions are affected so you can report them through the **Single Reporting Platform (SRP)**._ , [2](https://sbomify.com/compliance/eu-cra/)


---

Gaps in Tooling: Generation vs. Operationalization

You are entirely correct: generating a flat file is solved, but orchestrating the real-time workflow required by the 24-hour reporting rule has severe tooling gaps. According to ENISA's 2026 SBOM Adoption findings, the industry faces several operational bottlenecks: [1](https://socfortress.medium.com/enisa-sbom-adoption-in-2026-from-security-best-practice-to-regulatory-imperative-part-ii-c36fa696e7ee), [2](https://www.mend.io/blog/eu-cyber-resilience-act-compliance-guide/), [3](https://www.securitytoday.de/en/2026/03/16/sbom-practical-check-how-your-company-implements-the-software-bill-of-materials-by-september-2026/)

1. The Vulnerability Matching & Identity Crisis

- **The Gap:** Open-source tools easily write an SBOM component name, but correlating that name to vulnerability databases is broken. Inconsistent naming schemas (CPE vs. PURL) cause massive automated "false positive" storms or missing exposures.
- **The Operational Impact:** Engineering teams spend hours manually triaging whether a CVE actually impacts their version, exhausting the 24-hour notification window before a decision is reached. [1](https://cycode.com/blog/eu-cra-vulnerability-reporting-requirements/), [2](https://socfortress.medium.com/enisa-sbom-adoption-in-2026-from-security-best-practice-to-regulatory-imperative-part-ii-c36fa696e7ee), [3](https://www.timesys.com/security/how-to-actually-understand-and-use-the-6-different-types-of-cisa-sboms/), [4](https://sbomify.com/compliance/eu-cra/), [5](https://finitestate.io/blog/sbom-mistakes)

2. The Dynamic VEX (Vulnerability Exploitability eXchange) Disconnect

- **The Gap:** An SBOM is static, but vulnerability status is highly dynamic. VEX documents exist to state: _"Yes, we use this library, but the vulnerable function is uncallable."_ However, there is a total lack of automated orchestration tools that bind live SCA data, VEX statements, and automated CRA notification drafts together.
- **The Operational Impact:** Security teams are forced to track exploitability analyses via spreadsheets or siloed ticketing systems, slowing down the regulatory escalation process. [1](https://www.securapoint.de/en/blog/sbom-und-der-cyber-resilience-act/), [2](https://fossa.com/blog/sbom-formats-compared-explained/), [3](https://arxiv.org/html/2503.14388v1), [4](https://www.amazee.io/blog/post/dependency-track-software-supply-chain-security/), [5](https://sbomify.com/compliance/eu-cra/)

3. Legacy and Binary Artifact Archeology

- **The Gap:** Pipeline tools work beautifully for modern, greenfield codebases. They fail completely on legacy software, firmware binaries, and proprietary third-party blobs where source code is unavailable.
- **The Operational Impact:** Manufacturers must resort to complex binary analysis tools that struggle to accurately identify transitive dependencies, leading to incomplete compliance records. [1](https://socfortress.medium.com/enisa-sbom-adoption-in-2026-from-security-best-practice-to-regulatory-imperative-part-ii-c36fa696e7ee), [2](https://sbomify.com/compliance/eu-cra/), [3](https://www.keysight.com/us/en/assets/7126-1034/solution-briefs/Achieving-SBOM-Accuracy-Generate-or-Validate-with-Confidence.pdf), [4](https://devops.com/sboms-101-what-you-need-to-know/)

1. Automated CI/CD-to-Registry Pipeline Lack [1](https://www.securitytoday.de/en/2026/03/16/sbom-practical-check-how-your-company-implements-the-software-bill-of-materials-by-september-2026/)

- **The Gap:** There is an absence of turnkey tools that sign, version-control, store, and automatically deprecate SBOMs seamlessly in concert with container or artifact registries.
- **The Operational Impact:** SBOMs become stale immediately after a hotfix is deployed. Organizations struggle to maintain a reliable chronological history of what was exactly live on the EU market at the precise time an exploit occurred. [1](https://goregulus.com/cra-requirements/cra-sbom-requirements/), [2](https://socfortress.medium.com/enisa-sbom-adoption-in-2026-from-security-best-practice-to-regulatory-imperative-part-ii-c36fa696e7ee), [3](https://sbomify.com/compliance/eu-cra/), [4](https://sbomify.com/compliance/eu-cra/)

