# Sovereign Veracity — Method & harness design

This document describes **how** pilots are built and how lab services map into the judge set.  
**Current results:** [`FINDINGS-PILOT-10.md`](./FINDINGS-PILOT-10.md) · [`AGENT_SYNOPSIS.md`](./AGENT_SYNOPSIS.md) · [`README.md`](./README.md).

---

## Design goals

1. **Local-first** — no cloud LLM APIs required for generation or eval (lab LAN models OK).
2. **Machine ground truth** — avoid expensive, noisy human labeling of the whole corpus.
3. **Failure taxonomy** — report recall by corruption type and category, not only aggregate accuracy.
4. **Resumable eval** — long local GPU/CPU runs survive interrupt.
5. **Honest prevalence** — report precision at 3/5/8% confabulation rate, not the artificial 50/50 split.
6. **Fail-closed ship floors** — pre-register targets; do not rewrite GT to flip a MISS into PASS.
7. **Independent evidence** — det gates + LLM + ensembles; empty/skip-only suites must not green-flag.

---

## Ground-truth generation

```text
seed documents (seeds/*.txt)
        │
        ▼
  extractive / policy-clean builders (per category)
        │
        ├─► clean case (ground_truth = clean)
        │     optional: paraphrase clean_mode (exact reformats only)
        │
        └─► + deterministic corruption(s)
              └─► confabulated case (ground_truth + failure_type [+ multi_corrupt meta])
```

### Categories

| Category | Clean construction (sketch) |
|----------|-----------------------------|
| claim_verification | Source paragraph; candidate = verbatim claim sentence |
| summarization | Source = seed; candidate = extractive lead + sampled sentence |
| data_extraction | Candidate = key-value lines of numbers found in source |
| reasoning_numeric | Source holds two values; candidate states a derived difference |
| **code_review** (v0.2+) | Source = fenced/synthetic code unit; candidate starts as exact copy |

### Corruptions (`failure_type`)

| Type | Intent |
|------|--------|
| number_perturb | Alter a numeric token |
| negation_flip | Flip polarity (not / never / etc.) |
| entity_swap | Swap named entities |
| fabricated_detail | Insert unsupported plausible sentence |
| unsupported_generalization | Strengthen hedges (may→will, some→all, …) |
| date_shift | Shift a year / date token |
| **off_by_one** (code) | ±1 on a small integer in code-like context |
| **condition_flip** (code) | Flip comparison op or code `if`/`while` (not English “if needed”, not markdown `>`) |

**v0.2+:** optional **multi-corrupt** confabs (~20%): two deterministic faults; primary `failure_type` + meta list.  
**v0.2+:** **multi-voice** seed meta when source contains opposing voices (product policy: restating either voice can be clean).

Reproducibility: each case stores `rng_seed`, `seed_hash`, `generator_version`.  
**Corpus linter** runs before GPU spend (invalid “clean” paraphrases, IP/model-name word-number bugs, etc.).

---

## Product policy (must match labels)

Full doctrine: lab `PRODUCT_POLICY.md` (excerpted in harness). Critical points:

- Faithfulness is to the **source as written**, not a reconciled world model.  
- **Multi-voice:** accurate restatement of either voice is clean; third-claim fabrication is confab.  
- **Omission** alone is not confabulation.  
- **Numbers:** exact value or exact derivation; approximated figures that introduce new digit sequences are confab by product choice.  
- **Derived arithmetic:** correct A−B from source operands is clean (prompt + GT aligned).

---

## Lab service mapping

Judges are **pluggable**; hosts differ by pilot era.

### Current (pilot-09 / pilot-10 family)

| Config name | Typical lab service | Harness type |
|-------------|---------------------|--------------|
| `spark_veracity` | Spark / vLLM OpenAI-compatible (e.g. Qwen3.6-27B) | `openai` |
| `numeric_gate` | In-process Python | `numeric` |
| `date_gate` | In-process Python | `date` |
| `negation_gate` | In-process Python | `negation` |
| `extractive_gate` | In-process Python | `extractive` |
| `ship_stack` / `gated_ship` | Derived after sweep | ensemble materialize |

### Historical (pilot-01 / pilot-02)

| Config name | Typical Node2-side service | Harness type |
|-------------|----------------------------|--------------|
| `node2_critic` | Ollama `patronus-lynx` | `ollama` |
| `hhem_gate` | HHEM FastAPI groundedness | `hhem` |
| `secondary_judge` | Larger local instruct model | `ollama` |
| `numeric_gate` | In-process Python | `numeric` |
| `ship_stack` / `precision_stack` | Derived ensembles | ensemble |

**Convention:** primary LLM under test is named in config; ensembles are **synthetic rows** written after the sweep (`materialize_ensembles`), not extra LLM calls.

### Critic I/O (Spark / Lynx-class)

- Framing: source + candidate (product prompt version pinned by hash).  
- Expected: structured reasoning + PASS/FAIL-style verdict (parser tolerates semi-JSON).  
- Pilot-10 freeze: prompt **v5.4** / hash **`0105f91470e7`**.

### HHEM API shape (when used)

```http
POST /check
{
  "pairs": [{"premise": "<source>", "hypothesis": "<candidate>"}],
  "threshold": 0.5
}
→ { "results": [{ "score": 0.0-1.0, "grounded": bool }] }
```

---

## Ship plane (current)

```text
gated_ship:
  if extractive_gate says force-clean → CLEAN
  else CLEAN only if Spark and all det_gates agree CLEAN
  else CONFAB if Spark OR any det_gate says CONFAB
```

Det gates are **high-confidence / low-FP by construction** where possible (substring recovery, bag support, structure-aware arithmetic). LLM covers residual semantics.

---

## Evaluation harness

| Module | Role |
|--------|------|
| `generator.py` | Build corpus → SQLite + JSONL |
| `corpus_linter.py` | Pre-eval corpus validity (§0) |
| `evaluator.py` | Resumable judge sweep + ensemble materialize |
| `judges.py` | LLM + deterministic judges |
| `report.py` | Per-judge metrics + prevalence precision |
| `shadow_server.py` | Phase-1 log-only production observation path |

Export helper for this package: `sv-testgen/_export_benchmarks.py` (point at the pilot DB / run_id).

---

## Ship discipline

1. Fix floors **before** reading metrics.  
2. New prompt or model → **new run_id**.  
3. Do not re-label GT post-hoc to flip ship decision.  
4. Soft spots (e.g. code_review near-copies) are **documented**, then fixed on a **new** generator/gate version without rewriting frozen pilots.  
5. This metrics package does **not** include raw eval DBs with private seeds.

---

## Version timeline (public)

| Generator | Prompt | Notable | Ship? |
|-----------|--------|---------|:-----:|
| 0.1.x era | Lynx SCORE prompts | pilot-01/02 Node2 stacks | historical |
| 0.1.x→Spark | evolving | pilot-03…06 path (extractive, gated planes, Spark primary) | diagnostic |
| **0.1.6** | **v5.3** | pilot-09 A bare + det_gates | **YES** |
| **0.2.0** | **v5.4** | pilot-10 multi-voice + code_review | **YES** |
| **0.2.1** (lab) | v5.4 family | code flip hygiene + gate HC (not re-score of pilot-10) | — |

All exported runs under `runs/` are listed in `index.json` and the README timeline.

---

*Method first, marketing second. Prefer humility when citing.*
