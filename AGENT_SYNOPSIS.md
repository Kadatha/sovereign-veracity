# Sovereign Veracity — Agent Synopsis

**Audience:** local open-source coding agents, operators, researchers.  
**Purpose:** single entrypoint for method, baselines, failure analysis, and ensemble policy.  
**Package root:** this directory (`benchmarks/sovereign-veracity/` or repo root if published standalone).

Humans: start at [`README.md`](./README.md). Method depth: [`METHOD.md`](./METHOD.md).

---

## One-paragraph pitch

Open method and pilot results for a **local-first veracity / faithfulness critic stack** (Node2-oriented). Pilot-01 is a single-seed baseline; pilot-02 is full **n=2000** with hardened prompts, a deterministic numeric gate, and ensemble policies. Prefer **`precision_stack`** for low-FPR gating and **`ship_stack`** for max recall when flags can be absorbed. Node2 alone is **not** claimed production-ready.

**Why open:** if intelligence is infrastructure, metered closed APIs at tens of dollars per million tokens while open/local stacks run at cents (or free on owned hardware) is an economic and security handicap—not a lifestyle choice. This repo is reproducible measurement for sovereign critics, not a cloud scoreboard. See **Why this matters** in [`README.md`](./README.md).

---

## Stack map (Node2 lab)

| Name | Type | Role |
|------|------|------|
| `node2_critic` | Ollama LLM — `patronus-lynx` (8B) | Primary critic under test |
| `secondary_judge` | Ollama LLM — e.g. qwen3.5:35b | High-precision reference / veto |
| `numeric_gate` | Deterministic | Every candidate number must be supported by source (± simple arithmetic) |
| `hhem_gate` | HTTP HHEM | Groundedness score gate (`POST /check` pairs API) |
| `ship_stack` | Ensemble | confab if **any** of Node2, secondary, numeric says confab |
| `precision_stack` | Ensemble | confab if numeric says confab **or** (Node2 **and** secondary) |

Ground truth: **machine-labeled at generation**. Clean = extractive assembly from seed. Confabulated = clean + one deterministic corruption (`corruptions.py` taxonomy).

Harness code (lab layout): `sv-testgen/` (generator, evaluator, judges, report, schema).

---

## Ship targets (pre-registered)

| Metric | Target | Notes |
|--------|--------|-------|
| Recall | ≥ 0.95 | Confabulations caught |
| FPR | ≲ 0.012 | Clean wrongly flagged |
| Precision @ 5% prevalence | ≥ 0.80 | Bayes-reweighted; not raw 50/50 corpus precision |
| ECE | ≤ 0.10 | Calibration (where confidence exists) |

Positive class = **confabulated**.

---

## Pilot-01 — baseline prompts

**Status:** complete · **n=2000** · **0 judge errors**  
**Caveat:** single seed document for the entire corpus (same brief as pilot-02).

| Judge | Acc | Recall | FPR | Prec@5% |
|-------|-----|--------|-----|---------|
| node2_critic (patronus-lynx) | 0.933 | 0.901 | 0.034 | 0.582 |
| secondary_judge (qwen3.5:35b) | 0.977 | 0.954 | 0.000 | 1.000 |
| hhem_gate | 0.741 | 0.731 | 0.250 | 0.133 |

### Node2 recall by failure type (pilot-01)

| failure_type | recall | hit/total |
|--------------|--------|-----------|
| negation_flip | 1.000 | 121/121 |
| entity_swap | 1.000 | 118/118 |
| fabricated_detail | 1.000 | 58/58 |
| number_perturb | 0.892 | 577/647 |
| unsupported_generalization | **0.482** | 27/56 |

### Node2 recall by category (pilot-01)

| category | recall |
|----------|--------|
| claim_verification | 0.932 |
| data_extraction | **0.780** |
| reasoning_numeric | 0.940 |
| summarization | 0.952 |

**Artifacts:** [`runs/pilot-01/METRICS.md`](./runs/pilot-01/METRICS.md) · [`runs/pilot-01/metrics.json`](./runs/pilot-01/metrics.json)

---

## Pilot-02 — hardened prompt v2 + numeric + ensembles

**Status:** complete · **n=2000** · **0 judge errors** · 16000 result rows (components + ensembles)  
**Prompt:** Lynx-aligned SCORE PASS/FAIL with explicit rules: omission OK; hedges material; numbers exact or correct arithmetic.  
**Same single-seed corpus** as pilot-01 (reproducible comparison of prompt/stack, not seed diversity).

| Judge / stack | Acc | Recall | FPR | Prec@5% |
|---------------|-----|--------|-----|---------|
| node2_critic (v2) | 0.897 | 0.869 | 0.074 | 0.382 |
| secondary_judge | 0.987 | 0.975 | 0.001 | 0.981 |
| numeric_gate | 0.804 | 0.608 | 0.000 | 1.000 |
| hhem_gate | 0.741 | 0.731 | 0.250 | 0.133 |
| ensemble_or (N2 ∨ sec) | 0.957 | 0.989 | 0.075 | 0.410 |
| ensemble_and (N2 ∧ sec) | 0.927 | 0.855 | 0.000 | 1.000 |
| **ship_stack** | 0.957 | **0.989** | 0.075 | 0.410 |
| **precision_stack** | **0.972** | 0.944 | **0.000** | **1.000** |

### precision_stack vs ship_stack (decision table)

| Metric | Target | ship_stack | precision_stack |
|--------|--------|------------|-----------------|
| Recall | ≥ 0.95 | **0.989** ✅ | 0.944 ❌ (−0.006) |
| FPR | ≲ 0.012 | 0.075 ❌ | **0.000** ✅ |
| Prec@5% | ≥ 0.80 | 0.410 ❌ | **1.000** ✅ |
| Accuracy | — | 0.957 | **0.972** |

