# Sovereign Veracity Critic — Pilot-02 Results (n=2000)

**Open method, harness design, baselines, failure analysis, and ensemble backtests for a local-first veracity stack (Node2-based).**

| Stack | Best for | Recall | FPR | Prec@5% |
|-------|----------|--------|-----|---------|
| **precision_stack** | Gating when false flags are costly | 0.944 | **0.000** | **1.000** |
| **ship_stack** | Max catch rate when you can absorb flags | **0.989** | 0.075 | 0.410 |

Full details, caveats (single-seed corpus, prompt iteration effects), and failure slices: **[`AGENT_SYNOPSIS.md`](./AGENT_SYNOPSIS.md)** — built for sovereign agents: fork, extend, improve.

---

## Start here

| Reader | Entry |
|--------|--------|
| **Agents / automation** | [`AGENT_SYNOPSIS.md`](./AGENT_SYNOPSIS.md) |
| **Method & Node2 mapping** | [`METHOD.md`](./METHOD.md) |
| **Machine index** | [`index.json`](./index.json) |
| **Per-run tables + JSON** | [`runs/`](./runs/) |

---

## What this is

A **local, zero-cloud-API** evaluation package for faithfulness / confabulation critics used with a sovereign Node2-style stack:

- Ground truth is **generated**, not hand-labeled: extractive clean cases + one deterministic corruption per confabulated case.
- Pilots measure **Node2 critic** (`patronus-lynx`), **HHEM**, a **secondary LLM judge**, a **deterministic numeric gate**, and **ensembles**.
- Positive class = **confabulated**. Precision is reported at realistic prevalence (3/5/8%), not the artificial 50/50 corpus split.

**Harness source** (sibling in this monorepo layout): `sv-testgen/`. This package is metrics + method + agent docs—not a dump of evaluation databases.

---

## Headline results

### Pre-registered ship targets

| Metric | Target |
|--------|--------|
| Recall | ≥ 0.95 |
| FPR | ≲ 0.012 |
| Precision @ 5% prevalence | ≥ 0.80 |

### Pilot-01 — baseline prompts (n=2000, 0 errors)

*Corpus note: single synthetic steel brief seed for all 2000 cases (same seed as pilot-02).*

| Judge | Acc | Recall | FPR | Prec@5% |
|-------|-----|--------|-----|---------|
| node2_critic (patronus-lynx) | 0.933 | 0.901 | 0.034 | 0.582 |
| secondary_judge (qwen3.5:35b) | 0.977 | 0.954 | 0.000 | 1.000 |
| hhem_gate | 0.741 | 0.731 | 0.250 | 0.133 |

### Pilot-02 — hardened prompt v2 + numeric + ensembles (n=2000, 0 errors)

| Judge / stack | Acc | Recall | FPR | Prec@5% |
|---------------|-----|--------|-----|---------|
| node2_critic (v2) | 0.897 | 0.869 | 0.074 | 0.382 |
| secondary_judge | 0.987 | 0.975 | 0.001 | 0.981 |
| numeric_gate | 0.804 | 0.608 | 0.000 | 1.000 |
| hhem_gate | 0.741 | 0.731 | 0.250 | 0.133 |
| **ship_stack** (N2 ∨ sec ∨ numeric) | 0.957 | **0.989** | 0.075 | 0.410 |
| **precision_stack** (numeric ∨ (N2 ∧ sec)) | **0.972** | 0.944 | **0.000** | **1.000** |

### Recommendation

- **Production gating (prefer few false flags):** `precision_stack` — excellent FPR/precision; recall 0.6pp under the 0.95 bar on this corpus.
- **Max recall (can triage flags):** `ship_stack` — 0.989 recall; FPR inherits Node2 false positives.
- **Do not claim** Node2 alone as production-ready on these pilots (pilot-02 FPR 0.074, recall 0.869).

---

## Unsupported generalization (key failure slice)

Hedge upgrades (`may` → `will`) were the hardest class under pilot-01.

| Judge / stack | Pilot-01 | Pilot-02 |
|---------------|----------|----------|
| node2_critic | 0.482 | **0.857** |
| secondary_judge | 0.446 | **1.000** |
| ship_stack | — | **1.000** |
| precision_stack | — | 0.857 |

Prompt hardening helped modality; ensembles close remaining gaps differently (recall vs precision).

---

## Runs on disk

| Run | n | Description |
|-----|---|-------------|
| [`runs/pilot-01/`](./runs/pilot-01/) | 2000 | Baseline prompts; Node2 + secondary + HHEM |
| [`runs/pilot-02/`](./runs/pilot-02/) | 2000 | Hardened prompt + numeric_gate + ensembles |
| [`runs/pilot-02-smoke/`](./runs/pilot-02-smoke/) | 15 | Plumbing smoke only |

Each run: `METRICS.md` + `metrics.json`.

---

## Caveats (please keep these when citing)

1. **Single seed** — corpus is 2000 cases derived from one short public-style steel market brief, not a multi-document production mix.
2. **Synthetic corruptions** — template taxonomy (number_perturb, negation_flip, entity_swap, fabricated_detail, unsupported_generalization, …).
3. **Validation, not training** — designed so the critic under test has not been trained on this set; still, patterns can overfit templates.
4. **Prompt iteration** — pilot-02 v2 improved hedges but increased Node2 FPR vs pilot-01; stacks matter more than any single 8B critic.
5. **No raw eval DB in this package** — regenerate with the harness if you need case-level data; keep proprietary seeds private.

See also [`NOTICE.md`](./NOTICE.md).

---

## Reproduce (high level)

```bash
# Harness lives next to this package in the lab layout:
cd sv-testgen
python generator.py --target 2000 --db sv_corpus.db
python evaluator.py --db sv_corpus.db --config config.json --run-id my-run
python report.py --db sv_corpus.db --run-id my-run
python _export_benchmarks.py   # refreshes this benchmarks tree
```

Details: [`METHOD.md`](./METHOD.md) and [`AGENT_SYNOPSIS.md`](./AGENT_SYNOPSIS.md).

---

## License / use

Method, metrics, and documentation are intended for open collaboration with local / sovereign agent stacks.  
Do not republish proprietary seed text or evaluation databases that embed it.

Built for sovereign agents — **fork, extend, improve.**
