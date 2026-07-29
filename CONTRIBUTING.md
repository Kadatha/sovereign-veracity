# Contributing

Thanks for helping improve **Sovereign Veracity** — open method, local-first critic benchmarks, and ensemble policies for sovereign agents.

## Before you start

1. Read [`README.md`](./README.md) and **[`FINDINGS-PILOT-10.md`](./FINDINGS-PILOT-10.md)** (current ship claim).
2. Read [`AGENT_SYNOPSIS.md`](./AGENT_SYNOPSIS.md) (tables, version stamps) and [`METHOD.md`](./METHOD.md).
3. Read [`NOTICE.md`](./NOTICE.md) — keep claims honest (cite the right pilot; no single-LLM overclaim; no GT rewrite).
4. Follow the [Code of Conduct](./.github/CODE_OF_CONDUCT.md).

## High-value contributions

| Area | Examples |
|------|----------|
| **Multi-seed corpora** | Public-domain or clearly licensed seeds; generator configs; diversity stats |
| **Failure types** | New deterministic corruptions; multi-corruption cases; code_review hygiene |
| **Judges / stacks** | Adapters for other local models; better numeric/negation gates; calibrated confidence |
| **Ship soft spots** | Autopsies that preserve frozen pilot scores while improving the next generator |
| **Repro** | CI that runs a tiny smoke on mock judges; pinned model tags; export scripts |
| **Docs** | Clearer agent instructions; non-English summaries; threat-model notes |

## What we will not merge

- Proprietary or customer seed text without explicit public license  
- Raw evaluation DBs that embed private data  
- Leaderboard claims that drop single-seed / synthetic-corruption caveats  
- Cloud-only “required” paths that break local-first operation  

## How to work

```text
# Prefer a fork + PR
# Keep ship targets fixed before reading new metrics
# New prompt or model → new run-id; export metrics JSON/MD
# Document version stamps (model tags, prompt hash, generator version)
```

Harness code may live in a sibling `sv-testgen/` tree in the lab layout. If you only change this metrics package, keep PRs focused on docs + exported numbers; if you change the harness, describe how to re-run pilots.

## Reproducibility checklist (PRs with new numbers)

- [ ] Generator version and base seed recorded  
- [ ] Model names/tags and prompt hash (or template path) recorded  
- [ ] `n` cases, seed count / diversity note  
- [ ] Error rate of judge calls  
- [ ] Headline: recall, FPR, prec@5% for primary + preferred stack  
- [ ] Per–failure-type table for confabulated class  

The **version stamps** and **reproduce** blocks in `AGENT_SYNOPSIS.md` are the gold standard—copy that shape.

## Issues

Open an issue for:

- Bugs in documented metrics or broken links  
- Proposal for multi-seed pilot-03  
- Ensemble policy ideas (beyond precision_stack / ship_stack)  

## License

By contributing, you agree your contributions are licensed under the [MIT License](./LICENSE).
