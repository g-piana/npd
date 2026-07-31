# The Overshadowed Toolkit: High-Impact ML, Signal Processing & Data Engineering Techniques for Regulated Industrial Products (2021–2026)

## TL;DR

- **The most valuable industrial ML advances of 2021–2026 are not LLMs but a quieter cluster of "small-data, edge-ready, uncertainty-aware" methods**: memory-bank image anomaly detection (PatchCore→EfficientAD→DINOv2-based training-free detectors), conformal prediction for distribution-free uncertainty, TabPFN for small-sample tabular problems, matrix-profile and state-space models for sensor time-series, and neural operators for physics surrogates — all Python/PyTorch-native and most runnable on a Jetson.
- **For your regulated-industry use cases, prioritize in this order**: (1) conformal prediction (MAPIE) as a universal wrapper for safety-critical UQ; (2) anomalib + EfficientAD/PatchCore for visual inspection with tens of images; (3) TabPFN v2.5 vs. gradient boosting for SME tabular data; (4) STUMPY matrix profile + a strong deep baseline (PatchTST/TimesNet) for time-series; (5) DINOv2 frozen features for few-shot inspection. Treat time-series and vision "foundation models" as overhyped for domain-specific industrial data until validated on your own data — a 2025 Bosch/KIT study found foundation models that ace public benchmarks _fail_ on real factory images.
- **For the agentic "feed triage" question: skip the agent frameworks.** The correct architecture is a plain scheduled pipeline (feedparser → one structured-output LLM call per entry validated with Instructor/Outlines Pydantic schemas → PostgreSQL → daily cron/APScheduler → HTMX digest). LangGraph/CrewAI only earn their complexity with cycles, branching, or human-in-the-loop gates. For privacy, Qwen2.5-7B on Ollama is sufficient; defer LoRA/QLoRA fine-tuning until you have ~200+ labeled examples and a frozen taxonomy.

---

## Key Findings

1. **Memory-bank image anomaly detection is mature and deployable.** PatchCore (2022) and EfficientAD (WACV 2024) hit ~99%+ AUROC on MVTec AD at millisecond latencies, are implemented in Intel's **anomalib** library with OpenVINO export, and need only normal images. The frontier has moved to _logical_ anomalies (MVTec LOCO), where SALAD (ICCV 2025) set a new 96.1% AUROC.
2. **Frozen foundation-model features (DINOv2) enable training-free few-shot inspection**, but generic vision-language foundation models are _not_ plug-and-play for real factory defects.
3. **Conformal prediction is the single most important UQ technique for regulated industries** — model-agnostic, distribution-free coverage guarantees, mature Python tooling (MAPIE v1), and directly relevant to safety cases.
4. **TabPFN v2 (Nature, Jan 2025) changed the small-data tabular game** — a single 2.8s inference beats a 4-hour-tuned ensemble on datasets up to ~10K rows, exactly the SME regime.
5. **The "transformers for time-series" debate resolved pragmatically**: no architecture universally dominates; simple linear models (DLinear), patched transformers (PatchTST), and classical matrix profiles (STUMPY) each win in different regimes, and for limited-data industrial series you should benchmark all three.
6. **State-space models (S4/Mamba) offer linear-complexity long-sequence modeling** — attractive for long sensor streams and edge deployment.
7. **Neural operators (FNO, DeepONet) are the mature "beyond-PINN" physics-ML tool**, with a PyTorch-ecosystem library, while differentiable filtering hybrids (differentiable Kalman filters) bridge classical estimation and deep learning for sensor fusion.
8. **Wavelet scattering transforms are an underused, small-data-friendly signal-processing/ML hybrid** ideal for vibration/acoustic fault diagnosis.
9. **The agentic-framework landscape has consolidated but is overhyped for solo builders**; structured-output pipelines beat agent frameworks for most concrete tasks.

---

## Details (by capability area)

### 1. Industrial Anomaly Detection — Image

**The memory-bank / student-teacher family.**

