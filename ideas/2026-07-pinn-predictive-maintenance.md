# Idea: Physics-informed predictive maintenance for SME manufacturers

> Created: 2026-07-28 | Status: `raw`
> Origin: opportunity-log entries #5, #8

---

## 1. Trigger (from Stage 2)

**What changed?** Two converging forces: (1) The predictive maintenance
industry has spent billions on sensor infrastructure and data collection,
but the gap between diagnostic insight and maintenance action remains
wide — most platforms alert but don't help decide. (2) Physics-Informed
Neural Networks (PINNs) have crossed a usability threshold, achieving
10³–10⁵x speedup vs. traditional FEM solvers, with mature PyTorch
implementations. This makes real-time physics simulation on edge hardware
feasible for the first time.

**Why now?** PINNs capability threshold crossed 2025-2026. NIS2 critical
infrastructure requirements (Oct 2026 in Italy) increase pressure on
energy and industrial operators to demonstrate proactive risk management.
Industrial decarbonization mandates drive demand for asset optimization
(extending equipment life, reducing unnecessary maintenance). Enterprise
PdM market is crowded but SME segment remains underserved.

---

## 2. Combinatorial pairing (from Stage 3)

**Domain insight:** Most PdM platforms are purely data-driven — they learn
correlations from historical data but don't understand physics. When a
transformer runs hot, a data-driven system flags an anomaly. A physics-
informed system knows whether the temperature is expected given current
load and ambient conditions. The "anomaly" is the delta between actual
and predicted-under-known-conditions — a fundamentally more reliable
signal.

**Technical combination:** PINNs via PyTorch (physics-constrained neural
networks for digital twin modeling) + signal processing/Kalman filtering
(sensor data cleaning and state estimation) + edge deployment on Jetson
(on-site, real-time, privacy-preserving inference) + Django/FastAPI
(dashboard and alert management platform).

**What can't a pure software person build here?** Formulating the physics
loss function. A PINN requires encoding the governing equations (heat
transfer, vibration dynamics, fluid flow) as constraints in the neural
network training. This is applied physics, not ML engineering. Pure
software PdM vendors use black-box anomaly detection. The physics-
informed approach produces fewer false positives, works with less
historical data (the physics provides the prior), and generalizes to
new operating conditions — exactly the properties that make it valuable.

---

## 3. Differentiation and moat (from Stage 4)

**Competitive alternatives:**
- **Enterprise PdM platforms:** Augury (vibration-focused, €100k+/yr),
  GE Predix (proprietary ecosystem), Siemens MindSphere, Tractian
  (hardware-integrated) — all target large enterprises, all primarily
  data-driven
- **Mid-market CMMS with PdM bolted on:** Fiix (Rockwell), UpKeep —
  basic IoT hooks, not physics-informed
- **Factory AI:** Sensor-agnostic, 14-day deployment, brownfield focus
  — closest to SME positioning but still data-driven, no physics
- **Manual process:** Scheduled maintenance calendars, reactive repair

**Your structural advantage:**
- Physics moat embedded in product: PINNs require domain-specific
  governing equations. Each industrial application (wind turbine gearbox,
  industrial compressor, heat exchanger) needs its own physics model.
  This is hard to copy without physics expertise.
- Data efficiency: physics priors mean the system works with less
  historical data — critical for SMEs who don't have 5 years of sensor
  archives.
- Edge deployment: on-site inference means data stays local (privacy,
  latency, reliability in poor-connectivity environments). Privacy
  matters increasingly under NIS2 and GDPR.
- Data flywheel: as more equipment of the same type runs through the
  system, the physics models calibrate and improve.

**Why can't incumbents just add this?** PINNs require physics expertise
in the core team. Enterprise PdM vendors are optimized for large
deployments with dedicated data science teams on the customer side.
Their pricing and delivery models don't work for SMEs. Adding physics
to a black-box ML platform is an architectural change, not a feature.

---

## 4. Revenue line of sight (from Stage 5)

**Buyer:** Maintenance Manager, Plant Manager, or Operations Director at
SME manufacturers (50-500 employees). In energy: Asset Manager at wind
farm operators or grid equipment owners.

**Budget line:** Operations / maintenance (existing budget line, not new
spend). For energy sector: could also be compliance (NIS2 risk management
requirement).

**Cost of not buying:** Unplanned downtime (€10k-€100k per incident for
SME manufacturers). Unnecessary preventive maintenance on healthy
equipment. For energy: NIS2 Art. 21 requires risk management measures
for critical infrastructure — PdM is a demonstrable measure.

**Can you reach 10 of these buyers?** Need to build. Entry points:
Italian manufacturing associations (Confindustria), energy sector
conferences, Hackaday/industrial IoT communities. Could leverage medtech
manufacturing contacts — medical device manufacturers use the same types
of industrial equipment.

**Pricing model intuition:** SaaS subscription per monitored asset or
per site. Edge hardware (Jetson module) as one-time cost or rental. Lower
price point than enterprise platforms (€500-€2,000/month per site vs.
€100k+/yr for Augury).

---

## 5. De-risking plan (from Stage 6)

**Riskiest assumption:** That SME manufacturers will pay for physics-
informed PdM when cheaper (but less accurate) data-driven alternatives
exist. The risk is that SMEs don't value the accuracy improvement
enough to justify the price premium over basic anomaly detection, or
that they lack the sensors to provide input data.

**Next 3 actions to test it:**
1. **Learn PINNs to deployable depth:** Complete a hands-on PINN project
   using PyTorch — e.g., model heat transfer in a simple industrial
   system using the tutorial papers in references/PINN.md. Timeline:
   2-4 weeks.
2. **Interview 5 maintenance/operations managers at SME manufacturers.**
   Mom Test: "Walk me through the last unplanned downtime you had."
   "What sensors do you have on your critical equipment today?" "Have
   you looked at predictive maintenance tools? What happened?"
3. **Identify one specific industrial application** where physics-
   informed approach clearly outperforms data-driven (e.g., wind turbine
   gearbox monitoring, industrial compressor health) and build a
   minimal proof-of-concept with synthetic data.

**Kill criteria:**
- Interviews reveal SME manufacturers don't have sensors on critical
  equipment and won't invest in installing them
- The PINN learning investment exceeds 2 months to reach deployable
  depth (indicating the capability gap is larger than estimated)
- Buyers consistently say existing data-driven PdM tools are "good
  enough" and the physics accuracy improvement doesn't justify
  switching

---

## Open questions

- Which specific industrial application should be the beachhead? Wind
  turbine gearboxes? Industrial compressors? Heat exchangers? The
  physics model must be application-specific, so the choice matters.
- Is the SME manufacturer segment large enough and willing to pay?
- Could this start as an edge-only product (Jetson box that connects to
  existing sensors, runs PINN inference locally, sends alerts) to reduce
  deployment friction?
- What is the relationship to NIS2? Could "physics-informed asset
  monitoring" be positioned as a NIS2 risk management measure for
  critical infrastructure operators?

## References

- references/PINN.md — technical overview and arXiv papers
- Factory AI blog: "Predictive Maintenance in Energy: Beyond Detection
  to Execution" (action gap analysis)
- NVIDIA Modulus framework for PINNs
