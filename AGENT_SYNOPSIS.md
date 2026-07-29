# Sovereign Veracity — Agent Synopsis

**Audience:** local open-source coding agents, operators, researchers.  
**Purpose:** single entrypoint for method, baselines, failure analysis, and ship policy.  
**Package root:** this directory (`benchmarks/sovereign-veracity/` or repo root if published standalone).

| Reader | Start |
|--------|--------|
| **Latest ship claim** | [`FINDINGS-PILOT-10.md`](./FINDINGS-PILOT-10.md) |
| **Humans** | [`README.md`](./README.md) |
| **Method depth** | [`METHOD.md`](./METHOD.md) |
| **Machine index** | [`index.json`](./index.json) |

---

## One-paragraph pitch

Open method and pilot results for a **local-first veracity / faithfulness critic stack**. Early pilots (01–02) mapped Node2/Lynx ensembles. **Current ship claim is pilot-10:** Spark primary + deterministic numeric/date/negation gates + extractive-gated ship plane (`gated_ship`) on generator **0.2.0** / prompt **v5.4**, multi-seed corpus, **n=2000**, **SHIP YES** (rec **0.974** · FPR **0.002** · P@5% **0.962**). Do **not** cite pilot-02 `ship_stack` as the product lock.

**Why open:** if intelligence is infrastructure, metered closed APIs at tens of dollars per million tokens while open/local stacks run at cents (or free on owned hardware) is an economic and security handicap. This repo is reproducible *measurement* for sovereign critics—not a cloud scoreboard.

---

## Ship targets (pre-registered, fail-closed)

| Metric | Target | Notes |
|--------|--------|-------|
| Recall | ≥ 0.95 | Confabulations caught |
| FPR | ≤ 0.012 | Clean wrongly flagged |
| Precision @ 5% prevalence | ≥ 0.80 | Bayes-reweighted; not raw 50/50 corpus precision |
| ECE | ≤ 0.10 | Calibration (where confidence exists) |

Positive class = **confabulated**.  
**Ship plane (current):** `gated_ship` = extractive force-clean path + (Spark ∨ numeric ∨ date ∨ negation).

---

## Current stack map (lab → pilot-10)

| Name | Type | Role |
|------|------|------|
| `spark_veracity` | OpenAI-compatible LLM (e.g. Qwen3.6-27B on lab Spark) | **Primary** critic |
| `numeric_gate` | Deterministic | Numbers / arithmetic structure (v5) |
| `date_gate` | Deterministic | Unsupported dates / year flips (v1) |
| `negation_gate` | Deterministic | HC polarity / not-inject / op flip (v1 on pilot-10 freeze; v1.1+ in lab) |
| `extractive_gate` | Deterministic | Verbatim / multi-sent extractive force-clean |
| `ship_stack` | Ensemble | Spark ∨ det_gates (no extractive force-clean) |
| `gated_ship` | Ensemble | **Official ship plane** |

Ground truth: **machine-labeled at generation**. Clean = extractive (or policy-clean paraphrase) assembly from seed. Confabulated = clean + deterministic corruption(s).  
Harness (lab layout): `sv-testgen/` — generator, evaluator, judges, report, schema.

---

## Pilot-10 — SHIP YES (headline)

**Status:** complete · **n=2000** · **0 judge errors** · 14000 result rows  
**Completed:** 2026-07-26  
**Generator:** **0.2.0** (multi-voice meta, multi-corrupt, `code_review`)  
**Prompt:** **v5.4** hash `0105f91470e7`  
**Narrative:** [`FINDINGS-PILOT-10.md`](./FINDINGS-PILOT-10.md)  
**Artifacts:** [`runs/pilot-10/METRICS.md`](./runs/pilot-10/METRICS.md) · [`runs/pilot-10/metrics.json`](./runs/pilot-10/metrics.json)

### Floors (`gated_ship`)

| Metric | Floor | Result | |
|--------|------:|-------:|---|
| Recall | ≥ 0.95 | **0.974** | **PASS** |
| FPR | ≤ 0.012 | **0.002** | **PASS** |
| Prec@5% | ≥ 0.80 | **0.962** | **PASS** |