- **PatchCore** (Roth et al., 2022, arXiv:2106.08265) — stores a coreset memory bank of nominal patch features from a frozen ImageNet CNN; anomaly = distance to nearest nominal patch. Still a standard strong baseline.
- **EfficientAD** (Batzner, Heckler, König; WACV 2024, arXiv:2303.14535) — student-teacher + autoencoder design. Per the paper it reaches an **AU-ROC of 99.8% on MVTec AD**, with the EfficientAD-S variant running at **2.2 ms latency** and achieving AU-ROC ~3.6% higher than the prior SOTA (AST) while being 24× faster (measured on an NVIDIA RTX A6000 GPU). Two variants (S/M). Crucially it addresses _both_ structural and logical anomalies (via the autoencoder branch), and was evaluated on MVTec AD, VisA, and MVTec LOCO. This is the pragmatic default for high-throughput inspection.
- **Why it matters industrially**: these methods train on _normal images only_ (unsupervised), need no defect labels, run fast on modest hardware, and produce pixel-level localization maps for operator explainability.

**State of the art on logical anomalies (MVTec LOCO).** Logical anomalies (wrong count, wrong arrangement, missing part) are the hard, high-value industrial case. Recent SOTA:

- **SALAD** (Fučka, Zavrtanik & Skočaj, ICCV 2025, arXiv:2509.02101) — "Semantics-Aware Logical Anomaly Detection." Per the paper, SALAD "achieves a new state-of-the-art result on MVTec LOCO (AUROC of 96.1%), outperforming competing methods by a significant margin of 3.0 percentage points," plus 98.3% mean AUROC across MVTec AD + VisA.
- **VLM-based training-free approaches** — LogicAD (arXiv:2501.01767) and LogicQA (arXiv:2503.20252, ~87.6% AUROC on LOCO) use vision-language models to generate checklists/explanations; training-free, few-shot, and _explainable_ — attractive where you must justify a reject decision.

**anomalib (Intel/OpenVINO).** The reference open-source library (`open-edge-platform/anomalib`, formerly openvinotoolkit/anomalib; ICIP 2022 paper). Largest ready-to-use collection of deep AD algorithms; Lightning-based; most models export to **OpenVINO IR** for accelerated Intel inference; includes zero-/few-shot **WinCLIP** since v1.0; AUPIMO benchmarking metric (arXiv:2401.01984). Recent releases add **SuperADD** (training-free class-agnostic segmentation using **DINOv3** multi-layer memory banks — CVPR 2026 VAND 4.0 Industrial Track winner) and the **AutoVI** automotive-inspection dataset. This is your fastest path from "tens of good images" to a deployed inspector; note the pipeline can target OpenVINO on Intel or be re-exported for Jetson via ONNX/TensorRT.

**DINOv2 / SAM as frozen extractors for few-shot inspection.**

- **AnomalyDINO** (Damm et al., WACV 2025, arXiv:2405.14529) — vision-only, training-free, patch-level nearest-neighbor in frozen DINOv2 feature space. Per the paper it achieves SOTA, "pushing the one-shot performance on MVTec-AD from an AUROC of 93.1% to 96.6% (thereby halving the gap between the few- and full-shot setting)." Methodologically simple; no fine-tuning or meta-learning.
- **SubspaceAD** (2026, arXiv:2602.23013) — frozen DINOv2 features + PCA subspace; training-free, interpretable residual scores; found DINOv3 gives "no clear advantage in frozen transfer" but better after full fine-tuning.
- **Reality check** (Baeuerle et al., Bosch/KIT, arXiv:2509.20479, presented at Proc. 35. Workshop Computational Intelligence, Berlin, Nov 20–21 2025; "Are Foundation Models Ready for Industrial Defect Recognition?") — tested multiple FMs on a custom real-world dataset (IndustrialSAT) plus MVTec AD and Oxford-IIIT-Pet. Verbatim: "We show that all of those models fail on our real-world data, while the very same models perform well on public benchmark datasets." The lesson for your regulated products: frozen-feature few-shot methods are a strong _starting point_ with minimal labels, but you must validate on your own imagery and likely need some adaptation; do not trust public-benchmark AUROC as evidence for your line.

_Fit to your stack:_ All PyTorch; anomalib is pip-installable; DINOv2 methods are training-free and run on a Jetson after ONNX/TensorRT export.

