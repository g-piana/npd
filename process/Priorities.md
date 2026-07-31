### 1. The failure dates: you never need to "adjust" them

The key methodological point is that the failure dates are not training ground truth. Both detectors train **unsupervised, on healthy data only** — the model variables are exactly what you suspected: temperatures, power, speed, ambient conditions. The failure log plays only two roles, and neither requires editing it:

**Role 1 — excluding contaminated data from training/calibration.** Here you don't adjust the date; you declare a conservative buffer. "We excluded the 60 days preceding each logged failure from training, calibration, _and_ false-alarm counting, because failure onset timing is uncertain." That's a disclosed methodological choice, standard practice in the condition-monitoring literature, and it's conservative in the right direction — uncertain periods count for neither side. Nothing is tampered with; the logged dates appear on charts exactly as logged.

**Role 2 — scoring detection lead time.** This is where label imprecision genuinely bites, so demote it. Report it honestly and secondarily: "both detectors showed sustained divergence beginning N days before the logged failure date; the log itself carries uncertainty of days to weeks." Symmetric across both detectors, so the _comparison_ survives even if the absolute lead time is fuzzy.

And here's the part that dissolves the skeptical-client problem entirely: **your headline chart doesn't use failure labels at all.** The distribution-shift experiment — calibrate both detectors on winter, count false alarm episodes on summer, on turbines that stayed healthy throughout — is 100% label-free. The claim is "same healthy machine, same thresholds, hot season: the data-driven detector cried wolf 9 times per turbine-month, the physics-informed one twice." No ground truth needed beyond "this turbine didn't fail," which the log establishes coarsely and reliably. Lead with that chart; let detection lead-time be a supporting observation. The demo charter should be reordered accordingly — the label-free result is primary, not the killer bonus. I'd update §4/Phase 3 of the charter to reflect that inversion when you commit it.

### 2. Is "fewer false alarms" headache #1? Honest answer: no — but it ladders into headache #1

For a major wind turbine OEM, the top-of-mind crises are margin collapse, warranty provisions, and supply chain — Siemens Gamesa-scale problems. "Fewer false alarms" pitched cold sounds like a nice-to-have. But false alarms are a _component_ of their actual #1 — service margin on O&M contracts — so the pitch must ladder: false alarms → unnecessary truck rolls + alarm fatigue → missed real failures → service margin erosion and warranty exposure. You sell margin protection; false alarms are the mechanism and the demo's proof point.

One more honesty check on wind specifically: tier-1 wind OEMs (Vestas, SGRE, GE) have large in-house condition-monitoring and NBM teams — you'd face build-vs-buy against people who understand the problem. The receptive wind segment is more likely **owner-operators, independent service providers, and second-tier OEMs** without that in-house depth.

Back-of-envelope value pools — all order-of-magnitude, to be replaced by interview data:

|Segment|Cost per false alarm episode|Rough frequency|Value pool (mid-size fleet)|Receptivity guess|
|---|---|---|---|---|
|**Onshore wind** (500-turbine owner/ISP)|€300 remote triage; €1–2k if site visit|5–10 alerts/turbine-yr, majority false|€1–3M/yr, plus 1–2 prevented missed gearbox failures ≈ €0.7M (€350k each)|Medium — sophisticated buyers, crowded|
|**Offshore wind** (100 turbines)|€10–20k per unnecessary vessel visit (CTV/SOV day rates)|Even 1/turbine-yr|€1–2M/yr on visits alone|High — per-incident cost is brutal|
|**Compressor OEM** (10,000 units under service contracts)|€500–1,500 truck roll|~0.5 false-alarm visits/unit-yr|€2.5–7M/yr, plus uptime-contract penalty avoidance|High — service is their profit engine, thermodynamics well-defined, less in-house data science|
|**Chiller / heat-exchanger OEM**|N/A — different mechanism|Fouling drift: 5–10% energy penalty|€10–50k/site-yr in wasted energy × fleet|High, but requires **reframing to efficiency drift**, not false alarms|
|**Pump OEMs** (large fleets, cheap units)|€500 truck roll|Low per-unit stakes|Thin per-unit economics|Low unless critical-service pumps|

Two strategic readings from this table. First, the value pools (€1–7M/yr per mid-size fleet) comfortably support your €500–2k/month/site pricing under any reasonable capture assumption — the economics aren't the problem; _salience_ is, which is a positioning and interview problem. Second, the same physics-residual engine supports **two distinct value propositions**: "fewer false alarms" (wind, compressors — cost of responding) and "efficiency drift detection" (heat exchangers, chillers — cost of _not_ responding, amplified by EU energy prices and efficiency directives). The second may actually be easier to sell because energy waste is continuous, measurable in euros per month, and requires no failure to ever occur to prove value. That's worth adding to the beachhead open question in the idea card: the heat-exchanger path might enter through the energy-efficiency door rather than the maintenance door.

The interviews arbitrate all of this — these numbers tell you _who to interview first_ (compressor OEM service directors and offshore wind O&M managers ahead of tier-1 wind OEMs), not what to conclude.