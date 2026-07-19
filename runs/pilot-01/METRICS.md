# pilot-01 — metrics summary

Exported: 2026-07-19T04:49:47.600130+00:00
Results: 6000 (errors: 0)
Corpus cases: 2000
Seeds: {'example_steel_brief.txt': 2000}

## Ship targets
- Recall ≥ 0.95
- FPR ≤ 0.012
- Precision @ 5% prevalence ≥ 0.8

## Per-judge headline

| Judge | n | Acc | Recall | FPR | Prec@5% | Ship? |
|-------|---|-----|--------|-----|---------|-------|
| hhem_gate | 2000 | 0.741 | 0.731 | 0.250 | 0.133 | N |
| node2_critic | 2000 | 0.933 | 0.901 | 0.034 | 0.582 | N |
| secondary_judge | 2000 | 0.977 | 0.954 | 0.000 | 1.000 | partial |

## Recall by failure type

### hhem_gate
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| number_perturb | 0.872 | 564/647 |
| fabricated_detail | 0.741 | 43/58 |
| entity_swap | 0.483 | 57/118 |
| negation_flip | 0.479 | 58/121 |
| unsupported_generalization | 0.161 | 9/56 |

### node2_critic
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| negation_flip | 1.000 | 121/121 |
| entity_swap | 1.000 | 118/118 |
| fabricated_detail | 1.000 | 58/58 |
| number_perturb | 0.892 | 577/647 |
| unsupported_generalization | 0.482 | 27/56 |

### secondary_judge
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| negation_flip | 1.000 | 121/121 |
| entity_swap | 1.000 | 118/118 |
| fabricated_detail | 1.000 | 58/58 |
| number_perturb | 0.977 | 632/647 |
| unsupported_generalization | 0.446 | 25/56 |

## Recall by category

### hhem_gate
- **claim_verification**: 0.692 (173/250)
- **data_extraction**: 0.860 (215/250)
- **reasoning_numeric**: 0.996 (249/250)
- **summarization**: 0.376 (94/250)

### node2_critic
- **claim_verification**: 0.932 (233/250)
- **data_extraction**: 0.780 (195/250)
- **reasoning_numeric**: 0.940 (235/250)
- **summarization**: 0.952 (238/250)

### secondary_judge
- **claim_verification**: 0.928 (232/250)
- **data_extraction**: 0.956 (239/250)
- **reasoning_numeric**: 0.984 (246/250)
- **summarization**: 0.948 (237/250)
