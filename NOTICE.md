# NOTICE — publishing boundary

## Intended for public / open distribution

- `README.md`, `AGENT_SYNOPSIS.md`, `METHOD.md`, `FINDINGS-PILOT-10.md`, this file  
- `index.json` and **all** `runs/*/METRICS.md` + `runs/*/metrics.json` (pilot-01 through pilot-10 path, including intermediate diagnostics)  
- Method description, ship floors, ensemble definitions  
- Failure-slice lessons and soft-spot honesty  

**Headline ship claim** remains pilot-10 `gated_ship` only; intermediate runs are transparency, not alternate locks.

## Do not overclaim

- **A single LLM critic alone** is not certified production-ready by these pilots (see Spark-alone vs `gated_ship` on pilot-10).  
- Early pilots (**pilot-01/02**) are **single-seed**; later ship pilots use **multi-seed** corpora—cite the right pilot.  
- Corruptions are **synthetic / mechanical** templates.  
- Cite as **pilot / validation**, not universal SOTA.  
- **Do not rewrite** a frozen pilot’s GT or re-use a run_id after stack changes.

## Current public ship claim

- **pilot-10** · plane **`gated_ship`** · **SHIP YES**  
  rec **0.974** · FPR **0.002** · P@5% **0.962**  
  See [`FINDINGS-PILOT-10.md`](./FINDINGS-PILOT-10.md).

## Hold back unless explicitly scrubbed

- Raw SQLite eval DBs once seeds include proprietary Kit, customer, or prospectus text  
- Private seed dumps (internal extracts, client rationales, session transcripts)  
- LAN hostnames, keys, or internal-only secrets  

## Attribution

Patronus Lynx is an open hallucination-evaluation model family (see Patronus AI / model cards).  
HHEM is Vectara’s hallucination evaluation model family (or your local port).  
Lab Spark runs use open-weight models under local deployment (e.g. Qwen-class).  
This package evaluates a **local deployment composition**, not those projects’ official leaderboards.