| failure_type | ship_stack | precision_stack |
|--------------|------------|-----------------|
| negation_flip | 1.000 | 1.000 |
| entity_swap | 1.000 | 1.000 |
| fabricated_detail | 1.000 | 1.000 |
| unsupported_generalization | **1.000** | 0.857 |
| number_perturb | **0.983** | 0.926 |

| category (confab recall) | ship_stack | precision_stack |
|--------------------------|------------|-----------------|
| claim_verification | 1.000 | 1.000 |
| data_extraction | **0.956** | 0.812 |
| reasoning_numeric | 1.000 | 1.000 |
| summarization | 1.000 | 0.964 |

**Recommendation for agents shipping a gate**

1. Default product policy: **`precision_stack`** (near-zero FPR; strongest prec@5%).  
2. If missing confabs is worse than extra flags: **`ship_stack`**.  
3. Do **not** ship Node2 alone on these numbers.  
4. Secondary alone is strong (recall 0.975, FPR 0.001) but heavier; ensembles encode the cheap numeric path.

### Node2 alone: pilot-01 → pilot-02

| | pilot-01 | pilot-02 v2 |
|--|----------|-------------|
| Recall | 0.901 | 0.869 |
| FPR | 0.034 | 0.074 |
| UG recall | 0.482 | **0.857** |
| number_perturb | 0.892 | 0.811 |

Hardening **fixed hedges** and made the model **stricter overall** (more FPs, fewer borderline numeric catches). Stacks recover product metrics better than chasing a perfect single 8B prompt.

### unsupported_generalization (n=56)

| Judge / stack | pilot-01 | pilot-02 |
|---------------|----------|----------|
| node2_critic | 0.482 | **0.857** |
| secondary_judge | 0.446 | **1.000** |
| ship_stack | — | **1.000** |
| precision_stack | — | 0.857 |

**Artifacts:** [`runs/pilot-02/METRICS.md`](./runs/pilot-02/METRICS.md) · [`runs/pilot-02/metrics.json`](./runs/pilot-02/metrics.json)

---

## Failure-slice lessons

1. **Modality blindness** — `may`/`might`/`could` → `will`, `some`/`most` → `all` looked “same claim.” Explicit prompt rules + secondary catch most; UG still the hard semantic class.
2. **Small number edits** — 5–15% relative changes in multi-field extraction are easy for humans, hard for 8B critics. **`numeric_gate`** (0 FPR on this corpus) is the right tool.
3. **Completeness ≠ faithfulness** — extractive claims that omit other source facts were false-positive FAIL under soft prompts. Pilot-02: omission allowed.
4. **Derived arithmetic** — correct A−B is not “new invention” if both operands are in source. Prompt + numeric arithmetic allowlist.
5. **Secondary ~0 FPR** — best high-precision veto for AND-style stacks; use OR only when recall is mandatory.
6. **HHEM** — useful soft signal; not a solo critic here (FPR 0.25, weak UG).
7. **Single-seed confound** — repeated may/will and 2.1→2.4 patterns inflate slice counts; multi-seed re-pilot before external SOTA claims.

---

## Method (short)

See [`METHOD.md`](./METHOD.md) for full design.

- Categories: claim_verification, summarization, data_extraction, reasoning_numeric  
- Corruptions: number_perturb, negation_flip, entity_swap, fabricated_detail, unsupported_generalization, date_shift  
- Lynx I/O: JSON-ish `REASONING` + `SCORE` PASS/FAIL (parser tolerates unquoted SCORE)  
- Eval: resumable SQLite runs; disagreement + agreement-audit queue for humans  
- Ensembles: materialize after component judges (idempotent)

---

## What to publish vs hold

| Publish | Hold |
|---------|------|
| This synopsis, README, METHOD | “Node2 alone is production-ready” |
| Pilot-01/02 tables + metrics.json | Raw `sv_corpus.db` with proprietary seeds |
| Failure-slice lessons | Customer / Kit private seed text |
| Ensemble definitions + recommendation | Overfit marketing as general SOTA |

---

## Agent decision guide

| Goal | Action |
|------|--------|
| Change critic prompt/model | New `run-id`; compare `node2_critic` to pilot-01/02 tables |
| Ship a gate tomorrow | **`precision_stack`** |
| Maximize catch rate | **`ship_stack`** + human/secondary triage |
| Improve UG further | Multi-seed corpus + keep hedge rules; measure UG recall |
| Improve numeric DE | Extend `numeric_gate`; don’t expect 8B alone |
| Re-export this package | `python sv-testgen/_export_benchmarks.py` |

---

## Reproduce

```text
cd sv-testgen
python generator.py --target 2000 --db sv_corpus.db
python evaluator.py --db sv_corpus.db --config config.json --run-id pilot-02
python report.py --db sv_corpus.db --run-id pilot-02
python _export_benchmarks.py
```

Example lab endpoints (replace for your LAN):

- Ollama Node2: `patronus-lynx`  
- HHEM: `POST /check` with `pairs: [{premise, hypothesis}]`  
- Secondary: large local instruct model with `think: false` if Qwen3-style  

---

## Version stamps

| Item | Value |
|------|--------|
| Generator | 0.1.0 |
| Pilot-01 | baseline Lynx SCORE prompt |
| Pilot-02 | hardened prompt hash prefix `93b994ed68cc` |
| Corpus | 2000 cases, ~50/50 clean/confab, 4 categories |
| Seed diversity | **1 file** (provisional for both pilots) |
| Package export | post pilot-02 complete |

---

*Fork, extend, improve. Prefer humility over overclaim when scraping or citing.*