---

### 2. Time-Series Anomaly Detection & Forecasting

**Matrix Profile / STUMPY.** STUMPY (`stumpy.readthedocs.io`; SciPy library, based on UC Riverside's Keogh-group STOMP/GPU-STOMP work) computes the matrix profile — the z-normalized Euclidean distance from each subsequence to its nearest neighbor. Discords = anomalies; motifs = recurring patterns; also supports semantic segmentation, time-series chains, and multidimensional profiles. **Why it matters**: it is interpretable, essentially hyperparameter-free (just window length), needs no training data, and scales to long streams — ideal for SME sensor monitoring where you cannot train a deep model and must explain the alert. This is a first-line tool that the LLM wave completely overshadowed.

**The "are transformers good for time-series?" debate.**

- **DLinear** (Zeng et al., AAAI 2023, arXiv:2205.13504) — an "embarrassingly simple" one-layer linear model with series decomposition that outperformed complex transformers (Informer, Autoformer, FEDformer) on nine benchmarks, arguing that the transformers' gains "had little to do with temporal-relation extraction."
- **PatchTST** (Nie et al., ICLR 2023) — reintroduced patching + channel-independence and slightly beat DLinear on most datasets ("A Time Series is Worth 64 Words"); ~21% MSE reduction reported vs. prior transformers.
- **TimesNet** (Wu et al., ICLR 2023) — reshapes 1D series into 2D by discovered periods; strong general backbone across forecasting/imputation/anomaly/classification.
- **Pragmatic resolution**: no family uniformly dominates (as multiple 2024–2026 surveys note); with limited industrial data, simple/linear and classical methods are highly competitive and less prone to overfit. **Recommendation: benchmark STUMPY + a gradient-boosted lag-feature model + PatchTST/TimesNet before committing.** These deep models are implemented in the Time-Series-Library (`thuml/Time-Series-Library`, "TSlib") and in NeuralForecast/AutoGluon.

**State-space models (S4, Mamba) for long sensor sequences.**

- **S4** (Gu et al., 2021) — structured state-space model; models long-range dependencies with **linear** O(N) complexity; dominates the Long Range Arena benchmark.
- **Mamba** (Gu & Dao, 2023) — adds input-dependent "selective" state transitions; linear scaling with fast inference; spawned time-series variants (TimeMachine, MambaAD for anomaly detection, S5/S4ND for multivariate/multidimensional). **Why it matters**: linear complexity and streaming-friendly recurrence make SSMs attractive for very long vibration/telemetry windows and for edge inference where transformer quadratic memory is prohibitive. Still younger/less "plug-and-play" than transformers; treat as promising, benchmark before betting.

**Time-series foundation models (Chronos, TimesFM, MOMENT, Lag-Llama, Moirai).** A wave from Amazon/Google/Salesforce/ServiceNow in late 2024–2025 offering _zero-shot_ forecasting. In an independent short-term load-forecasting study (Liao et al., arXiv:2411.11350), "the Chronos model significantly outperforms nine popular baseline models for both deterministic and probabilistic load forecasting" across five real-world datasets over 1–48h horizons, "even though the Chronos model is neither tailored nor fine-tuned to these specific load datasets"; TimesFM/TimeGPT were competitive, while MOMENT and Lag-Llama were weaker. **Caveat (well-documented)**: benchmark contamination is rampant — datasets like Australian Electricity Demand and ETT appear in both pretraining and "zero-shot" evaluation, making many published comparisons "practically useless" (arXiv:2510.13654). For your domain-specific industrial signals, zero-shot foundation models are worth a quick baseline but are likely _overhyped_; a small model trained/fine-tuned on your data usually wins.

---

### 3. Uncertainty Quantification

**Conformal prediction — the top pick for regulated/safety-critical work.** Conformal prediction (CP) wraps _any_ trained model and produces prediction intervals (regression) or prediction sets (classification) with a _distribution-free, finite-sample marginal coverage guarantee_ under exchangeability, using a held-out calibration set. This is exactly the property auditors and safety cases want: "with 95% coverage" is a provable statement, not a calibration hope.

- **MAPIE** (`scikit-learn-contrib/MAPIE`; v1 released 2025) — the leading Python CP library (highest GitHub stars among CP libraries per its maintainers), developed since April 2021 by Capgemini/Inria/Michelin/ENS. Supports regression, classification, time-series (EnbPI), Jackknife+, Mondrian (group-conditional) CP, and _risk control_ (guarantees on recall/precision for multilabel and segmentation). Works with scikit-learn, PyTorch, TensorFlow. 2026 roadmap adds risk control for LLM-as-judge and image segmentation.
- **crepes** (Boström, 2022) — conformal classifiers/regressors and Venn predictors; gaining traction.
- Others: **puncc**, **TorchCP** (PyTorch-native, rising since 2024); MAPIE remains the safest default.
- **Why it beats alternatives**: unlike Bayesian deep learning or MC-dropout it needs no architectural change or retraining, and its guarantee is mathematically explicit.

**Evidential deep learning (EDL).** Single-pass networks that predict the parameters of a higher-order (Dirichlet / Normal-Inverse-Gamma) distribution to estimate aleatoric + epistemic uncertainty in one forward pass (Sensoy et al. 2018 for classification; Amini et al. 2020 "Deep Evidential Regression"). **Attractive for edge** (one model, one pass, cheap vs. ensembles). **Important caveat**: a body of 2024–2025 work questions whether EDL _faithfully_ represents epistemic uncertainty (Jürgens et al., arXiv:2402.09056; multiple "revisiting"/"relaxing" papers). Recommendation: use EDL where compute is tight, but **for compliance evidence prefer conformal prediction, or combine EDL point-uncertainty with a conformal wrapper** (recent work combines the two, e.g., evidential + conformal sets, arXiv:2406.10787).

---

### 4. Small-Data & Tabular

**TabPFN — the champion of small data.**

- **TabPFN v2** (Hollmann et al., **Nature, Jan 2025**, s41586-024-08328-6) — a transformer pre-trained on millions of synthetic tabular tasks that does Bayesian in-context learning. Per the paper, "In 2.8 s, TabPFN outperforms an ensemble of the strongest baselines tuned for 4 h in a classification setting," evaluated "across 29 classification datasets and 28 regression datasets" (baselines included CatBoost/XGBoost/LightGBM). The Nature-stated effective range is **up to 10,000 samples** (≤~500 features, ≤10 classes) — precisely the SME industrial regime.
- **TabPFN-2.5** (Prior Labs, Nov 2025, arXiv:2511.08667) — scales to 50,000 rows / 2,000 features; reports a **100% win rate vs. default XGBoost on small-to-medium classification datasets (≤10K rows, 500 features)** and an 87% win rate on larger datasets up to 100K samples, plus a distillation engine to compress into a compact MLP or tree ensemble for low-latency/edge deployment.
- **Ecosystem/independent benchmarks**: TabArena leaderboard shows RealTabPFN v2.5 as the best single tabular foundation model (with the caveat that TabPFN's own lab is involved in TabArena). SAP is investing heavily in Prior Labs, signaling enterprise seriousness.
- **vs. gradient boosting in practice**: TabPFN wins decisively on small, clean, numeric-heavy datasets and requires _no tuning_. Gradient boosting (XGBoost/LightGBM/CatBoost) remains preferable for large datasets, heavy categorical/lag-feature engineering, or where you need a tiny, fully self-hosted, interpretable model. **Recommendation**: for SME datasets under ~10K rows, run TabPFN v2.5 as a zero-effort strong baseline _and_ a tuned LightGBM; ship whichever wins on your validation set. Both are Python; TabPFN uses a GPU but can distill to CPU-friendly models.
- **TabR** and other deep-tabular retrieval models (TabICL, CARTE) exist but TabPFN dominates the small-data conversation.

_Pair TabPFN/GBMs with MAPIE conformal intervals for regulated deployments._

---

### 5. Physics-Informed ML Beyond Vanilla PINNs

**Neural operators — the mature beyond-PINN tool.**

- **DeepONet** (Lu et al., Nature Machine Intelligence 2021) — branch/trunk architecture learning operators via the universal operator-approximation theorem.
- **Fourier Neural Operator (FNO)** (Li et al., 2020, arXiv:2010.08895) — parameterizes the integral kernel in Fourier space; resolution-invariant; learns PDE solution operators, reportedly up to 3 orders of magnitude faster than traditional solvers for the cases studied (a claim later contested/contextualized as applying to smooth laminar flows).
- **Library**: `neuraloperator/neuraloperator` — comprehensive **PyTorch** library, official FNO implementation, part of the PyTorch Ecosystem; resolution-invariant modules; recent practical guide (arXiv:2512.01421, arXiv:2412.10354). **Why it matters industrially**: fast surrogates for digital twins, real-time control, inverse design; resolution invariance means train on coarse grids, deploy on fine ones. Compared to vanilla PINNs, operators _generalize across many inputs/geometries_ rather than solving one instance.

**Differentiable physics / differentiable filtering (sensor fusion).**

- **Differentiable Kalman/particle filters** cast the filter as a differentiable computational graph (like an RNN) with physics-based inductive bias, learning system/measurement models end-to-end from data (Kloss et al. "How to train your differentiable filter," Auton. Robots 2021; α-MDF attention-based multimodal differentiable filter, 2023; differentiable EKF/UKF/EnKF variants). **Why it matters**: for drones/aerospace/IoT sensor fusion you keep the interpretability and stability of Bayesian estimation while learning the hard-to-model parts from data. Hybrid Kalman+PINN approaches (e.g., leak detection, ScienceDirect 2025) and differentiable-simulation-in-EKF object tracking (arXiv:2309.15703) illustrate the pattern.
- **PINN limitations to know**: vanilla PINNs (soft PDE-residual loss) suffer from training pathologies and are often outperformed by operator methods or hard-constrained architectures for real engineering problems — a key reason to reach past the basic loss-penalty PINN.

_Fit:_ neuraloperator and differentiable-filter code are PyTorch-native; surrogates can run on Jetson.

---

### 6. Edge & Efficient Inference

**Quantization.** INT8/INT4 quantization + **TensorRT** is the backbone of Jetson deployment; documented speedups up to ~27× over unoptimized models on Jetson Nano and consistent gains on Orin (with the caveat that TensorRT can fail to build very large models under memory limits, and INT8 needs calibration). FP16 is the low-risk default; INT8 with calibration for maximum throughput. For LLMs specifically, **GPTQ** and **AWQ** are the standard post-training weight-quantization methods; newer NVFP4 4-bit formats are emerging for low-power edge (arXiv:2606.06527).

**Efficient architectures.**

- **EfficientViT** (Cai et al., ICCV 2023) — multi-scale linear attention; on Jetson AGX Orin with TensorRT+FP16 delivers large throughput gains at equal/higher accuracy vs. prior segmentation/classification models (e.g., ViT-Huge-quality zero-shot segmentation at ~84× higher throughput).
- **MobileNetV4** (2024) and **FastViT** (Apple, ICCV 2023) — mobile/edge CNN-ViT hybrids designed for low-latency inference; MobileNetV4-Conv-Small appears in current edge-benchmark suites.
- **Pattern**: PyTorch → ONNX → TensorRT engine (layer fusion, FP16/INT8 calibration, kernel auto-tuning); profile with `trtexec`. Torch-TensorRT and ONNX Runtime are the two mainstream optimization paths for your Jetson targets.

**Knowledge distillation** remains a reliable lever (teacher→student); TabPFN-2.5's distillation engine and student-teacher AD methods (EfficientAD) are industrial examples.

---

### 7. Foundation Models for Non-Language Tasks — Useful or Overhyped?

- **Vision (DINOv2, SAM)**: genuinely useful as _frozen feature extractors / few-shot backbones_ (AnomalyDINO, SubspaceAD, anomalib's DINOv3-based SuperADD). But generic zero-shot FMs **fail on real factory defect data** (Bosch/KIT reality check, arXiv:2509.20479). Verdict: **useful with adaptation, overhyped as zero-shot.**
- **Time-series (TimesFM, Chronos, Lag-Llama, MOMENT, Moirai)**: convenient zero-shot baselines; Chronos is the strongest in several independent tests; but benchmark contamination and domain mismatch mean **for your domain-specific signals a small tailored model usually wins.** Verdict: **worth a baseline, usually overhyped for specialized industrial data.**
- **General rule for your regulated context**: foundation models buy you a fast start with few labels; they do not remove the need to validate on your own data and quantify uncertainty.

---

### 8. Signal Processing + ML Hybrids

**Wavelet scattering transform (WST).** A cascade of wavelet convolutions + modulus + averaging that produces translation-invariant, deformation-stable features — essentially a _fixed_ (untrained) CNN grounded in signal-processing theory, so it needs **no training data and few samples**. Widely validated for **bearing/gearbox fault diagnosis** (e.g., Sensors 2025, PMC11819883; compound-fault detection, Measurement 2022; bevel-gearbox diagnosis, J. Braz. Soc. Mech. Sci. 2025), typically feeding an SVM/random-forest/XGBoost classifier and beating raw-signal baselines on small datasets. **Library**: Kymatio (PyTorch/NumPy/TensorFlow scattering transforms). **Why it matters**: ideal for vibration, acoustic emission, and power-quality monitoring where data is scarce, physics is oscillatory/non-stationary, and you want stable, explainable features. Related learnable approaches: continuous/synchrosqueezing wavelet transforms + CNN, and learnable filter banks.

---

### 9. Graph Neural Networks for Industrial Applications

- **Structural health monitoring & sensor networks**: GNNs model sensors as nodes and physical/mechanical couplings as edges. A well-cited framework combines Graph Signal Processing + GNN on real bridge strain/vibration data (Bloemheuvel et al., Applied Network Science 2021; arXiv:2105.05316) to identify key sensors and forecast strain (T-GCN). Dynamic GNNs (DynGNN, 2024) enable near-real-time damage identification on truss bridges.
- **Predictive maintenance / RUL**: a 2025 survey ("A survey on graph neural networks for remaining useful life prediction," Mechanical Systems and Signal Processing vol. 229, 2025) catalogs methodologies; knowledge-graph + GNN maintenance-planning and causal-intervention GNNs (CIGNN) for fault diagnosis on industrial datasets.
- **Dependency/supply-chain mapping (SBOM-relevant)**: graph-based digital twins for supply-chain optimization (arXiv:2504.03692, arXiv:2411.08550) — the natural formalism for dependency graphs, though this is still an emerging research area.
- **Libraries**: PyTorch Geometric (PyG) and DGL are the PyTorch-native standards. **Verdict**: high-value where your data is genuinely relational (sensor topology, asset dependency, BOM/SBOM); otherwise simpler models suffice.

---

### 10. Agentic Frameworks — A Hype-Free Assessment

**Landscape (early 2026).** Four serious frameworks: **LangGraph** (graph/state-machine, best production/enterprise story — durable execution, checkpointing, LangSmith observability; highest complexity), **CrewAI** (role-based multi-agent, fast iteration), **AutoGen** (Microsoft, conversational multi-agent — now effectively in _maintenance mode_, README redirects newcomers to Microsoft's newer "Agent Framework"), and **smolagents** (HuggingFace, code-writing agents, fast-growing). Reported benchmark spreads are modest and task-dependent (LangGraph ~76% vs AutoGen ~68% on medium tasks in one blog benchmark — treat vendor/blog numbers as directional, not peer-reviewed). Key trend: the minimum model size for reliable agentic behavior is falling (tasks needing 70B in early 2025 reportedly work at ~32B in early 2026).

**The concrete "feed triage agent" recommendation: do NOT use an agent framework.** The task (fetch RSS → score each entry against a taxonomy → write daily digest) is a **linear DAG**, the exact case where graph frameworks add a "complexity tax." Multiple 2026 sources converge: reach for LangGraph only when you have cycles, conditional branching, multi-agent handoffs, or human-in-the-loop gates.

**Simplest viable architecture (all Python-native, fits your FastAPI/Postgres/HTMX stack):**

1. **feedparser** (v6.x) — normalizes RSS/Atom/JSON feeds to uniform fields.
2. **One structured-output LLM call per entry**, validated against a **Pydantic** schema (your taxonomy scores). Use:
    - **Outlines** (~13k stars) — constrains token sampling to _guarantee_ 100% schema compliance with zero retries; fastest for high-volume local-model classification. **Best fit here.**
    - **Instructor** (~11k stars, ~3M monthly downloads) — wraps any LLM client, Pydantic validation + auto-retry; safest, best-documented general choice; has an official LiteLLM integration.
    - **Pydantic-AI** (~14k stars) — heavier typed-agent runtime; use only if you later want agent abstractions.
3. **LiteLLM** — unified OpenAI-compatible interface across cloud + local Ollama with fallbacks/retries/cost-tracking; develop against a cheap cloud model, switch to local by changing one string. (Note: a documented GitHub issue observed that structured-output enforcement for local Ollama models historically lagged cloud providers; Instructor/Outlines close that gap via prompt-level or sampling-level enforcement.)
4. **Storage**: PostgreSQL rows per entry (score, taxonomy tags, summary).
5. **Scheduling**: system **cron** (simplest) or **APScheduler** (in-process with FastAPI).
6. **UI**: FastAPI + HTMX digest view.

**Local/small LLMs for privacy-preserving on-prem use (2026):**

- **Qwen2.5-7B-Instruct** (7.6B, Apache-2.0, 128K context, MMLU ~74) — strongest single recommendation; explicitly strong at summarization and JSON structured output. **Qwen2.5-3B** (MMLU ~65.6; separate Qwen license, not Apache-2.0) for speed. **Qwen3** (April 2025, Apache-2.0 dense 0.6–32B + MoE) if you want the newest. Alternatives: Llama 3.1-8B (MMLU ~66.7), Gemma 3, Phi-4.
- **Serving runtime**: **Ollama** for a solo dev / <5 users — dead-simple, OpenAI-compatible at localhost:11434, serial by default (a non-issue for a once-daily batch). Scale to **vLLM** (PagedAttention, continuous batching; needs a GPU, reported ~6–16× throughput over Ollama's serial default under concurrency) only if volume/latency demands. **llama.cpp** for CPU/edge (GGUF). Common pattern: Ollama dev → llama.cpp CPU server → vLLM GPU production, same weights.

**LoRA/QLoRA coupling with local LLMs — worth it, but later.** Start with **few-shot prompting + Outlines constrained decoding** (zero training, immediate). The peer-reviewed evidence is mixed: fine-tuned/LoRA classifiers beat zero-shot prompting on most classification tasks _except sentiment_ (Vajjala & Shimangaud, arXiv:2502.11830), and LoRA is "a compelling compromise" between full fine-tuning and in-context learning (PEARC'25). But **LoRA needs roughly a few hundred labeled examples to reliably beat good prompting** (studies show payoff unreliable below ~150 labels) and is **highly sensitive to train/test prompt mismatch** (freeze your prompt template). Data-requirement rules of thumb: few-shot prompting 2–10 examples/label; lightweight encoders SetFit (<~20/label, struggles beyond ~5 classes) or FastFit (10/label, scales to 50–150 classes); generative QLoRA plan for ~200+ labels.

- **Tools**: **PEFT** (HF, base LoRA/QLoRA/DoRA); **Unsloth** (best for solo dev on one GPU — 2–5× faster training, ~70–80% less VRAM; e.g., Qwen2.5-7B 4-bit in ~49 min on a free-tier T4; single-GPU only in the free version); **Axolotl** (YAML config, multi-GPU/distributed); **torchtune** (pure PyTorch). QLoRA original: Dettmers et al., arXiv:2305.14314; LoRA: Hu et al., arXiv:2106.09685.
- **Workflow**: ship prompting first, log model predictions vs. your corrections to accumulate labels, then QLoRA-tune Qwen2.5-7B with Unsloth once you have ~200+ examples and a frozen taxonomy, and A/B against the prompting baseline. For a many-category taxonomy with few labels, consider a SetFit/FastFit encoder as a cheaper non-generative classifier.

**Verdict for a solo technical founder**: agentic _patterns_ (structured tool use, retry-on-validation-failure) are useful; agentic _frameworks_ are largely unnecessary and immature for this class of problem. Build the boring pipeline; add LangGraph only if a genuine loop/human-gate appears.

---

## Recommendations (staged, with thresholds)

**Stage 1 — Adopt now (low risk, high leverage):**

1. **Wrap every predictive model in conformal prediction (MAPIE).** This is the single highest-ROI move for regulated products — provable coverage with no retraining. Threshold to escalate: if calibration-set exchangeability is violated (distribution shift), switch to Mondrian/time-series CP variants or adaptive CP.
2. **For visual inspection, start with anomalib + EfficientAD (or PatchCore).** Collect tens–hundreds of normal images, train unsupervised, export to OpenVINO/ONNX→TensorRT. Threshold: if logical anomalies (count/arrangement) dominate, move to SALAD-style or VLM-checklist methods.
3. **For SME tabular problems (<10K rows), benchmark TabPFN v2.5 against a tuned LightGBM** and ship the winner + conformal intervals.
4. **For sensor time-series, deploy STUMPY matrix profile first** for interpretable, training-free anomaly detection; add a deep baseline only if motif/discord methods underperform.

**Stage 2 — Pilot (medium effort):** 5. **Few-shot inspection with frozen DINOv2 (AnomalyDINO)** when you have only 1–5 good images per class — but **validate on your own imagery** (per the Bosch/KIT failure finding) before trusting it. 6. **Vibration/acoustic diagnosis: wavelet scattering (Kymatio) + gradient boosting** as a small-data, explainable baseline. 7. **Physics surrogates: neuraloperator (FNO/DeepONet)** for digital-twin/fast-simulation needs; **differentiable Kalman filters** for learnable sensor fusion in drones/aerospace. 8. **Edge: standardize a PyTorch→ONNX→TensorRT (FP16, then INT8-calibrated) pipeline**; evaluate EfficientViT/FastViT/MobileNetV4 backbones on Orin.

**Stage 3 — Build the feed-triage tool (now, it's low-risk):** 9. Ship the **feedparser → LiteLLM + Outlines/Instructor → Postgres → cron → HTMX** pipeline with **Qwen2.5-7B on Ollama**. No agent framework. 10. **Defer LoRA/QLoRA** until you have ~200+ labeled entries and a frozen taxonomy; then fine-tune with Unsloth and A/B test. Threshold to fine-tune: prompting accuracy plateaus below your business requirement _and_ you have the labels.

**Watch-list / re-evaluate if these mature:** Mamba/SSM time-series libraries; time-series foundation models on _your_ validated data; agentic frameworks if a genuine multi-step/human-in-loop workflow emerges.

---

## Caveats

- **Benchmark contamination in time-series foundation models is severe** — treat published zero-shot numbers skeptically and validate on held-out, domain-specific data.
- **Public-benchmark AUROC does not transfer to real factory data** — the Bosch/KIT study is a direct warning; budget for on-site validation.
- **Agentic-framework "benchmarks" cited here are mostly vendor/blog sources**, not peer-reviewed; the _directional_ conclusion (linear task → no framework) is robust across independent sources, but specific percentages and GitHub star counts are soft and drift over time.
- **Local-LLM throughput multipliers and the "3B quality transition" claim are blog/community sourced** — directional, re-measure on your hardware.
- **LoRA data thresholds (~150–200 labels) come from specific tasks** (short-answer grading, image classification) and are order-of-magnitude guidance, not guarantees for RSS triage.
- **Evidential deep learning's epistemic-uncertainty faithfulness is contested** in 2024–2025 literature — prefer conformal prediction for compliance evidence.
- **Some arXiv identifiers above are from 2026 preprints** reflecting the current date (July 30, 2026) and are early-stage/not yet peer-reviewed; verify before citing in formal documentation.
- **Dataset licensing**: MVTec AD and LOCO are **CC BY-NC-SA 4.0 (non-commercial)** — do not use the datasets themselves in a commercial product; the _methods_ are free to use.