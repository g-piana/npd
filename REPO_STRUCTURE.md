# NPD Repository — Recommended Structure

```
npd/
├── README.md                        # Project overview and quick-start
│
├── process/
│   ├── process.md                   # The NPD process (operational reference)
│   ├── asset-inventory.md           # Stage 1: your capabilities, domains, stack
│   └── evaluation-criteria.md       # Stage 4-5: scoring rubric for ideas
│
├── scanning/
│   ├── opportunity-log.md           # Running log of demand triggers
│   ├── regulation-tracker.md        # EU/national regulation timelines
│   └── tech-shifts.md               # Capability step-changes worth watching
│
├── ideas/
│   ├── _template.md                 # Blank idea card (copy for each new idea)
│   ├── 2026-07-ehds-deidentification.md   # One file per idea, dated
│   └── 2026-07-cra-edge-sbom.md
│
├── knowledge/
│   ├── medtech/                     # Domain-specific references
│   │   ├── ehds-regulation-summary.pdf
│   │   └── mdr-post-market-surveillance.pdf
│   ├── defense-aerospace/
│   ├── ml-cv/                       # Technical references
│   ├── edge-computing/
│   └── data-engineering/
│
├── validation/
│   ├── interviews/                  # Mom-Test interview notes (anonymized)
│   │   └── _interview-template.md
│   ├── experiments/                 # Landing page tests, concierge results
│   └── competitor-snapshots/        # Point-in-time competitive analyses
│
└── resources/
    ├── books.md                     # Reading list with notes
    └── tools-and-sources.md         # Scanners, feeds, databases you use
```

## Why this structure

**Not by stage** — Stages are a sequence you walk through, not folders you
fill. An idea born in Stage 3 gets validated in Stage 6 and you'd constantly
cross-reference. Organizing by content type means every artifact has one
obvious home regardless of which stage produced it.

**`knowledge/` by domain** — You'll accumulate PDFs, papers, and notes.
Organizing by domain (medtech, defense, ML, etc.) keeps them findable and
maps directly to your Stage 1 asset inventory. When scanning triggers a
new domain of interest, add a subfolder.

**`ideas/` as flat dated files** — Ideas are cheap; most will die. A flat
folder with date-prefixed names lets you see volume and recency at a glance.
Each file follows the `_template.md` structure (trigger → pairing → moat →
buyer → status).

**`validation/` separate from `ideas/`** — Interview notes and experiment
results are evidence; keeping them apart from the idea cards avoids bloating
the idea files and lets you cross-reference one interview across multiple
ideas.

**`scanning/` as the engine room** — The opportunity log, regulation tracker,
and tech-shifts file are living documents you update weekly. They feed
everything downstream.
