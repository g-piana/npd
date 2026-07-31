# Technology Catalog — NPD Analysis

> Generated: 2026-07-30
> Purpose: For each technique from the research retrospective, assess
> product potential through the Stage 3–5 lens: concrete use cases,
> business value an executive understands, moat sources, and
> combinatorial pairings that increase differentiation.
>
> **Reading guide:** Technologies are ordered by estimated product
> readiness (most deployable first). Each section is self-contained.
> "Combine with" suggestions reference other entries in this document
> or capabilities from `asset-inventory.md`.

---

## 1. Memory-Bank Image Anomaly Detection (PatchCore / EfficientAD / anomalib)

### What it is

A family of methods that learn what "normal" looks like from a few
dozen good images, then flag anything that deviates — without ever
needing examples of defects. EfficientAD runs at 2 ms per image.
anomalib (Intel) packages these as a pip-installable library with
export to edge hardware.

### Use cases

1. **Visual quality inspection for SME manufacturers.** A food
   packaging company runs 50,000 units/day through a line. Today a
   human inspector catches ~70% of seal defects. An EfficientAD model
   trained on 30 photos of good seals, running on a camera + Jetson
   box on the line, catches 99%+ and generates a timestamped defect
   log — audit-ready for food safety compliance.

2. **Incoming parts inspection for contract manufacturers.** An
   electronics assembler receives reels of components from suppliers.
   Visual inspection of solder pads, connector pins, or PCB surface
   quality today is either skipped or sampled. A camera station with
   anomaly detection provides 100% incoming inspection at the cost of
   one camera, generating reject evidence for supplier claims.

3. **Post-production documentation for regulated products.** A medical
   device or aerospace component manufacturer needs to demonstrate
   that every unit leaving the line was inspected. The system produces
   per-unit image + pass/fail + anomaly heatmap — a compliance artifact
   that replaces a paper checklist.

### Business value (executive summary)

The buyer replaces a manual inspection step (unreliable, expensive,
unscalable) with an automated system that needs no defect samples to
set up. Setup takes hours, not months, because the model only needs
normal images. The cost of a missed defect — a product recall, a
customer complaint, a failed audit — is typically 100–1000x the cost
of the system.

### Differentiation and moat

- **Physics of the problem:** Knowing what makes an anomaly meaningful
  in a specific manufacturing context (is this scratch cosmetic or
  structural?) requires domain understanding of the product, not just
  ML engineering. A generic "AI inspection" vendor treats every pixel
  equally.
- **Data flywheel:** Every flagged anomaly the operator confirms or
  overrides improves the model's decision boundary. Over months, the
  system learns the specific failure modes of that line, that supplier,
  that material batch.
- **Edge deployment as moat:** Running on-site (Jetson, OpenVINO on
  Intel NUC) means images never leave the factory — a real selling
  point in defense, aerospace, and anywhere IP-sensitive parts are
  made. Cloud-dependent competitors cannot offer this.

### Combine with

- **Conformal prediction (§3)** — wraps the anomaly score in a
  statistically guaranteed threshold: "with 95% probability, anything
  above this score is a true defect." This is the difference between
  "the AI flagged it" and "the system provides 95% coverage guarantee"
  — the second sentence is what a quality auditor wants to hear.
- **Wavelet scattering (§8)** — for products where the defect is in
  texture or surface pattern (machined surfaces, woven materials),
  scattering features fed into the anomaly detector outperform raw
  pixels because they're invariant to irrelevant shifts.
- **Edge inference pipeline (§6)** — PyTorch → ONNX → TensorRT
  quantized to INT8. EfficientAD at INT8 on a Jetson Orin runs well
  under 1 ms — fast enough for high-speed lines.

---

## 2. Small-Data Tabular ML (TabPFN v2.5)

### What it is

A pre-trained transformer that does classification and regression on
tabular data (spreadsheets, sensor logs, structured records) in
seconds — no hyperparameter tuning, no feature engineering. In a
Nature-published comparison, it beat a 4-hour-tuned ensemble of
XGBoost/LightGBM/CatBoost on datasets up to 10,000 rows.

### Use cases

1. **Predictive maintenance risk scoring for SMEs.** A plant manager
   has 18 months of sensor logs from 12 compressors — vibration,
   temperature, pressure, run hours — and a spreadsheet of the 9
   failures that occurred. Too little data for deep learning. TabPFN
   trains a failure-risk model in seconds and outputs a probability
   per machine per week. Paired with conformal prediction (§3), the
   output is "Machine 7 has a >80% chance of failure in the next 2
   weeks, with 90% statistical confidence."