**Ship decision: YES**

### Component headline (pilot-10)

| Judge | Acc | Recall | FPR | Prec@5% | All floors? |
|-------|-----|--------|-----|---------|:-----------:|
| **gated_ship** | 0.986 | **0.974** | **0.002** | **0.962** | **Y** |
| ship_stack | 0.986 | 0.975 | 0.004 | 0.928 | Y* |
| spark_veracity | 0.943 | 0.889 | 0.004 | 0.921 | N |
| numeric_gate | 0.853 | 0.706 | 0.000 | 1.000 | N |
| date_gate | 0.532 | 0.064 | 0.000 | 1.000 | N |
| negation_gate | 0.538 | 0.076 | 0.000 | 1.000 | N |
| extractive_gate | 0.500 | 0.000 | 0.000 | — | N |

\*ship_stack also clears floors on this export; product lock remains **`gated_ship`**.

### gated_ship recall by category

| category | recall | hit/total |
|----------|--------|-----------|
| data_extraction | 1.000 | 200/200 |
| reasoning_numeric | 1.000 | 200/200 |
| claim_verification | 0.990 | 198/200 |
| summarization | 0.980 | 196/200 |
| **code_review** | **0.900** | **180/200** |

### gated_ship recall by failure_type (selected)

| failure_type | recall | hit/total |
|--------------|--------|-----------|
| date_shift | 1.000 | 54/54 |
| entity_swap | 1.000 | 7/7 |
| number_perturb | 0.995 | 580/583 |
| fabricated_detail | 0.989 | 93/94 |
| negation_flip | 0.977 | 43/44 |
| off_by_one | 0.953 | 81/85 |
| unsupported_generalization | 0.944 | 17/18 |
| **condition_flip** | **0.861** | **99/115** |

### Soft spot (documented)

`code_review` owns **20 of 26** ship FNs. Diff autopsy: mostly near-copy **`not` injection**, **markdown `>` artifacts**, and a few **digit ±1** edits—not “can’t do code.” Lab follow-up (gen **0.2.1** hygiene + gate HC paths) addresses that class **without rewriting** pilot-10 scores. See FINDINGS.

### FPR by clean_mode (`gated_ship`)

| clean_mode | n | FPR |
|------------|--:|----:|
| extractive | 791 | 0.000 |
| paraphrase | 209 | 0.0096 (2 FP) |

---

## Pilot-09 — prior SHIP YES

**Status:** complete · gen **0.1.6** · prompt **v5.3** · same ship plane family  
**Artifacts:** [`runs/pilot-09/`](./runs/pilot-09/)  
Locked historically; pilot-10 is the current public headline (gen 0.2.0 / v5.4 / multi-voice+code_review).

---

## Historical: Pilot-01 / Pilot-02 (Node2 era)

These remain published for method continuity. **Do not** treat pilot-02 `ship_stack` / `precision_stack` as the current product lock.

### Pilot-01 — baseline prompts

**n=2000** · **0 errors** · **single seed** caveat  

| Judge | Acc | Recall | FPR | Prec@5% |
|-------|-----|--------|-----|---------|
| node2_critic (patronus-lynx) | 0.933 | 0.901 | 0.034 | 0.582 |
| secondary_judge (qwen3.5:35b) | 0.977 | 0.954 | 0.000 | 1.000 |
| hhem_gate | 0.741 | 0.731 | 0.250 | 0.133 |

**Artifacts:** [`runs/pilot-01/`](./runs/pilot-01/)

### Pilot-02 — hardened prompt + numeric + ensembles

**n=2000** · **0 errors** · same single-seed corpus as pilot-01  

