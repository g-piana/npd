# Project Charter — Physics-Informed Normal Behavior Model Demo

> Created: 2026-07-31 | Status: `planned`
> Purpose: Public, reproducible demonstration that physics-informed
> residual monitoring produces fewer false alarms than data-driven
> anomaly detection on the same industrial sensor data — specifically
> under seasonal distribution shift.
>
> Parent idea: `ideas/2026-07-pinn-predictive-maintenance.md` (Idea #5)
> This demo is de-risking action #3 from that idea card.

---

## 1. What this demo proves (and what it doesn't)

### Proves

- A simple physics model (lumped-parameter thermal ODE) applied to
  real wind turbine SCADA data produces a residual signal that is
  more stable across operating conditions than raw sensor channels.
- When both detectors are tuned to catch the same known failures with
  comparable lead time, the physics-informed detector generates fewer
  false alarm episodes per turbine-month.
- Conformal prediction (MAPIE) on the physics residual produces
  calibrated alarm thresholds with a stated coverage guarantee.
- The false alarm difference is most visible under **distribution
  shift** (e.g., calibrate on winter, evaluate on summer heat wave).

### Does not prove

- That PINNs or neural operators are needed — this demo uses a
  classical ODE, not a neural network. PINNs are a v2 enhancement
  if the core claim lands.
- That SME manufacturers or OEMs will pay for this — that requires
  Mom Test interviews, not a demo.
- That the approach generalizes to non-thermal failure modes (e.g.,
  vibration, electrical) — each physics domain requires its own model.

---

## 2. Data sources

### Primary: EDP Open Data — Wind Farm SCADA

- **What:** 10-minute SCADA channels from ~5 wind turbines in
  Portugal (2016–2017): power output, rotor speed, generator speed,
  wind speed, nacelle temperature, ambient temperature, bearing
  temperatures, hydraulic pressures, plus operational status codes.
- **Why it's the best primary source:** Includes a **failure log**
  with dates and failure types (gearbox, transformer, hydraulic,
  generator bearing). Labeled failures are rare in public datasets.
- **Where:** Search "EDP Open Data wind" or check Kaggle for mirrors.
  Original dataset published by EDP Renewables.
- **Known issues:** Failure dates in the log may be imprecise (off by
  days or weeks). Validate failure onset against signal anomalies
  manually before using as ground truth.

### Secondary: Penmanshiel and Kelmarsh Wind Farms (Zenodo)

- **What:** Multi-year 10-minute SCADA from ~14 turbines (Cubico /
  University of Strathclyde / Sheffield). Status and event logs.
- **Why:** Larger volume, longer time span, better for seasonal shift
  experiments. Less precise failure labeling than EDP.
- **Where:** Zenodo — search "Kelmarsh wind farm SCADA" or
  "Penmanshiel wind farm SCADA."

### Supplementary: UCI Hydraulic Systems

- **What:** Pressure, temperature, flow, vibration from a hydraulic
  test rig with controlled degradation states (cooler, valve, pump,
  accumulator).
- **Why:** Thermodynamically rich, closer to heat-exchanger physics.
  Good for a second demo closer to manufacturing context.
- **Where:** UCI Machine Learning Repository.

### Supplementary: NASA Prognostics Repository

- **What:** IMS bearing run-to-failure (vibration), C-MAPSS turbofan
  degradation (simulated multi-sensor).
- **Why:** Standard PdM benchmarks. Useful for vibration-focused
  follow-up if the thermal demo lands.
- **Where:** NASA Prognostics Center of Excellence Data Repository.

---

## 3. Pre-requisite learning (Week 0, ~2–3 days)

### Wind domain minimum

- **Power curve:** Relationship between wind speed and power output.
  Everything outside the power curve is either curtailment, fault, or
  measurement error. This is the first filter.
- **SCADA channels:** What each sensor measures, physical units,
  typical ranges. Focus on: gearbox bearing temperature, generator
  bearing temperature, nacelle temperature, ambient temperature,
  rotor speed, power output, wind speed.
- **Status codes:** Operational states (producing, stopped, curtailed,
  maintenance, fault). Critical for data cleaning — you must exclude
  non-producing periods from the anomaly analysis or everything is
  noise.
- **Icing and curtailment:** Two major sources of "false anomalies"
  in raw data. Understand how they appear in the channels.

### Normal Behavior Modeling (NBM) literature

- Skim 2–3 papers on wind turbine NBM to understand state of practice.
  Search: "normal behavior model wind turbine SCADA gearbox
  temperature." Key authors: Tautz-Weinert & Watson (2017 review),
  Bangalore & Tjernberg.
- **Why:** The wind industry already uses data-driven NBMs (linear
  regression, neural network, random forest predicting bearing temp
  from power/speed/ambient). Your differentiator is *physics-informed
  residual + conformal bounds*, not NBM itself. Knowing the prior art
  prevents accidentally claiming novelty where there is none, and
  strengthens the teardown framing.

### Conformal prediction refresher

- MAPIE documentation: regression with conformal intervals.
- Key concept: EnbPI (Ensemble Batch Prediction Intervals) for
  time-series — handles temporal dependence that standard split
  conformal doesn't.

### What to skip for now

- **PINNs / neural operators:** Not on the critical path. The demo
  uses a classical ODE. PINNs are v2 if the claim lands.
- **Deep learning architectures:** The data-driven baseline should be
  deliberately simple (autoencoder or isolation forest) — you're
  representing what typical vendors ship, not trying to build the
  best possible data-driven model.

---

## 4. Project phases

### Phase 1 — Data foundation (Week 1)

**Objective:** Clean, structured SCADA dataset in PostgreSQL with
labeled healthy and failure periods.

**Tasks:**

1. Download EDP Open Data + one Zenodo farm (Kelmarsh or Penmanshiel).
2. Ingest into PostgreSQL. Schema: one row per turbine per timestamp,
   columns for each SCADA channel, status code, and a `period_label`
   column (healthy / pre-failure / failure / maintenance / excluded).
3. Clean using status logs:
   - Exclude curtailed, stopped, and maintenance periods.
   - Flag periods with missing or clearly erroneous readings.
   - For each documented failure, manually identify onset in the
     signal (the failure date in the log is often imprecise — look
     for the point where bearing temperature begins trending up
     relative to load).
4. Select 2–3 turbines with:
   - At least one documented gearbox or bearing failure.
   - At least 6 months of clean healthy data before the failure.
   - Data spanning both winter and summer (for the distribution
     shift experiment).
5. Exploratory plots: power curve, bearing temp vs. power, bearing
   temp vs. ambient, time-series of key channels with failure onset
   marked.

**Output:** `data/` directory with cleaned Parquet files (or
PostgreSQL views), plus a data quality notebook documenting
exclusions, imprecisions, and turbine selection rationale.

**Kill check:** If EDP failure labels cannot be validated against
signal onset (failure dates are too imprecise to define a useful
pre-failure window), pivot to Kelmarsh/Penmanshiel or to the UCI
hydraulic dataset. If no public dataset yields usable labeled
failures after 5 days of effort, pause and reassess.

### Phase 2 — Two detectors (Week 2)

**Objective:** Two anomaly detectors, both targeting gearbox bearing
temperature anomalies, trained on the same healthy data.

#### Detector A: Data-driven baseline

- **Method:** Autoencoder (small feedforward, not convolutional) on
  multivariate SCADA channels, OR isolation forest on the same
  channels. Choose whichever is more representative of what PdM
  vendors actually ship (research this briefly — most mid-market
  tools use gradient boosting or autoencoders, not exotic methods).
- **Training data:** Healthy periods only (no failure, no curtailment).
  Random 70/30 split for train/validation within the healthy window.
- **Anomaly signal:** Reconstruction error (autoencoder) or anomaly
  score (isolation forest).
- **Threshold:** Set to catch the known failure with ≥48 hours lead
  time. Record the threshold and the resulting false alarm rate.

#### Detector B: Physics-informed

- **Step 1 — Lumped thermal model.** Write a first-principles ODE for
  gearbox bearing temperature:

  ```
  C · dT_bearing/dt = Q_friction(power, speed) - h·A·(T_bearing - T_ambient) - Q_oil(T_oil)
  ```

  Where:
  - `C` = effective thermal mass (fitted)
  - `Q_friction` = heat generation from mechanical losses, function
    of power and speed (simplified: proportional to power × friction
    coefficient)
  - `h·A` = convective heat transfer coefficient × surface area
    (fitted)
  - `Q_oil` = oil cooling term, function of oil temperature (may be
    a measured channel or estimated)

  This is a single-ODE model with 3–5 fittable parameters. Fit on
  healthy data using scipy.optimize.minimize against measured bearing
  temperature. The model predicts "what bearing temperature should be
  given the current load, speed, and ambient conditions."

- **Step 2 — Residual.** Anomaly signal = measured bearing temp minus
  physics-predicted bearing temp.

- **Step 3 — Conformal bounds (MAPIE).** Fit conformal prediction
  intervals on the residual during the healthy calibration period.
  The alarm fires when the residual exits the conformal band (e.g.,
  95% coverage). This gives a statistically guaranteed threshold:
  "under normal conditions, the residual stays inside this band 95%
  of the time."

**Threshold:** Same criterion as Detector A — catch the known failure
with ≥48 hours lead time. Record the threshold and false alarm rate.

**Output:** Two anomaly-score time-series per turbine, aligned in
time. A notebook showing both detectors on the same plot with failure
onset marked.

**Kill check:** If the physics model's residual is not substantially
cleaner (lower variance during healthy operation) than the raw signal,
the thermal ODE is too coarse — either add complexity (separate oil
cooling term, wind-speed-dependent convection) or acknowledge that
this failure mode isn't thermal-dominant.

### Phase 3 — The honest comparison (Week 3)

**Objective:** The one chart that is the teardown post.

#### Primary metric: alarm episodes per turbine-month

- Cluster consecutive anomaly flags into episodes (e.g., flags within
  6 hours = one episode). This matches how a maintenance team
  experiences alarms — they don't count individual 10-minute flags.
- Report for each detector:
  - Episodes per turbine-month during **healthy** periods (= false
    alarms).
  - Lead time before failure onset (hours or days of advance warning).
  - Detection rate: did it catch the labeled failure? With how much
    lead time?

#### The distribution shift experiment (the killer chart)

- **Setup:** Calibrate both detectors on winter data (Oct–Feb). Fix
  the thresholds. Evaluate on summer data (Jun–Aug) from the same
  turbine.
- **Hypothesis:** The data-driven detector's false alarm rate
  increases substantially in summer (hot days push bearing
  temperatures up even under normal operation). The physics-informed
  detector's residual remains stable because the thermal model
  accounts for ambient temperature.
- **Chart:** False alarm episodes per month, winter vs. summer, for
  both detectors. Side-by-side bar chart or line chart over months.
  This is the visual that makes the teardown post.

#### Secondary: conformal coverage validation

- On held-out healthy data, verify that the MAPIE conformal band
  achieves the stated coverage (e.g., 95% of healthy residuals fall
  inside the band). If it doesn't, exchangeability is violated —
  report honestly and discuss why (trend, seasonality, non-
  stationarity).

**Output:** A comparison table and the distribution-shift chart. A
narrative interpreting the results in plain language.

### Phase 4 — Public deliverables (Week 3–4)

1. **GitHub repo** (public, under your account or RE:MARK).
   Structure:
   ```
   physics-nbm-demo/
   ├── README.md              # Summary, claim, results, how to run
   ├── data/                  # Scripts to download/clean (not raw data)
   ├── notebooks/
   │   ├── 01_data_prep.ipynb
   │   ├── 02_detectors.ipynb
   │   └── 03_comparison.ipynb
   ├── src/
   │   ├── thermal_model.py   # The lumped ODE
   │   ├── baseline.py        # Autoencoder or isolation forest
   │   └── conformal.py       # MAPIE wrapper
   └── results/
       ├── figures/           # The chart(s)
       └── metrics.json       # Raw numbers
   ```

2. **LinkedIn post** in STYLE.md format:
   - The vendor claim ("95% anomaly detection accuracy")
   - The honest number (false alarm episodes per turbine-month —
     the metric nobody publishes)
   - The mechanism explained plainly (data-driven models can't
     distinguish "hot" from "hotter than physics allows")
   - Three checkable questions for anyone evaluating PdM
   - Non-moralizing close
   - Link to the repo

3. **The chart** — the single distribution-shift comparison
   visualization. This is the artifact that travels.

---

## 5. Tools and libraries

| Purpose | Tool | Notes |
|---------|------|-------|
| Data storage | PostgreSQL | Already in stack |
| Data manipulation | Pandas, NumPy | Standard |
| ODE solving | SciPy (solve_ivp, minimize) | Lumped thermal model |
| Data-driven baseline | scikit-learn (IsolationForest) or PyTorch (small autoencoder) | Keep simple |
| Conformal prediction | MAPIE (MapieTimeSeriesRegressor or MapieRegressor) | EnbPI for time-series |
| Time-series features | STUMPY (optional) | Matrix profile on residuals as bonus analysis |
| Plotting | Matplotlib, Seaborn | Publication-quality charts |
| Notebooks | Jupyter | Reproducibility |
| Version control | Git / GitHub | Public repo |

---

## 6. Success criteria

### Demo succeeds if

- The physics-informed detector catches ≥ the same failures as the
  data-driven detector with comparable lead time (≥48 hours).
- The physics-informed detector produces **≤50% of the false alarm
  episodes** of the data-driven detector across the full evaluation
  period.
- The distribution-shift experiment shows a **visible divergence**:
  data-driven false alarms increase in summer; physics-informed
  false alarms stay flat.
- The MAPIE conformal band achieves within ±3% of stated coverage
  on held-out healthy data.
- The repo is runnable by a third party (documented, scripted data
  download, no manual steps).

### Demo fails if

- The lumped thermal model cannot fit healthy bearing temperature to
  within ±5°C RMSE — the physics is too coarse for this data.
- Both detectors produce comparable false alarm rates (the physics
  layer adds no value over data-driven on this dataset).
- The distribution shift experiment shows no meaningful divergence
  (seasonal effects are already captured by the data-driven model
  because ambient temperature is an input feature).
- Failure labels in the dataset are too imprecise to define a
  meaningful detection-lead-time comparison.

### If the demo fails

- If it fails on physics model quality: consider a more detailed
  thermal network (multi-node) or pivot to the UCI hydraulic dataset
  where the physics is cleaner.
- If it fails on false-alarm differentiation: the positioning claim
  ("physics kills false alarms") is weaker than assumed. Reframe
  around data efficiency or generalization instead, or reassess
  whether idea #5 has the differentiation claimed.
- Document the failure honestly in the repo. A failed experiment
  published transparently is more credible than a cherry-picked
  success, and aligns with your teardown ethos.

---

## 7. Risk register

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| EDP failure labels too imprecise | Medium | High — comparison meaningless without ground truth | Validate onset manually against signals; have Kelmarsh/UCI as fallbacks |
| Lumped thermal model too simplistic | Medium | Medium — poor fit means noisy residual | Multi-node thermal network; oil cooling as separate node |
| Data-driven baseline too weak (strawman) | Medium | High — credibility destroyed if wind engineers spot it | Research what vendors actually ship; use isolation forest on same features the physics model uses (power, speed, ambient) |
| Seasonal shift not visible in Portuguese data (mild climate) | Low-medium | Medium — kills the best chart | Kelmarsh (Scotland) has more extreme seasons |
| Scope creep into PINNs/neural operators | Medium | Medium — delays by weeks | Strictly out of scope for v1. Classical ODE only. |
| Over-engineering the repo | Medium | Low — perfectionism delay | Ship when the chart is clear and the notebooks run. Polish later. |

---

## 8. Timeline

| Week | Focus | Deliverable |
|------|-------|-------------|
| 0 | Domain learning, literature scan, data download | Reading notes, data access confirmed |
| 1 | Data cleaning, turbine selection, exploratory analysis | Clean dataset in PostgreSQL/Parquet, data quality notebook |
| 2 | Build both detectors, initial comparison | Two anomaly-score time-series, overlay plot |
| 3 | Distribution shift experiment, conformal validation, chart | The comparison chart, metrics table |
| 3–4 | Repo cleanup, README, LinkedIn post draft | Public repo, post draft |

**Total estimated effort:** 3–4 weeks at ~15 hours/week.

---

## References

- Tautz-Weinert & Watson (2017), "Using SCADA data for wind turbine
  condition monitoring — a review" (IET Renewable Power Generation)
- MAPIE documentation: mapie.readthedocs.io
- EDP Open Data: search Kaggle "EDP wind turbine SCADA"
- Kelmarsh/Penmanshiel: Zenodo datasets by Cubico/Strathclyde
- UCI Hydraulic Systems: archive.ics.uci.edu
- NASA Prognostics: ti.arc.nasa.gov/tech/dash/groups/pcoe/
- `references/PINN.md` in this repo (for v2 upgrade path)
- `ideas/2026-07-pinn-predictive-maintenance.md` (parent idea card)