2. **Quality prediction from process parameters.** An injection
   molding shop tracks 20 process variables (temperature, pressure,
   cycle time, material batch) and measures 3 quality outcomes per
   part. With 500 historical records, TabPFN builds a model that
   predicts quality from process settings — enabling real-time
   adjustment before defects occur.

3. **CRA/NIS2 risk triage.** A compliance platform ingests structured
   attributes of a vulnerability (CVSS score, component criticality,
   network exposure, active exploitation signals) and predicts
   likelihood that this requires a 24-hour ENISA report. The training
   set is small (the regulation is new, few precedents exist), making
   TabPFN's small-data strength directly relevant.

### Business value (executive summary)

Most SMEs have data in spreadsheets — enough to spot patterns but not
enough for traditional AI. TabPFN turns 200–10,000 rows of structured
data into a working predictive model in under 3 seconds, with no data
scientist required to tune it. The output is a probability score that
tells a manager where to focus attention. The alternative is either
ignoring the data or paying €50–100k for a consulting engagement to
build a custom model.

### Differentiation and moat

- **The "not enough data" objection killer:** SME buyers' #1 objection
  to predictive analytics is "we don't have enough data." TabPFN
  works with what they have. This is a positioning advantage, not
  just a technical one.
- **Speed as UX:** 2.8 seconds to a working model means the sales demo
  *is* the deployment. Upload your CSV, get predictions. No "we'll
  get back to you in 6 weeks with a trained model."
- **Distillation to edge:** TabPFN v2.5 can distill its predictions
  into a compact MLP or tree ensemble that runs on a Jetson or
  even a microcontroller — the cloud model trains, the edge model
  deploys.

### Combine with

- **Conformal prediction (§3)** — essential pairing. A point
  prediction from TabPFN is useful; a prediction interval with
  coverage guarantee is sellable to regulated buyers.
- **STUMPY matrix profile (§4)** — extract motif and discord features
  from sensor time-series, then feed them as tabular features into
  TabPFN. This bridges time-series complexity with TabPFN's
  tabular strength.
- **The CRA silent watchdog (idea card)** — the vulnerability triage
  scoring model is a natural TabPFN application inside the product.

---

## 3. Conformal Prediction (MAPIE)

### What it is

A statistical wrapper that goes around any trained model — neural
network, gradient boosting, TabPFN, anything — and produces prediction
intervals (for numbers) or prediction sets (for categories) with a
mathematically guaranteed coverage rate. If you set 95% coverage, the
true answer will fall inside the interval at least 95% of the time.
No retraining. No architectural change. Just a calibration step on
held-out data.

### Use cases

1. **Safety-critical inspection with auditable confidence.** A visual
   inspection system (§1) flags a part as defective. The conformal
   wrapper adds: "this classification has 97% coverage — in a
   calibration set of 500 parts, the true label was inside the
   prediction set 97% of the time." This is the evidence an ISO 13485
   or AS9100 auditor needs to accept automated inspection.

2. **Predictive maintenance with decision thresholds.** A predictive
   model says compressor failure probability is 73%. The conformal
   interval says: "the true probability is between 65% and 81% at
   90% confidence." The maintenance manager now knows: even in the
   best case, the risk is above my 60% intervention threshold. Act.

3. **CRA vulnerability severity scoring.** A triage model classifies a
   vulnerability as "likely reportable." The conformal prediction set
   says: "at 95% coverage, the classification is {reportable}." If
   the set were {reportable, review-needed}, the system escalates to
   a human. The uncertainty is made visible and actionable — exactly
   what a regulator wants to see.

### Business value (executive summary)

In regulated industries, a prediction alone is not enough — you need
to show how confident you are and what could go wrong. Conformal
prediction provides a certificate of reliability that no other AI
technique offers without complex Bayesian modeling. It turns "the AI
said so" into "the system provides a 95% statistical guarantee." For
a medical device manufacturer, an energy company, or a defense
contractor, that sentence is the difference between using AI and not.

### Differentiation and moat

- **Regulatory fit as positioning:** No other off-the-shelf technique
  provides distribution-free, finite-sample coverage guarantees. This
  is a structural advantage in any market where a buyer needs to
  justify an AI decision to a regulator or auditor.
- **Model-agnostic = platform play:** Conformal prediction wraps any
  model. A product that bundles CP into its prediction pipeline can
  say "every output comes with a statistical guarantee" regardless of
  the underlying algorithm. Competitors who don't do this are
  structurally weaker in regulated markets.
- **Low moat risk:** CP itself is open-source (MAPIE). The moat is in
  the *application* — knowing which coverage targets matter for which
  regulatory context (MDR, AI Act, CRA) and integrating CP into the
  product's UX so the operator sees confidence, not statistics.

### Combine with

- **Every other technique in this document.** CP is a universal wrapper.
  It should be a design principle, not an add-on.
