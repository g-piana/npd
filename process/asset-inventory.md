# Asset Inventory

> Last updated: 2026-07-27
> Purpose: Capability menu for Stage 3 forced-pairing. Each entry should
> be concrete enough to pair with an opportunity-log trigger and ask
> "what product exists at this intersection?"
>
> **Learning stance:** A CERN physics PhD provides the analytical
> foundations and the mindset to learn new technical areas fast. Items in
> "ready to invest" are not speculative — they are extensions of existing
> skills that can reach deployable depth in weeks-to-months given a
> concrete product opportunity. A good opportunity justifies the
> learning investment; don't filter ideas out just because a capability
> isn't in the "ship today" column yet.

---

## 1. Transferable analytical foundations

These are domain-agnostic and port to any physics-heavy or data-heavy
industry. They are the reason you can learn new technical areas faster
than a pure software engineer.

| Method | Deployment depth | Example application |
|--------|-----------------|---------------------|
| Statistical inference | Deep — used professionally | Parameter estimation, hypothesis testing on noisy data |
| Signal processing (filtering, Kalman) | Deep — used professionally | Sensor data cleaning, state estimation, tracking |
| Monte Carlo methods | Deep — used professionally | Simulation, uncertainty quantification |
| ML pipeline engineering | Deep — used professionally | End-to-end training, evaluation, deployment pipelines |
| Applied math / physics modeling | Deep — research + professional | Building mathematical models of physical systems |
| PDE solvers | Foundations — coursework | Extendable to PINNs (see adjacent capabilities) |
| Bayesian methods | Foundations — coursework | Extendable to probabilistic ML |
| Optimization (convex, constrained) | Foundations — coursework | Extendable to operational problems |
| FFT / spectral analysis | Foundations — coursework | Extendable to vibration, acoustic, power-quality analysis |
| Stochastic modeling | Foundations — coursework | Extendable to reliability, predictive maintenance |
| Control theory | Foundations — coursework | Extendable to robotics, process control |

---

## 2. Deployable capabilities — can ship in weeks

These are proven in production or near-production. Use these as the
"fast" column in forced-pairing — ideas combining these with a strong
trigger can move to validation quickly.

### Computer vision & deep learning (strongest area)

- **CNN training from scratch** — ResNet, U-Net architectures; image
  classification, segmentation, object detection
- **Fine-tuning pretrained models** — transfer learning on domain-specific
  image data
- **Visual search via embeddings** — vector databases for similarity search
- **Image-based metrology** — measurement and dimensional analysis from images
- **Anomaly detection** — on images and structured data
- **Most complex deployed system:** Three-stage CNN pipeline for real-time
  detection on x-ray video during minimally invasive surgery: stage 1
  detected device in noisy fluoroscopy, stage 2 cropped region of
  interest, stage 3 classified device orientation. Real-time inference
  on local server.

### NLP

- **SpaCy pipelines** — entity extraction, text classification
- **Production NLP systems** — deployed and maintained

### Web & data infrastructure

- **Django + DRF / FastAPI** — full-stack web applications, REST APIs
- **HTMX** — reactive frontends without JS framework overhead
- **PostgreSQL** — relational data modeling, queries, time-series
- **Python ecosystem** — NumPy, Pandas, SciPy, Matplotlib, scikit-learn

---

## 3. Adjacent capabilities — ready to invest if opportunity justifies

These are learnable in reasonable time given existing foundations.
They extend the search space for Stage 3 ideation. **Do not filter out
ideas that require these — the question is whether the opportunity is
strong enough to justify the investment.**

| Capability | Learning distance | What makes it reachable | Potential product applications |
|-----------|------------------|------------------------|-------------------------------|
| **PINNs** (Physics-Informed Neural Networks) | Short — PyTorch + physics background = natural fit | Already use PyTorch; PINNs add physics constraints to loss functions you already understand | Digital twins, predictive maintenance, structural health monitoring, flow simulation, anomaly detection with physics priors |
| **Inverse problems** | Short — mathematical foundations from PhD | Core applied-math technique; extends statistical inference skills | Parameter estimation from sensor data, non-destructive testing, subsurface characterization, source localization |
| **LLM/SLM, LoRA fine-tuning** | Medium — new paradigm but Python-native | Hugging Face ecosystem; fine-tuning workflow similar to CNN training | Domain-specific document processing, compliance automation, structured extraction from unstructured text |
| **Agentic architectures** | Medium — emerging patterns, fast-moving | Builds on LLM capability; frameworks maturing (LangGraph, CrewAI) | Automated workflows, multi-step analysis tools, monitoring agents |
| **Edge deployment** (Jetson inference, TensorRT, quantization) | Medium — have experimented, need production depth | Already ran inference on Jetson; need optimization and hardening | On-site real-time analysis, privacy-preserving inference, remote/harsh-environment monitoring |
| **Edge data acquisition** (sensors, Arduino, I/O) | Medium — have experimented with Arduino | Electronics foundations from physics; interfaces are well-documented | IoT sensor platforms, environmental monitoring, vibration/acoustic sensing |
| **Graph modeling on PostgreSQL** | Short — already deep in PostgreSQL | pgRouting, Apache AGE, recursive CTEs; SQL-native | Supply chain traceability, network analysis, dependency mapping (e.g., SBOM) |
| **Time-series ML** | Short — have experimented, foundations in place | Signal processing background transfers directly | Predictive maintenance, energy forecasting, process monitoring |

---

## 4. Domain familiarity

Familiarity accelerates *problem-finding* and *sales conversations*,
not product delivery. The product must work without founder domain
expertise in the room.

| Domain | Depth | Network access | Notes |
|--------|-------|---------------|-------|
| Medtech / medical devices | Deep (15yr) | Strong | Actively diversifying away; use for problem-finding, not as product dependency |
| Defense / aerospace | Moderate | Some contacts | Valid only if product needs no regulatory certification |
| Energy / renewables | Learning | Building | High regulatory activity (EU energy directives, grid codes) |
| Industrial IoT | Moderate (via edge work) | Limited | Natural fit for edge + CV + CRA |
| Drones / robotics | Learning | Building | Young industry, low domain barriers, high regulation impact |
| Security / surveillance | Learning | Limited | AI Act restrictions create tooling demand |

---

## How to use this in Stage 3

When running the forced-pairing matrix:
- **Rows** = opportunity-log entries (demand triggers)
- **Columns** = entries from sections 1–3 above (all capabilities, both
  deployable and adjacent)
- For each promising intersection, ask:
  1. "What product exists here that a pure software person couldn't build?"
  2. "Can this product deliver value without me in the room?"
  3. "Is the opportunity strong enough to justify learning an adjacent
     capability?"
- If a pairing requires an adjacent capability AND the opportunity is
  strong (urgent deadline, clear buyer, mandated budget), that's a
  *better* signal than a pairing using only existing skills on a weak
  opportunity.
