# Sovereign Veracity Critic: Pilot-10 (self-graded), now re-testing on real lab claims

> **Status, October 2026: re-testing.** The Pilot-10 numbers below come from a generated test set, graded by the same lab that built the critic. When the critic was put to work on the lab's real output, its number check raised false alarms on correct claims, so I pulled it back and moved the lab's critic job to a frontier model. It is now being re-tested on claims taken from the lab's own receipts, on an upgraded server. New results will be published here, including what fails.

**Open method, harness design, baselines, and ship-floor results for a local-first faithfulness / confabulation stack.**

| Stack | Role | Recall | FPR | Prec@5% | Ship? |
|-------|------|-------:|----:|--------:|:-----:|
| **gated_ship** (pilot-10) | Official ship plane | **0.974** | **0.002** | **0.962** | **YES** |
| ship_stack (pilot-10) | Spark ∨ det gates | 0.975 | 0.004 | 0.928 | partial* |
| spark_veracity alone | Primary LLM | 0.889 | 0.004 | 0.921 | N |

\*Ship requires all three floors; ship_stack FPR still under target but gated_ship is the locked plane.

**Label:** recall **0.974** / FPR **0.002** are **self-graded / builder-authored** (machine GT). Not independent third-party eval. Not a store price. Shelf: [`../../sovereign-veracity/PUBLIC_SHELF.md`](../../sovereign-veracity/PUBLIC_SHELF.md).

**Details:** [`FINDINGS-PILOT-10.md`](./FINDINGS-PILOT-10.md) · machine tables [`runs/pilot-10/`](./runs/pilot-10/)

---

## Why this matters

The future is open source. We need to embrace it and get on with it. If AI is the through line of future economic (and security) activity, locking the stack behind closed models that cost **$26–56 per 1M tokens** while open / local intelligence runs at roughly **$0.50–1** (or free on your own silicon) is not a branding preference—it is a structural handicap: commercial and strategic. **Sovereign Veracity** is a small, concrete piece of that stack: local-first *measurement* of faithfulness critics, so sovereign agents can be judged without a cloud tax and without outsourcing the scoreboard. Fork it, re-seed it, beat the numbers.

---

## Start here

| Reader | Entry |
|--------|--------|
| **Latest findings** | [`FINDINGS-PILOT-10.md`](./FINDINGS-PILOT-10.md) |
| **Agents / automation** | [`AGENT_SYNOPSIS.md`](./AGENT_SYNOPSIS.md) |
| **Method & Node2 mapping** | [`METHOD.md`](./METHOD.md) |
| **Machine index** | [`index.json`](./index.json) |
| **Per-run tables + JSON** | [`runs/`](./runs/) |

---

## What this is

A **local, zero-cloud-API** evaluation package for faithfulness / confabulation critics:

- Ground truth is **generated**, not hand-labeled: extractive clean cases + deterministic corruption(s) per confabulated case.
- Positive class = **confabulated**. Precision is reported at realistic prevalence (3/5/8%), not the artificial 50/50 corpus split.
- **Ship floors (pre-registered, fail-closed):** recall ≥ 0.95 · FPR ≤ 0.012 · P@5% ≥ 0.80.

**Harness source** (sibling in the lab monorepo layout): `sv-testgen/`. This package is metrics + method + agent docs—not a dump of evaluation databases.

---

## Pre-registered ship targets

| Metric | Target |
|--------|--------|
| Recall | ≥ 0.95 |
| FPR | ≤ 0.012 |
| Precision @ 5% prevalence | ≥ 0.80 |

---

## Full pilot timeline (all published under `runs/`)

| Run | What | Ship? |
|-----|------|:-----:|
| [pilot-01](./runs/pilot-01/) | Early Node2 / secondary / HHEM baselines | — |
| [pilot-02](./runs/pilot-02/) | Ensembles, numeric gate, precision vs ship stacks | — |
| [pilot-02-smoke](./runs/pilot-02-smoke/) | Small smoke on pilot-02 stack | — |
| [pilot-03](./runs/pilot-03/) | Multi-seed push; extractive lessons | — |
| [pilot-03b](./runs/pilot-03b/) | Extractive gate slice | — |
| [pilot-04-spark](./runs/pilot-04-spark/) | Spark primary enters stack | — |
| [pilot-04d-summ](./runs/pilot-04d-summ/) | Summarization-focused slice | — |
| [pilot-05](./runs/pilot-05/) | Gated planes + multi-judge full n | — |
| [pilot-05b-prompt](./runs/pilot-05b-prompt/) | Prompt iteration | — |
| [pilot-05c-v5-fn](./runs/pilot-05c-v5-fn/) | Spark v5 FN probe | — |
| [pilot-06](./runs/pilot-06/) | Pre–floor-lock diagnostic (later burned for FPR claims) | — |
| [p07-v51-smoke](./runs/p07-v51-smoke/) | Smoke only (not independent FPR sample) | — |
| [pilot-09](./runs/pilot-09/) | Gen 0.1.6 + prompt v5.3, A bare + det_gates | **YES** |
| [**pilot-10**](./runs/pilot-10/) | Gen **0.2.0** + prompt **v5.4**, multi-voice / code_review | **YES** |

Machine index for all of the above: [`index.json`](./index.json).

### Pilot-10 component snapshot (n=2000 confab / 2000 clean)

**Self-graded / builder-authored** (machine GT). gated_ship 0.974 / 0.002 is not a price.

| Judge | Recall | FPR | Prec@5% |
|-------|-------:|----:|--------:|
| gated_ship | 0.974 | 0.002 | 0.962 |
| ship_stack | 0.975 | 0.004 | 0.928 |
| spark_veracity | 0.889 | 0.004 | 0.921 |
| numeric_gate | 0.706 | 0.000 | 1.000 |
| date_gate | 0.064 | 0.000 | 1.000 |
| negation_gate | 0.076 | 0.000 | 1.000 |

Det gates: **zero FP** on this run. Weakest category: `code_review` rec **0.900** (see findings autopsy).

---

## Soft spot (documented, not papered over)

Pilot-10 `code_review` misses were mostly **near-copy** edits (`not` injection, markdown chevron artifacts, digit ±1)—not a failure of the overall ship claim. Lab follow-up (generator **0.2.1** + gate HC paths) addresses that class without rewriting pilot-10 scores. Details: [`FINDINGS-PILOT-10.md`](./FINDINGS-PILOT-10.md).

---

## License

MIT — see [`LICENSE`](./LICENSE). Method docs and metrics are for reuse; do not present two different pilots under the same run_id.
