# Idea #5: Physics-informed false alarm elimination for industrial monitoring

> Created: 2026-07-28 | Updated: 2026-07-31 | Status: `investigating`
> Origin: opportunity-log entries #5, #8
> Supersedes: `2026-07-pinn-predictive-maintenance.md` (repositioned)

---

## 0. Repositioning note

The original idea card was titled "Physics-informed predictive
maintenance for SME manufacturers." This repositioning emerged from
two inputs:

1. **The 160-email lesson.** Cold outreach to 160+ SME manufacturers
   for Anomaly Inspector received zero responses. Diagnosed as a
   structural channel failure: SMEs lack push (no felt pain), have
   maximum habit (existing process works "well enough"), and high
   anxiety (new tech = project, not purchase). This pattern applies
   equally to a generic "predictive maintenance" pitch.

2. **The false-alarm insight.** The strongest early market is not
   companies that have never tried PdM — it's companies that tried
   and got burned. Data-driven PdM has a well-known failure mode:
   alert floods, alarm fatigue, technicians chasing anomalies that
   turn out to be a hot day or a load change. These buyers have push
   (existing pain with current PdM), low anxiety (already believe in
   the category), budget precedent (already spent money on this), and
   a crisp evaluation question: "does this stop crying wolf?"

3. **The OEM channel.** Equipment OEMs — compressor manufacturers,
   gearbox makers, heat-exchanger builders — are structurally better
   than direct SME sales because: one physics model covers their
   entire machine family across their installed base (inverts the
   per-application cost); new machines ship instrumented (solves the
   "no sensors" kill criterion); OEMs face servitization pressure
   (selling uptime contracts where false alarms cost *them* money);
   and OEMs face CRA + Data Act obligations on their connected
   products (compliance-adjacent credibility compounds).

The core technology is unchanged. The positioning, buyer, and channel
are different.

---

## 1. Trigger (from Stage 2)

**What changed?** Two converging forces:

- **PdM disillusionment cycle.** The predictive maintenance industry
  has spent billions on sensor infrastructure and analytics platforms.
  Many deployments produce alert floods — anomalies that are
  statistically real but operationally meaningless (seasonal effects,
  load changes, sensor drift). Maintenance teams lose trust and stop
  responding. The investment is wasted.

- **Physics-informed ML crossing usability threshold.** PINNs and
  neural operators now achieve 10³–10⁵x speedup vs. traditional FEM
  solvers, with mature PyTorch implementations. But even simpler
  physics models (lumped-parameter ODEs) can eliminate false alarms
  when properly applied — the key is comparing measured behavior
  against physics-predicted behavior under known conditions.

**Why now?**
- PINNs/neural operators matured 2025–2026 (capability threshold).
- NIS2 critical infrastructure requirements (Oct 2026 in Italy)
  increase pressure on energy and industrial operators to demonstrate
  proactive risk management — monitoring is a demonstrable measure.
- OEM servitization pressure: equipment makers are shifting from
  selling machines to selling uptime contracts, where false alarms
  directly erode margin.
- CRA + Data Act require connected-product manufacturers (including
  OEMs selling instrumented equipment) to build data infrastructure
  anyway — the monitoring layer is an incremental addition.

---

## 2. Combinatorial pairing (from Stage 3)

**Domain insight:** Most PdM platforms are purely data-driven — they
learn correlations from historical data but don't understand physics.
When a gearbox bearing runs hot, a data-driven system flags an
anomaly. A physics-informed system knows whether the temperature is
*expected* given current power output, rotor speed, and ambient
conditions. The anomaly is the delta between actual and predicted-
under-known-conditions — a fundamentally more reliable signal.

The critical positioning is: **the system knows what your machine
should be doing.** A hot bearing under high load on a hot day is
normal. A hot bearing under low load on a cold day is not. Data-
driven systems cannot make this distinction without seeing that exact
combination in training data. Physics models make it by construction.

**Technical combination:**
- **Lumped-parameter physics models** (ODEs, thermal networks) for
  v1; **PINNs / neural operators** (PyTorch) for v2 — physics-
  constrained prediction of expected behavior
- **Conformal prediction** (MAPIE) — statistically guaranteed alarm
  thresholds on the physics residual. "Under normal conditions, the
  residual stays inside this band 95% of the time."
- **Signal processing** (Kalman filtering, wavelet scattering via
  Kymatio) — sensor data cleaning and state estimation
- **Edge deployment** (Jetson) — on-site, real-time, privacy-
  preserving, offline-capable inference
- **Django/FastAPI + HTMX** — dashboard showing expected vs. actual,
  with alarm history and conformal coverage status

**What can't a pure software person build here?** Formulating the
physics model. Each application (gearbox thermal, compressor
thermodynamics, heat-exchanger fouling) requires encoding the
governing equations from first principles. A pure software PdM
vendor would need to hire a physicist — and each new equipment type
needs its own model. This is the moat.

---

## 3. Differentiation and moat (from Stage 4)

**Competitive alternatives:**
- **Data-driven PdM platforms (the "burned" market):** Augury
  (vibration-focused, €100k+/yr), GE Predix, Siemens MindSphere,
  Tractian (hardware-integrated), Factory AI (14-day deployment,
  brownfield). All primarily data-driven. All produce false alarms
  under distribution shift.
