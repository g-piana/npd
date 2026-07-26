# NPD Process — Asset-Constrained, Demand-Triggered, Combinatorial Pipeline

> **Purpose:** A repeatable ideation-to-validation process designed for a
> solo technical founder with deep domain expertise (physics PhD, 15yr
> medtech), a specific tech stack, and a bias toward regulatory-driven
> "why now" opportunities in digital products (SaaS, web-app, edge).

---

## Design principles

1. **Start from assets, not abstractions.** Effectuation (Sarasvathy):
   begin with what you have — domains, capabilities, network — and discover
   opportunities at intersections others can't see.
2. **Demand before supply.** Every idea must trace back to a dated forcing
   function (regulation, cost threshold, workflow pain). No trigger, no idea.
3. **Combinatorial creativity.** Differentiation comes from combining 2+
   capabilities (domain + technical) in ways a pure-software or pure-domain
   person wouldn't. Force the pairing; don't wait for inspiration.
4. **Kill early, kill cheap.** Evidence before code. Interviews before
   landing pages. Landing pages before prototypes.

---

## Stage 1 — Asset inventory

**Frequency:** Do once, refresh quarterly.

Maintain three lists in `asset-inventory.md`:

- **Domain access:** Industries where you have insider knowledge and
  network (medtech workflows, hospital procurement, quality systems,
  physics-heavy engineering, defense/aerospace where no certification
  is required to sell).
- **Deployable capabilities:** Tech you can ship in weeks (Python ecosystem,
  Django/DRF, FastAPI, HTMX, PostGres, PyTorch/CNNs, Nvidia Jetson,
  Arduino, applied math / physics modeling).
- **Adjacent capabilities:** Learnable in reasonable time (LLM/SLM,
  LoRA fine-tuning, agentic architectures, graph modeling on PostGres).

This is the search space. Everything downstream is filtered through it.

---

## Stage 2 — Demand-trigger scanning

**Frequency:** ~2 hours/week, continuous.

Monitor three categories of forcing functions, in order of reliability:

### 2a. Regulation with deadlines (strongest "why now")

EU regulations create dated, mandatory, budgeted demand. Priority sources:

- EUR-Lex, EU Commission "Have Your Say" portal (see regulation coming)
- National competent authority guidance
- Key regulations to track: EU AI Act (phased 2025-2027), EHDS, Cyber
  Resilience Act, NIS2, Data Act, CSRD, MDR/IVDR ongoing pain points

### 2b. Cost/capability step-changes

When technology crosses a price or performance threshold, previously
uneconomical problems become solvable. Sources:

- arXiv (cs.LG, eess.SP sections)
- NVIDIA developer blog, Hugging Face trending
- Hacker News, key technical newsletters

### 2c. Workflow pain in your network

Jobs-to-be-Done interviews with former colleagues, hospital contacts,
quality/regulatory people. Listen for workarounds — spreadsheets, manual
double-checks, "the intern does it." Workarounds are proof of demand.

**Output:** Entries in `scanning/opportunity-log.md`.

**Key references:** Rob Fitzpatrick, *The Mom Test*; Bob Moesta,
*Demand-Side Sales 101*.

---

## Stage 3 — Combinatorial ideation

**Frequency:** Monthly, or when opportunity log has 5+ fresh entries.

### Method: Forced-pairing matrix

- **Rows:** Entries from opportunity log
- **Columns:** Capabilities from asset inventory (Stage 1)
- **For each promising cell ask:** "What product exists at this
  intersection that a pure software person *couldn't* build and a pure
  domain person *wouldn't* think of?"

### Creativity techniques

- **Analogical transfer:** Import a solved problem's structure from
  physics/math into a new domain (e.g., inverse-problem methods applied
  to industrial data, signal-processing techniques for anomaly detection).
- **TRIZ contradiction resolution:** Find where the market accepts a
  trade-off (e.g., "accurate but slow") and break it (e.g., with edge
  inference).
- **Minimum combination rule:** Every candidate must combine at least
  two elements — one domain insight + one technical capability.

**Output:** Idea cards in `ideas/` using the `_template.md` structure.

---

## Stage 4 — Differentiation and moat test

For each idea card, complete section 3 of the template.

### Positioning exercise (April Dunford, *Obviously Awesome*)

- Who are the **true competitive alternatives**? (Often "Excel + manual
  process," not another startup.)
- What can you claim that they **structurally cannot**?

### Moat sources, ranked for solo technical founder

1. Proprietary domain knowledge (hardest to copy)
2. Data flywheels (product generates its own training data)
3. Physics/math depth in the core algorithm
4. Regulatory-adjacency know-how (understand the regulated world without
   needing certification yourself)

### Kill signal

If the only differentiation is "better UX" or "AI-powered" — kill it.
Those are features, not moats.

---

## Stage 5 — Revenue line of sight

Complete section 4 of the idea card. Four questions, one page max:

1. **Who signs the purchase order?** (Job title, not persona.)
2. **What budget line?** (Compliance budgets = easiest; "innovation" = worst.)
3. **What's the cost of not buying?** (Fines, audit failure, downtime,
   FTE cost of workaround.)
4. **Can you reach 10 of these buyers through your existing network?**

If any answer is vague → back to Stage 2 for more scanning.

---

## Stage 6 — Cheap de-risking

Sequence matters. Do not skip steps:

1. **5-10 Mom-Test interviews** — Validate the problem exists and is
   painful enough to pay for.
2. **Message test** — Landing page or one-pager to test whether your
   positioning resonates.
3. **Concierge / Wizard-of-Oz** — Deliver the value manually or
   semi-manually to prove willingness to pay.
4. **Code spike** — Only now build. With Django/FastAPI/HTMX, this can
   be days, not months.

Complete section 5 of the idea card: riskiest assumption, next 3 actions,
and explicit kill criteria.

**Key reference:** Teresa Torres, *Continuous Discovery Habits*.

---

## Excluded sectors

Per design constraints: legal, customer service, accounting, finance.

## Certification constraint

Ideas in medtech or defense/aerospace are valid only if the product
itself does **not** require regulatory certification (e.g., MDR/IVDR
device classification) for the founder to sell it. Compliance tooling,
data infrastructure, and monitoring/analytics products typically qualify.

---

## Key references

| Book | Used in stage |
|------|--------------|
| Sarasvathy, *Effectuation* | 1 (asset-first thinking) |
| Fitzpatrick, *The Mom Test* | 2c, 6 (interviews) |
| Moesta, *Demand-Side Sales 101* | 2c (demand triggers) |
| Dunford, *Obviously Awesome* | 4 (positioning) |
| Torres, *Continuous Discovery Habits* | 6 (weekly loops) |
| Ries, *The Lean Startup* | 6 (build-measure-learn) |