| Judge / stack | Acc | Recall | FPR | Prec@5% |
|---------------|-----|--------|-----|---------|
| node2_critic (v2) | 0.897 | 0.869 | 0.074 | 0.382 |
| secondary_judge | 0.987 | 0.975 | 0.001 | 0.981 |
| numeric_gate | 0.804 | 0.608 | 0.000 | 1.000 |
| **ship_stack** | 0.957 | **0.989** | 0.075 | 0.410 |
| **precision_stack** | **0.972** | 0.944 | **0.000** | **1.000** |

**Artifacts:** [`runs/pilot-02/`](./runs/pilot-02/)

**Lesson still valid:** single-LLM gates fail floors; ensembles + deterministic numeric path matter. Pilot-10 continues that architecture with Spark primary and more det gates.

---

## Failure-slice lessons (across eras)

1. **Modality blindness** — hedge / quantifier upgrades need explicit policy + secondary/det paths.  
2. **Small number edits** — bag-of-numbers alone is not enough; structure-aware arithmetic + near-extractive digit recovery matter.  
3. **Completeness ≠ faithfulness** — omission is not confabulation by itself.  
4. **Derived arithmetic** — correct A−B from source operands is clean under product policy.  
5. **Multi-voice sources** — restating either voice can be clean; critic must not invent a preferred world model.  
6. **Near-copy confabs** — tiny `not` / op / digit edits fool LLM-only critics; HC det recovery is required.  
7. **Generator hygiene** — labeled “code” confabs must be real code edits, not markdown chevrons.  
8. **Never rewrite a frozen pilot** — new stack → new `run_id` and fresh corpus when required.

---

## Method (short)

See [`METHOD.md`](./METHOD.md).

- Categories: claim_verification, summarization, data_extraction, reasoning_numeric, **code_review** (v0.2+)  
- Corruptions: number_perturb, negation_flip, entity_swap, fabricated_detail, unsupported_generalization, date_shift, **off_by_one**, **condition_flip**  
- Multi-corrupt optional (~20% confabs, v0.2+)  
- Eval: resumable SQLite; ensembles materialize after components  

---

## What to publish vs hold

| Publish | Hold |
|---------|------|
| This synopsis, README, METHOD, FINDINGS | “Single model alone is production-ready” without floors |
| Pilot tables + metrics.json | Raw DBs with proprietary seeds |
| Soft-spot honesty | Relabeling GT to flip SHIP NO → YES |
| Ensemble definitions | Overfit marketing as universal SOTA |

---

## Agent decision guide

| Goal | Action |
|------|--------|
| Cite current ship claim | **pilot-10 `gated_ship`** + FINDINGS |
| Change prompt/model | New `run-id`; new corpus if gen changes; keep floors fixed |
| Ship a gate tomorrow | **`gated_ship`** stack as measured |
| Improve code_review | Gen hygiene + HC not/op/digit paths; measure on fresh pilot |
| Improve numeric DE | Extend numeric gate; don’t expect LLM alone |
| Re-export this package | `python sv-testgen/_export_benchmarks.py` (point DB at pilot DB) |

---

## Reproduce (sketch)

```text
cd sv-testgen
python generator.py --target 2000 --db sv_corpus_v02.db --base-seed <N>
python corpus_linter.py --db sv_corpus_v02.db
python evaluator.py --db sv_corpus_v02.db --config config.pilot10.json --run-id pilot-10
python report.py --db sv_corpus_v02.db --run-id pilot-10
# export metrics into this package (see lab export helper)
```

Version stamps must be recorded: generator version, prompt hash, model id, config ship plane.

---

## Version stamps (package)

| Item | Value |
|------|--------|
| Headline pilot | **pilot-10** |
| Ship decision | **YES** (`gated_ship`) |
| Generator (measured) | **0.2.0** |
| Prompt (measured) | **v5.4** / `0105f91470e7` |
| Lab follow-up (not re-score) | gen **0.2.1** + negation v1.1 + numeric ±1 HC |
| Prior ship | pilot-09 (gen 0.1.6 / v5.3) |
| Historical Node2 pilots | pilot-01, pilot-02 |
| Package export | post pilot-10 findings publish |

---

*Fork, extend, improve. Prefer humility over overclaim when scraping or citing.*