- **AI Act compliance (regulation tracker)** — Art. 9 requires risk
  management for high-risk AI, including performance bounds. CP
  provides exactly this. A product that ships with CP-based
  performance certificates is pre-aligned with AI Act requirements.

---

## 4. Matrix Profile for Time-Series (STUMPY)

### What it is

An algorithm that, given a sensor stream, finds the most unusual
segments (anomalies) and the most repeated patterns (motifs) — with
no training data, no model, no hyperparameters beyond window length.
It compares every subsequence to every other subsequence and reports
which ones have no match. Interpretable by construction: "this
10-minute segment of vibration data is unlike anything else in the
last 6 months."

### Use cases

1. **First-line anomaly detection for predictive maintenance.** A wind
   turbine operator has 2 years of vibration data from gearbox sensors
   but no labeled failures — the turbines haven't failed yet (or
   failures weren't recorded systematically). STUMPY finds the
   segments that are most unlike the rest. The maintenance engineer
   reviews them and says "yes, that's when we had the bearing issue"
   or "no, that's a normal seasonal pattern." The system learns
   nothing — but it surfaces the needles in the haystack.

2. **Regime detection in manufacturing processes.** A chemical batch
   process has 50 variables logged every second. STUMPY's semantic
   segmentation identifies when the process shifts between regimes
   (startup, steady-state, upset, shutdown) without being told what
   the regimes are. Operations can then correlate regime transitions
   with quality outcomes.

3. **Baseline monitoring for CRA/NIS2 compliance.** Network traffic
   from an IoT device fleet is logged as a time-series. STUMPY
   identifies communication patterns that are unlike the device's
   normal behavior — a potential indicator of exploitation. No ML
   model needed, no training data, no labeled attacks.

### Business value (executive summary)

Most SMEs have sensor data they're not using because they lack the ML
expertise to build models and the labeled failures to train them.
STUMPY requires neither. It works on raw data, finds the unusual
patterns, and presents them for human review. The result is not
"predictive maintenance" (that comes later) — it's "you can now see
what your data is telling you for the first time, in minutes, at zero
cost." For a company that has never done any analytics on their sensor
data, this is the entry point.

### Differentiation and moat

- **No-training-data positioning:** "Works on day one, with the data
  you already have, without labeling a single example." This is the
  exact message that resonates with SMEs who've been told by every AI
  vendor that they need 5 years of clean, labeled data.
- **Interpretability:** The output is "this segment is unusual" with
  a distance score. An engineer can look at the raw signal and
  confirm or reject. No black box. This matters in safety-critical
  and regulated contexts.
- **Low moat risk:** STUMPY itself is open-source. The moat is in
  wrapping it into a product that connects to industrial data sources,
  presents results in domain-specific terms (not statistician terms),
  and feeds confirmations into a learning loop.

### Combine with

- **TabPFN (§2)** — once the operator has confirmed enough STUMPY
  anomalies and motifs, extract features from the matrix profile and
  feed them into TabPFN for a supervised risk model. STUMPY is the
  label generator; TabPFN is the predictor.
- **Wavelet scattering (§8)** — for vibration data, scattering
  features compress the oscillatory structure before matrix profile
  computation — faster, more robust to noise.
- **Conformal prediction (§3)** — once you move from STUMPY (purely
  descriptive) to a trained model, wrap it in CP.
- **Physics-informed models (§5)** — STUMPY finds *that* something
  unusual happened; a PINN or neural operator model explains *why*
  by checking whether the behavior is consistent with the governing
  physics.

---

## 5. Neural Operators (FNO / DeepONet) and Differentiable Filtering

### What it is

**Neural operators** learn to map one function to another — for
example, "given these boundary conditions and material properties,
what does the temperature field look like?" — trained once, then
applied to new inputs instantly. Unlike PINNs, which solve one
specific case per training run, an operator generalizes across
geometries, materials, and conditions. 1,000–100,000x faster than
traditional FEM solvers.

**Differentiable Kalman/particle filters** combine classical
estimation theory (Kalman filters) with neural networks: the physics
model is known, but the noise characteristics and hard-to-model
dynamics are learned from data. The result is a state estimator that
respects physics but adapts to reality.

### Use cases

1. **Real-time digital twins for predictive maintenance.** A neural
   operator trained on a family of heat exchangers predicts the
   temperature field under any operating condition in milliseconds.
   When a sensor reads 15°C above the predicted field, the system
   knows it's not just "hot" — it's "hotter than physics says it
   should be given current load and ambient conditions." This
   eliminates false alarms from normal operating variation — the core
   accuracy advantage of physics-informed PdM over data-driven PdM.