- **NBM (Normal Behavior Modeling) in wind energy:** SCADA-based
  regression models predicting bearing temperature from operational
  parameters. Established practice, but typically data-driven (neural
  net or random forest), not physics-informed. Closest existing
  approach to what we're building.
- **Enterprise PdM consultancies:** Expensive, human-delivered,
  not scalable, not available at 2am.
- **Manual process / run-to-failure:** Scheduled maintenance
  calendars, reactive repair. The default for SMEs.

**Your structural advantage (product-embedded moats):**

1. **Physics moat is deep and application-specific.** Each equipment
   type requires its own governing equations. This is hard to copy
   without physics expertise. Once built for a machine family, the
   model covers the OEM's entire installed base.

2. **Data flywheel on the residual, not the raw signal.** As more
   machines of the same type run through the system, the physics
   model calibrates and the conformal bounds tighten. The residual
   distribution across the fleet becomes a unique asset — "normal
   residual" for this machine family under these conditions.

3. **Conformal prediction as regulatory armor.** The alarm comes with
   a stated coverage guarantee. No other PdM product can say "this
   alarm threshold provides 95% coverage." For NIS2 risk management
   documentation, this is a concrete artifact. AI Act Art. 9 (risk
   management for high-risk systems) requires performance bounds —
   CP provides them by construction.

4. **Edge deployment.** On-site inference means data stays local.
   Privacy (NIS2, GDPR, customer contracts), latency (real-time
   alarm), reliability (works offline in remote wind farms or
   factories with poor connectivity).

**Why can't incumbents just add this?**
- Data-driven PdM vendors (Augury, Tractian, Factory AI) would need
  to hire physicists and re-architect their core — adding physics
  to a black-box ML platform is an architectural change, not a
  feature.
- Enterprise platforms (GE, Siemens) have physics capability in their
  R&D labs but their pricing and delivery models don't serve the OEM
  monitoring-as-a-service use case.
- NBM practitioners in wind energy use data-driven models because
  "it's always been done that way" — physics-informed approaches
  are published in research but rarely productized.

**Positioning (Dunford):**
- **Competitive context:** Not "a better PdM platform" (too broad,
  invites comparison with Augury/Siemens). Position as "the
  monitoring system that knows what your machine should be doing" —
  the evaluating question becomes "does it understand the physics?"
  not "does it have more features?"
- **Competitive alternative:** The buyer's real alternative is their
  existing PdM tool that cries wolf, plus the human judgment they've
  learned to apply on top. You replace the human judgment with physics.

---

## 4. Revenue line of sight (from Stage 5)

**Buyer:** Two paths, in order of structural soundness.

**Path A (preferred): Equipment OEM — Head of Service / Aftermarket
Director / VP Product.**
- OEMs selling compressors, gearboxes, heat exchangers, turbines
  with servitization/uptime contracts.
- They have the physics knowledge to validate the model (they designed
  the machine), the sensor data (they instrument new machines), and
  the financial incentive (false alarms erode their service margin).
- One OEM deal = one physics model covering their machine family
  across potentially hundreds of installations.

**Path B (secondary): PdM-burned end users — Maintenance Manager /
Reliability Engineer at a plant that has an existing PdM system
generating too many false alarms.**
- Harder to find at scale, but high push (existing pain), budget
  precedent (already paying for PdM), and a concrete evaluation
  criterion.

**Budget line:**
- OEM: Service / aftermarket P&L (existing, large, recurring).
- End user: Operations / maintenance (existing budget, not new spend).
- Both: could also pull from compliance (NIS2 risk management
  requirement for critical infrastructure operators).

**Cost of not buying:**
- OEM: False alarms on uptime contracts cost real money — every
  unnecessary truck roll, every technician dispatched to a healthy
  machine, every hour of downtime caused by alarm fatigue (ignoring
  a real alarm because the last ten were false).
- End user: Same, plus unplanned downtime (€10k–€100k per incident
  for manufacturing SMEs), plus NIS2 risk management documentation
  gaps.

**Can you reach 10 of these buyers?**
- OEMs: Confindustria (Italian industrial associations), trade shows
  (SPS Nuremberg, Hannover Messe), the teardown content engine on
  LinkedIn. OEM product/service managers are active on LinkedIn and
  responsive to peer-frame technical content.
- PdM-burned end users: the demo + teardown post is the magnet.
  People who comment "this is exactly our problem" are the interview
  pipeline.

**Does the product deliver value without you?**
- Yes. The monitoring pipeline runs automatically. The physics model
  is pre-built per machine family. The alarm + conformal threshold
  is self-explanatory to a maintenance technician. The edge box runs
  on-site without cloud dependency.

**Pricing model intuition:**
- OEM: Per-machine-family license (one-time setup for the physics
  model) + SaaS subscription per monitored asset or per site.
  €500–€2,000/month per site for the monitoring layer. Physics model
  development as a one-time project fee (€10k–€30k per machine
  family).
