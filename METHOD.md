# Sovereign Veracity — Method & harness design

This document describes **how** the pilots were built and how Node2 maps into the judge set. For results tables, see [`AGENT_SYNOPSIS.md`](./AGENT_SYNOPSIS.md) and [`README.md`](./README.md).

---

## Design goals

1. **Local-first** — no cloud LLM APIs required for generation or eval.
2. **Machine ground truth** — avoid expensive, noisy human labeling of the whole corpus.
3. **Failure taxonomy** — report recall by corruption type, not only aggregate accuracy.
4. **Resumable eval** — long local GPU/CPU runs survive interrupt.
5. **Honest prevalence** — report precision at 3/5/8% confabulation rate, not the artificial 50/50 split.

---

## Ground-truth generation

```text
seed documents (seeds/*.txt)
        │
        ▼
  extractive clean builders (per category)
        │
        ├─► clean case (ground_truth = clean)
        │
        └─► + one deterministic corruption
              └─► confabulated case (ground_truth + failure_type)
```

### Categories

| Category | Clean construction (sketch) |
|----------|-----------------------------|
| claim_verification | Source paragraph; candidate = verbatim claim sentence |
| summarization | Source = seed; candidate = extractive lead + sampled sentence |
| data_extraction | Candidate = key-value lines of numbers found in source |
| reasoning_numeric | Source holds two values; candidate states a derived difference |

### Corruptions (`failure_type`)

| Type | Intent |
|------|--------|
| number_perturb | Alter a numeric token |
| negation_flip | Flip polarity (not / never / etc.) |
| entity_swap | Swap named entities |
| fabricated_detail | Insert unsupported plausible sentence |
| unsupported_generalization | Strengthen hedges (may→will, some→all, …) |
| date_shift | Shift a year token |

**MVP limits:** one corruption per case; template-based edits; number_perturb over-represented in numeric categories (inherent).

Reproducibility: each case stores `rng_seed`, `seed_hash`, `generator_version`.

---

## Node2 mapping

In the Kit / Sovereign lab, **Node2** is the verification-oriented host (Ollama + optional HHEM service). The harness treats Node2 as **pluggable judges**, not a monorepo dependency:

| Config name | Typical Node2-side service | Harness type |
|-------------|----------------------------|--------------|
| `node2_critic` | Ollama `patronus-lynx` (faithfulness critic) | `ollama` |
| `hhem_gate` | HHEM FastAPI groundedness | `hhem` |
| `secondary_judge` | Larger local model (often workstation, not Node2) | `ollama` |
| `numeric_gate` | In-process Python | `numeric` |

**Convention:** first configured LLM under test = primary critic (Node2 subject). Ensembles are **derived rows** written after the sweep (`materialize_ensembles`).

### HHEM API shape (lab daemon)

```http
POST /check
{
  "pairs": [{"premise": "<source>", "hypothesis": "<candidate>"}],
  "threshold": 0.5
}
→ { "results": [{ "score": 0.0-1.0, "grounded": bool }] }
```

Higher score → more grounded. Default: score ≥ threshold ⇒ clean.

### Lynx critic I/O

- Prompt framing: QUESTION / DOCUMENT / ANSWER (Lynx-aligned).
- Expected model output: `REASONING` + `SCORE` ∈ {PASS, FAIL}.
- Production models often emit **semi-JSON** (`SCORE: PASS` unquoted). Parser accepts SCORE/verdict synonyms and bare PASS/FAIL.
- Pilot-02 prompt adds: omission allowed; hedges material; numbers exact or correct simple arithmetic; completeness not required.

### Qwen3-class secondary

If the model uses a thinking channel, set `think: false` (or equivalent) so content is non-empty; otherwise the harness falls back to the thinking field when needed.

---

## Evaluation harness

| Module | Role |
|--------|------|
| `generator.py` | Build corpus → SQLite + JSONL |
| `evaluator.py` | Sweep judges; resume by `(run_id, case_id, judge_name)` |
| `judges.py` | Adapters: ollama, hhem, numeric, mock |
| `report.py` | Acc / FPR / FNR / prec@π / per category & failure type / ECE |
| `schema.sql` | `test_cases`, `eval_results`, `manual_review` |

**Manual review queue:** disagreements (primary vs secondary) + random agreement-audit sample (default 300) to bound correlated error. Synthetic labels remain authoritative for pilot metrics.

### Ensemble definitions (pilot-02)

| Name | Rule (confabulated if…) |
|------|-------------------------|
| `ensemble_or` | Node2 **or** secondary |
| `ensemble_and` | Node2 **and** secondary |
| `ship_stack` | Node2 **or** secondary **or** numeric |
| `precision_stack` | numeric **or** (Node2 **and** secondary) |

---

## Metrics definitions

- **TP / FN / FP / TN** — positive class confabulated.  
- **Recall (TPR)** — TP / (TP+FN).  
- **FPR** — FP / (FP+TN).  
- **Precision @ prevalence π** —  
  \( \frac{\pi \cdot \mathrm{TPR}}{\pi \cdot \mathrm{TPR} + (1-\pi)\cdot \mathrm{FPR}} \)  
- **ECE** — 10-bin confidence calibration when confidence is present.

---

## What this method does *not* claim

- Multi-domain production prevalence without re-seeding.  
- Node2-alone ship readiness (see pilot tables).  
- That template corruptions equal organic confabulations.  
- That results transfer without re-running on your seeds.

---

## Extending (agents)

1. Drop diverse `.txt` into `seeds/` (20–50 real docs preferred).  
2. `generator.py --target N` with a new DB or clean slate.  
3. Keep ship targets fixed **before** reading metrics.  
4. New `run-id` per prompt/model change.  
5. Re-export: `_export_benchmarks.py` → refresh this package.