2. **Inverse parameter estimation for asset health.** Given sparse
   sensor readings on a bridge or a pipeline, a neural operator +
   inverse solver estimates the underlying material properties
   (stiffness, corrosion level, wall thickness). Changes in these
   inferred parameters over time indicate degradation — months before
   a sensor alarm triggers.

3. **Drone/robot state estimation in GPS-denied environments.** A
   differentiable Kalman filter fuses IMU, barometer, and visual
   odometry data. The physics of motion is encoded in the filter; the
   measurement noise and drift characteristics are learned from data.
   The result is more robust localization than either pure physics
   (which doesn't handle real sensor noise) or pure ML (which
   doesn't respect the laws of motion).

### Business value (executive summary)

Traditional physics simulations (FEM, CFD) take hours and require
expert setup. Neural operators deliver the same answers in
milliseconds, on commodity hardware. This unlocks real-time monitoring
and control applications that were previously impossible: a factory
operator can see a live physics model of their equipment, not just
raw sensor readings. The "what should be happening" vs. "what is
actually happening" comparison is the most reliable anomaly signal
available — it eliminates the false positives that plague every
data-only system.

### Differentiation and moat

- **Physics moat is deep and application-specific.** Each industrial
  application (heat exchanger, gearbox, turbine blade) requires its
  own governing equations encoded into the operator training. A pure
  software competitor cannot replicate this without hiring a physicist.
  This is the strongest moat in the entire catalog.
- **Data efficiency:** The physics provides the prior. A neural
  operator trained on 1,000 simulated scenarios generalizes to
  conditions never seen. A data-only model needs those conditions to
  have occurred in production — which, for rare failure modes, they
  haven't.
- **Generalization as product scalability:** One trained operator
  covers a family of equipment (all heat exchangers of this type,
  across customers). Each new customer's specific unit is just a new
  input to the same model.

### Combine with

- **Conformal prediction (§3)** — wrap the operator's output in a
  coverage guarantee. "The predicted temperature field is accurate
  to ±2°C with 95% coverage."
- **Edge deployment (§6)** — a trained neural operator is small
  (typical: 1–10 MB) and runs on a Jetson in single-digit
  milliseconds. This enables on-site, real-time, offline-capable
  physics inference.
- **STUMPY (§4)** — use the matrix profile on the *residuals*
  (difference between neural-operator prediction and actual sensor
  readings). Anomalies in the residual are physics-aware anomalies —
  the most reliable signal available.
- **Graph neural networks (§9)** — for systems with many interacting
  components (sensor networks, structural meshes), GNNs provide the
  spatial topology that neural operators use as input structure.

---

## 6. Edge Inference (Quantization / EfficientViT / TensorRT)

### What it is

A pipeline that takes a trained PyTorch model, optimizes it (pruning,
quantization to INT8 or FP16, kernel fusion), and deploys it on
embedded hardware (Jetson Orin, Intel NUC) at 10–100x the speed of
the original model. EfficientViT and FastViT are architectures
designed from the ground up for this pipeline.

### Use cases

1. **On-premise visual inspection that works offline.** A factory in a
   rural area with unreliable internet runs an inspection system on a
   Jetson box connected to a line camera. No cloud dependency, no
   data leaving the premises, no latency. If the internet goes down,
   inspection continues.

2. **Privacy-preserving monitoring for defense/aerospace.** A defense
   contractor needs automated inspection of classified components.
   Images of these components cannot leave the facility. An edge
   system processes everything locally and exports only pass/fail
   statistics.

3. **Remote asset monitoring for energy infrastructure.** A wind farm
   or solar installation runs edge inference on vibration and image
   data from each turbine/panel. Only anomaly alerts and summary
   statistics are sent to the operations center — reducing bandwidth
   costs by 99% compared to streaming raw data.

### Business value (executive summary)

Edge inference means the AI runs where the data is generated — on
the factory floor, at the wind turbine, inside the secure facility.
No cloud costs, no internet dependency, no data privacy risk, no
latency. For the buyer, this translates to: works in bad-connectivity
environments, satisfies data residency requirements (GDPR, NIS2,
customer contracts), and has predictable costs (one-time hardware, no
per-inference cloud fees).

### Differentiation and moat

- **Deployment friction as competitive filter:** Getting a model to
  run efficiently on a Jetson requires optimization expertise
  (quantization calibration, TensorRT layer support, memory profiling)
  that most ML engineers don't have. Competitors who only deploy to
  cloud lose every deal where data can't leave the premises.
- **Hardware-inclusive pricing model:** Sell the Jetson box + software
  as a package (or rent the hardware). This creates a physical
  switching cost — the customer has your box on their line.
- **Dual deployment:** Offer cloud-based model training / fine-tuning
  with edge deployment for inference. The cloud is for updates; the
  edge is for production. This combines recurring SaaS revenue with
  physical lock-in.

### Combine with

- **Anomaly detection (§1)** — EfficientAD + INT8 quantization on
  Jetson Orin is the reference deployment for visual inspection.
- **Neural operators (§5)** — a trained FNO exported to TensorRT runs
  real-time physics simulation on edge hardware.
- **Local LLMs (§10)** — Qwen2.5-3B/7B quantized on a Jetson for
  on-site natural-language interfaces to monitoring systems.
- **TabPFN distillation (§2)** — TabPFN distills to a small MLP that
  runs on a microcontroller. Prediction at the sensor.

---

## 7. Few-Shot Inspection with Foundation Models (DINOv2 / AnomalyDINO)

### What it is

DINOv2 is a vision model trained on 142 million images to produce
general-purpose visual features. AnomalyDINO uses these frozen
features to detect defects from as few as 1–5 reference images of a
good part — no training, no fine-tuning, no GPU time. It compares
the features of a new image to the reference and highlights anything
that doesn't match.

### Use cases

1. **Rapid inspection setup for high-mix, low-volume manufacturing.**
   A contract manufacturer produces 200 different PCB designs per
   year, each in batches of 50–500. Traditional vision systems need
   programming per design. AnomalyDINO needs 5 photos of a good board
   and works immediately. Setup time drops from days to minutes.

2. **Field inspection for maintenance technicians.** A technician
   photographs a component during a routine visit. The app compares
   the photo to reference images of the component in good condition
   and highlights differences: corrosion, cracking, displacement,
   missing parts. No connectivity needed (runs on a phone or tablet
   with a small model).

3. **Incoming quality verification against a golden sample.** A buyer
   receives a shipment of machined parts. They photograph one part
   from the shipment and compare it against 3 reference photos of the
   approved sample. Dimensional accuracy can't be measured this way,
   but surface defects, wrong finishes, and assembly errors can be
   caught instantly.

### Business value (executive summary)

Traditional vision inspection requires extensive programming and
training for each new product. DINOv2-based methods skip this
entirely: show the system what "good" looks like, and it works. For
manufacturers who change products frequently or inspect diverse
components, the setup cost of traditional systems makes them
uneconomical. This approach makes automated inspection viable for the
first time in high-mix environments.

**Critical caveat for the buyer:** A 2025 Bosch/KIT study found that
these foundation models perform well on public benchmarks but fail on
real factory imagery. The feature extraction is strong; the last-mile
adaptation to a specific production environment still matters. Sell it
as "fast start, then refine" — not as "plug and play."

### Differentiation and moat

- **Setup speed as competitive advantage:** "Inspect a new product in
  5 minutes" vs. "reprogram the vision system in 2 days" is a
  concrete, quantifiable benefit.
- **Low moat on the technique itself:** DINOv2 is open-source and
  AnomalyDINO is reproducible. The moat comes from wrapping it in a
  product with domain-specific UX (what does a PCB inspector need vs.
  a machined-parts inspector?), calibrated thresholds, and an
  operator workflow.
- **Foundation model risk:** Meta (DINOv2's creator) could release a
  commercial inspection product. Unlikely but possible. The defense
  is vertical specialization and customer relationships.

### Combine with

- **EfficientAD/PatchCore (§1)** — use DINOv2 features for the first
  shift (zero setup), then transition to a trained EfficientAD model
  once you have 50+ normal images. The product offers both modes:
  "instant" and "calibrated."
- **Conformal prediction (§3)** — wrap the distance-based score in a
  coverage guarantee.
- **Edge inference (§6)** — DINOv2 ViT-S exported to ONNX/TensorRT
  runs on a Jetson. Feature extraction + nearest-neighbor comparison
  is fast enough for real-time.

---

## 8. Wavelet Scattering Transforms (Kymatio)

### What it is

A fixed (not learned) feature extractor based on cascaded wavelet
transforms that produces translation-invariant, deformation-stable
representations of signals. It requires no training data because the
wavelets are mathematically defined, not learned. Feed the output into
a simple classifier (random forest, SVM, XGBoost) and you have a
fault diagnosis system that works with 50 labeled examples.

### Use cases

1. **Bearing fault diagnosis from vibration data.** An industrial
   compressor has one accelerometer. Scattering features from the
   vibration signal, classified by a random forest trained on 30
   examples per fault type (normal, inner race, outer race, ball),
   achieve 95%+ accuracy — matching or beating deep learning with
   1/100th of the data.

2. **Acoustic emission monitoring for structural health.** A bridge or
   pressure vessel has acoustic emission sensors. Scattering features
   extract the frequency-time structure of emission events without
   being sensitive to exact timing. Classified events indicate crack
   growth, corrosion, or impact damage.

3. **Power quality monitoring for industrial equipment.** A smart meter
   records voltage/current waveforms. Scattering features capture
   harmonic distortion, transients, and sag/swell patterns. A
   classifier trained on a small labeled set identifies the equipment
   type causing the disturbance (motor startup, VFD noise, arc event).

### Business value (executive summary)

Vibration analysis and acoustic monitoring are established practices
in industrial maintenance, but they typically require an experienced
analyst who "listens" to the data. Wavelet scattering automates the
feature extraction step — the part that takes years of experience —
and feeds it into a simple classifier anyone can train with minimal
examples. The result is consistent, 24/7, automated diagnosis that
doesn't depend on one irreplaceable expert.

### Differentiation and moat

- **Signal processing depth as barrier:** Implementing and tuning
  wavelet scattering for a specific domain (bearing vibration vs.
  acoustic emission vs. power quality) requires understanding of the
  physics of the signal, not just ML pipeline engineering. This is a
  direct mapping to your PhD in physics.
- **Small-data advantage:** No training data needed for the features
  themselves. Competitors using deep learning need thousands of
  labeled examples that SMEs don't have.
- **Interpretability:** Scattering coefficients map to physical
  scales (frequency bands, modulation depths). An engineer can
  understand why the system flagged a signal. This matters in
  safety-critical applications.

### Combine with

- **STUMPY (§4)** — scattering features compress oscillatory signals
  before matrix profile computation. The matrix profile finds *when*
  something unusual happened; scattering tells *what kind* of
  unusual it is.
- **TabPFN (§2)** — scattering features as input to TabPFN for
  fault classification with very few labeled examples.
- **Neural operators (§5)** — scattering features of a vibration
  signal compared to the neural operator's predicted vibration
  signature. The residual is a physics-aware anomaly signal.
- **Edge deployment (§6)** — Kymatio runs on GPU or CPU; the fixed
  transform is light enough for edge hardware.

---

## 9. Graph Neural Networks for Industrial Applications (PyG / DGL)

### What it is

Neural networks that operate on graph-structured data — nodes
connected by edges. Sensors on a structure form a graph (nodes =
sensors, edges = physical connections). Components in an SBOM form a
graph (nodes = libraries, edges = dependencies). Equipment in a plant
forms a graph (nodes = machines, edges = material/energy flows).

### Use cases

1. **SBOM dependency analysis for CRA compliance.** A manufacturer's
   SBOM has 500 components with 2,000 dependency edges. When a CVE
   hits one component, a GNN traverses the dependency graph to
   determine which products, through which paths, are affected — and
   how critical each path is (direct dependency vs. 4 levels deep,
   statically linked vs. optional). This is the core matching problem
   in the CRA silent watchdog idea card.

2. **Sensor network optimization for structural health monitoring.**
   A bridge has 40 strain gauges. A GNN trained on the sensor graph
   identifies which 10 sensors carry 90% of the diagnostic
   information — enabling cost reduction without information loss.
   Conversely, it identifies where to add sensors for maximum
   diagnostic improvement.

3. **Failure propagation prediction in industrial plants.** A GNN on
   the plant's equipment topology predicts how a failure in one
   machine (compressor A) will cascade through connected systems
   (heat exchanger B, downstream process C). The maintenance manager
   gets: "if compressor A fails, production line 2 goes down in 4
   hours."

### Business value (executive summary)

Many industrial problems are really network problems disguised as
list problems. An SBOM is not a list of components — it's a
dependency tree. A plant is not a list of machines — it's a network
of flows. GNNs operate on the network structure directly, which means
they can answer questions that flat-table analytics cannot: "what's
connected to what, and what happens if one node fails?" For CRA
compliance specifically, graph-based dependency analysis is the
difference between "we have a list of components" and "we know
exactly which products are affected through which paths."

### Differentiation and moat

- **Graph modeling is an adjacent capability (asset inventory §3).**
  PostgreSQL + Apache AGE or recursive CTEs already support graph
  queries. PyTorch Geometric (PyG) is the Python-native GNN library.
  The combination is architecturally natural for your stack.
- **Topological knowledge is domain-specific.** Knowing that a
  firmware dependency is different from a web-app dependency (static
  linking vs. dynamic, vendor blob vs. open source) requires the
  hardware-adjacent knowledge in your asset inventory.
- **Data flywheel:** Aggregated anonymized dependency graphs across
  customers reveal which components are most commonly used, most
  commonly vulnerable, and most likely to cause cascading failures.
  This becomes unique intelligence.

### Combine with

- **CRA silent watchdog (idea card)** — graph-based SBOM modeling is
  a core architectural choice for the product. PostgreSQL + Apache
  AGE for storage; PyG for inference on the dependency graph.
- **Neural operators (§5)** — for structural health monitoring, GNNs
  provide the spatial topology that the neural operator uses as input
  mesh. The GNN handles the graph structure; the operator handles
  the physics.
- **Conformal prediction (§3)** — wrap GNN-based cascade predictions
  in coverage guarantees.

---

## 10. Structured-Output LLM Pipelines (Outlines / Instructor / LiteLLM)

### What it is

Tools that constrain an LLM's output to a predefined data schema
(Pydantic model, JSON schema, regex). The model can only produce
valid, structured data — no free-form text, no hallucinated fields,
no post-processing needed. Outlines does this at the token-sampling
level (100% compliance); Instructor does it via validation + retry.
LiteLLM provides a unified interface to any model (cloud or local).

### Use cases

1. **Feed triage agent for NPD scanning.** RSS feeds are parsed by
   feedparser; each entry is sent to a Qwen2.5-7B model on Ollama
   with a Pydantic schema: {relevance_score, trigger_type, matching_
   capabilities, summary}. The output is guaranteed to match the
   schema. A daily digest of the top 10 entries lands in your
   Obsidian vault. This replaces the 143-entries-per-morning problem
   described in your question.

2. **Automated vulnerability triage for CRA reporting.** A CVE
   disclosure is parsed. A local LLM classifies it against a
   structured schema: {affected_component, exploit_status,
   reportability_assessment, confidence, recommended_action}. The
   output feeds into the CRA watchdog's decision workflow.
   Structured output guarantees every field is filled, every
   classification is from the allowed set.

3. **Interview note extraction for validation.** After a Mom Test
   interview, paste the notes. A structured-output call extracts:
   {company_profile, sbom_maturity, time_to_determine, cra_awareness,
   buying_trigger, key_quotes} — exactly the fields from your
   interview guide template. Saves 15 minutes of manual tagging per
   interview.

### Business value (executive summary)

LLMs are powerful but unpredictable — they hallucinate fields, produce
wrong formats, and require expensive post-processing. Structured
output fixes this: the model can only produce data that matches your
schema. For any workflow where you need reliable, machine-readable
output from natural-language input, this is the enabling technology.
The cost of a local 7B model on commodity hardware (one-time €500 GPU
investment) is negligible compared to the labor it replaces.

### Differentiation and moat

- **This is infrastructure, not product.** The moat comes from what
  you build on top of it — the triage taxonomy, the regulatory
  knowledge, the domain-specific schemas. The LLM is a component.
- **Local/privacy advantage:** Running Qwen2.5-7B on-premise means
  customer data (SBOMs, vulnerability details, interview notes) never
  leaves their network. For CRA/NIS2-related products, this is a
  selling point: "your vulnerability data stays on your server."
- **LoRA upgrade path:** Start with prompted Qwen2.5-7B. Log
  predictions and corrections. After 200+ labeled examples, QLoRA
  fine-tune with Unsloth. The model improves without changing the
  architecture. Each customer's corrections are their own competitive
  data.

### Combine with

- **The entire NPD scanning workflow** — this is the enabling layer
  for automating your own process before productizing it.
- **CRA silent watchdog (idea card)** — structured output is how the
  system classifies vulnerabilities, generates ENISA report drafts,
  and produces actionable alerts from unstructured threat feeds.
- **Conformal prediction (§3)** — use CP on the classifier's output
  probabilities to produce calibrated confidence scores. When the
  conformal set is ambiguous ({reportable, review-needed}), escalate
  to a human.

---

## 11. State-Space Models for Long Sequences (S4 / Mamba)

### What it is

A neural architecture that processes long sequences with linear
(not quadratic) computational cost. Where transformers choke on
100,000-point sensor streams, Mamba handles them efficiently. It
also supports streaming inference — processing data point by point
as it arrives, without re-processing the entire history.

### Use cases

1. **Continuous vibration monitoring on edge hardware.** A gearbox
   accelerometer produces 50,000 samples/second. A Mamba model
   processes the stream in real-time on a Jetson, detecting anomalies
   that span minutes (100,000+ samples) without truncating context.
   A transformer would need either truncation (losing context) or
   quadratic memory (crashing the device).

2. **Multi-day process monitoring in batch manufacturing.** A
   pharmaceutical or chemical batch runs for 72 hours with 20 sensors
   sampled every second — 5 million data points. Mamba ingests the
   full batch as a single sequence and identifies the process segment
   where deviation began.

3. **Power grid frequency monitoring.** Grid frequency data at 50 Hz
   over weeks produces millions of samples. Mamba identifies
   transient events and trending patterns across the full history
   without windowing artifacts.

### Business value (executive summary)

Industrial sensor data is often too long for conventional AI models
to process without cutting it into short windows — and the interesting
patterns (slow degradation, rare events, seasonal effects) span
exactly those cuts. Mamba processes the full signal without truncation,
at a cost that scales linearly. For the buyer, this means: fewer
missed events, earlier detection, and the ability to see patterns
that span hours or days, not just minutes.

### Differentiation and moat

- **Still early-stage for industrial time-series.** Mamba is proven
  for language and biomedical sequences; industrial deployment is
  emerging (MambaAD, TimeMachine). An early mover who validates
  Mamba on specific industrial signals builds the training data and
  domain expertise that followers lack.
- **Edge-friendly architecture.** Linear complexity + streaming
  inference = lower memory and compute than transformers. Fits the
  Jetson deployment model.
- **Risk:** The technique is newer and less validated than
  alternatives. Use it as a "second opinion" alongside STUMPY and
  PatchTST, not as a sole approach.

### Combine with

- **Wavelet scattering (§8)** — scattering features as input to
  Mamba. The wavelets handle the signal physics; Mamba handles the
  long-range temporal dependencies.
- **Edge inference (§6)** — Mamba's linear memory footprint makes it
  more deployable on edge than transformers.
- **Conformal prediction (§3)** — wrap Mamba's anomaly scores in
  coverage guarantees.

---

## 12. Time-Series Foundation Models (Chronos / TimesFM / MOMENT)

### What it is

Pre-trained models that forecast time-series data zero-shot — no
training on your specific data needed. Chronos (Amazon) is the
strongest in independent tests. The idea is analogous to GPT for
text: pre-train on diverse time-series, then apply to new ones.

### Use cases

1. **Quick-start demand or load forecasting.** An energy company
   needs a 48-hour load forecast for a new region where they have 3
   months of history. Chronos produces a usable forecast immediately,
   as a baseline while a custom model is trained.

2. **Anomaly detection via forecast deviation.** Run Chronos on a
   sensor stream; compare its forecast to actual readings. Large
   deviations are anomalies. This requires no training and gives a
   reasonable first-pass anomaly detector for any time-series.

3. **Synthetic data generation for model training.** Use a foundation
   model to generate realistic synthetic sensor data for scenarios
   that haven't occurred yet (e.g., failure modes). This bootstraps
   training sets for supervised models.

### Business value (executive summary)

When a customer says "I have 3 months of data and I need a forecast
by Friday," a foundation model is the fastest path to a working
answer. It won't be the best answer — a custom model trained on their
data will beat it. But it's the answer they get today, not in 8 weeks.

### Differentiation and moat

- **Very low moat.** These are open-source, general-purpose models.
  The moat is in knowing when to use them (quick starts, baselines)
  and when to replace them (serious deployments with enough data).
- **Benchmark contamination is a real risk.** Many published
  comparisons are unreliable because training and test datasets
  overlap. Do not trust published accuracy numbers for your specific
  domain. Always validate on held-out data.
- **Best used as a component**, not a product. A product that can
  "auto-start" with a foundation model and then transition to a
  custom model as data accumulates has a smoother onboarding story.

### Combine with

- **TabPFN (§2)** — for the structured features extracted from the
  time-series (lags, rolling stats, motifs).
- **STUMPY (§4)** — matrix profile as a complementary anomaly detector
  to foundation-model residuals.
- **Conformal prediction (§3)** — essential for turning a forecast
  into a prediction interval with a guarantee.

---

## Cross-cutting observations

### The recurring stack

Several use cases converge on the same architecture:

**Sensor input → wavelet scattering or matrix profile (feature
extraction) → TabPFN or gradient boosting (prediction) → conformal
prediction (uncertainty) → edge deployment (Jetson)**

This is a modular stack where each layer is independently valuable
and independently replaceable. It maps directly to the asset
inventory and can be productized as a platform with domain-specific
configurations rather than a custom build per customer.

### The conformal prediction thread

CP appears in every "combine with" section because it solves the
regulatory buyer's core objection: "how do I know I can trust this?"
It should be treated as a design principle for any regulated-market
product, not as an optional feature.

### Physics as moat, software as scale

The strongest moat entries (neural operators, wavelet scattering,
physics-informed anomaly detection) all require physics/math depth
to implement correctly for a specific application. The weakest moat
entries (foundation models, generic structured-output LLMs) are
commodities. The pattern confirms the NPD process principle: combine
domain insight + technical capability. Don't compete on the
commodity layer.

### The "CRA watchdog" as integration point

The CRA silent watchdog idea card is a natural home for several
of these techniques: graph modeling for SBOM dependencies (§9),
structured-output LLMs for vulnerability triage (§10), TabPFN for
risk scoring (§2), and conformal prediction for escalation
confidence (§3). The idea is already investigating status — these
pairings should be noted in the idea card.
