# Pilot-10 findings (SHIP YES)

**Completed:** 2026-07-26  
**Decision:** **SHIP YES** under pre-registered §1c floors (fail-closed point estimates)  
**Ship plane:** `gated_ship` (extractive gate + Spark primary + det gates: numeric / date / negation)

---

## Official floors

| Metric | Floor | Result | |
|--------|------:|-------:|---|
| Recall | ≥ 0.95 | **0.974** | **PASS** |
| FPR | ≤ 0.012 | **0.002** | **PASS** |
| Precision @ 5% prevalence | ≥ 0.80 | **0.962** | **PASS** |

| Setup | Value |
|-------|--------|
| Corpus | n=2000, generator **0.2.0** (multi-voice meta, multi-corrupt, `code_review`) |
| Prompt | product **v5.4** hash `0105f91470e7` |
| Component judge calls | 10 000 (0 errors) |
| Ensemble rows | 4 000 (`ship_stack` + `gated_ship`) |
| Primary LLM | Spark / Qwen3.6-27B (OpenAI-compatible) |

Full machine tables: [`runs/pilot-10/`](./runs/pilot-10/).

---

## What changed vs earlier public pilots

Public pilot-01/02 used Node2/Lynx-centric stacks and a single-seed steel corpus.  
Pilot-10 is the first published full-floor **SHIP YES** under:

- Spark as primary truth plane  
- Deterministic numeric + date + negation gates  
- Extractive-gated ship plane  
- Multi-seed / multi-category generator **0.2.0** (incl. multi-voice product policy)

Pilot-09 (also exported here) is the prior ship lock under generator **0.1.6** / prompt **v5.3**.

---

## Soft spot: `code_review` (not ship-blocking)

| Slice | Ship recall |
|-------|------------:|
| data_extraction | 1.000 |
| reasoning_numeric | 1.000 |
| claim_verification | 0.990 |
| summarization | 0.980 |
| **code_review** | **0.900** |

**Autopsy (20 of 26 total ship FNs):** near-copy confabs, not “can’t read code.”

| Edit class | n | Notes |
|------------|--:|-------|
| Prose `not` injection (e.g. if needed → if not needed) | 13 | Real polarity; Spark called near-verbatim |
| Markdown `>` → `<` | 4 | Generator artifact (blockquote), not code comparison |
| Digit ±1 | 4 | Bag-of-numbers collision cases |

**Conclusion:** good-enough for ship of the **document faithfulness** stack; code was a weak subject, not a failed floor.

**Follow-up (lab harness, not re-scoring pilot-10):** generator **0.2.1** hygiene (no markdown/prose fake flips) + negation **v1.1** (`not` inject / op flip HC) + numeric near-extractive ±1. Offline replay of those 20 FNs: **18/20** caught by the new gates. Pilot-10 numbers remain the frozen official score.

---

## Product policy reminders (unchanged)

- Faithfulness is to the **source as written**, not a reconciled world model (multi-voice restatement can be clean).  
- Strict number fidelity: approximated figures that introduce new digit sequences are intended confabs.  
- Machine labels are mechanical (extractive clean + deterministic corruption).

---

## How to cite

```text
Sovereign Veracity pilot-10 (2026-07-26): gated_ship rec=0.974 FPR=0.002 P@5%=0.962 — SHIP YES.
See https://github.com/Kadatha/sovereign-veracity runs/pilot-10/
```