- End user: SaaS subscription per monitored asset. Edge hardware
  (Jetson module) as one-time cost or rental.
- Both: significantly below enterprise platforms (Augury €100k+/yr).

---

## 5. De-risking plan (from Stage 6)

**Riskiest assumption:** That equipment OEMs and/or PdM-burned end
users perceive false alarm elimination as a problem worth paying to
solve *and* that a physics-informed approach is credibly better than
the data-driven status quo in their eyes.

**Secondary risks:**
- That the lumped-parameter physics model is good enough for real-
  world anomaly detection (too simplistic = noisy residual = no
  improvement over data-driven).
- That OEMs are willing to share the physics knowledge and sensor
  data needed to build per-machine-family models (IP sensitivity).
- That the OEM sales cycle is not prohibitively long for a solo
  founder (enterprise pilots that stall in innovation departments).

**Next 3 actions to test it:**

1. **Public data demo** — build and publish a side-by-side comparison
   of data-driven vs. physics-informed anomaly detection on wind
   turbine SCADA data. Key metric: false alarm episodes per turbine-
   month, especially under seasonal distribution shift. Conformal
   prediction on the physics residual. Public repo + LinkedIn
   teardown post.
   → See `validation/experiments/physics-nbm-demo.md` for full
   project charter.

2. **5 Mom Test interviews** — two buyer types in parallel:
   - 2–3 OEM product/service managers: "Walk me through the last
     false alarm on a machine under an uptime contract. What did it
     cost you?" / "How do your customers react to alert fatigue?" /
     "Have you considered embedding monitoring into your service
     offering?"
   - 2–3 maintenance/reliability engineers at plants with existing
     PdM: "Walk me through the last time you ignored an alarm and
     it turned out to be real." / "How many alarms per week does
     your PdM system generate? How many are actionable?"

3. **Identify the beachhead machine class** — based on interviews,
   pick the one equipment type where (a) the physics model is well-
   defined and not too complex, (b) the buyer has felt the false-
   alarm pain, and (c) the market size is sufficient. Current
   candidates: industrial compressors, wind turbine gearboxes, heat
   exchangers.

**Kill criteria:**
- Demo shows no meaningful false-alarm reduction vs. data-driven
  baseline on public data (the physics layer adds no value).
- 4+ of 5 interviewees say false alarms are annoying but not worth
  paying to fix (pain exists but willingness to pay doesn't).
- OEM interviews reveal that IP sensitivity makes physics-model
  collaboration infeasible (they won't share the equations or data).
- The beachhead machine class requires >2 months of physics modeling
  investment before a deployable product exists (capability gap
  larger than estimated).

---

## Open questions

- Which specific equipment type should be the beachhead? Wind turbine
  gearboxes (strong public data, established NBM practice, but
  possibly saturated)? Industrial compressors (clear thermodynamics,
  broad market, but needs OEM data)? Heat exchangers (clean physics,
  fouling detection is high-value, but hardware testbed may be needed
  for convincing demo)?
- Is the OEM channel hypothesis valid, or will OEM sales cycles be
  prohibitively long? The Mom Test interviews will test this.
- Can the same positioning ("monitoring that knows what your machine
  should be doing") work for both thermal and non-thermal failure
  modes (vibration, electrical), or does each physics domain need
  different positioning?
- What is the relationship between this product and the CRA silent
  watchdog (Idea A)? Same buyer segment (connected-product OEMs),
  complementary value propositions (cybersecurity + operational
  monitoring). Could they share infrastructure or be bundled?
- Should the v1 product include the edge box, or start as cloud-only
  with edge as a premium tier? Cloud-only is faster to ship; edge
  is the differentiator.

---

## Technology stack (from tech catalog analysis)

| Layer | v1 (demo → MVP) | v2 (scale) |
|-------|-----------------|------------|
| Physics model | Lumped-parameter ODE (SciPy) | PINNs / neural operators (PyTorch, neuraloperator) |
| Data-driven complement | Isolation forest, autoencoder | EfficientAD or PatchCore (anomalib) for visual; Mamba for long sequences |
| Uncertainty | MAPIE conformal prediction | Mondrian CP for group-conditional coverage per machine type |
| Signal preprocessing | Pandas, SciPy signal | Kymatio wavelet scattering, STUMPY matrix profile |
| Feature modeling | scikit-learn, LightGBM | TabPFN v2.5 for small-data tabular |
| Edge inference | — | PyTorch → ONNX → TensorRT on Jetson Orin |
| Backend | FastAPI + PostgreSQL | Django + PostgreSQL + HTMX dashboard |
| LLM integration | — | Structured-output (Outlines + Qwen2.5-7B) for alarm narrative generation |

---

## References

- `validation/experiments/physics-nbm-demo.md` — demo project charter
- `references/PINN.md` — technical overview and arXiv papers
- `knowledge/tech-catalog-npd-analysis.md` — full technology catalog
- Tautz-Weinert & Watson (2017), NBM review for wind turbines
- Moesta, *Demand-Side Sales 101* — four forces analysis of the
  160-email failure
- Dunford, *Obviously Awesome* — positioning framework
- Fitzpatrick, *The Mom Test* — interview methodology
