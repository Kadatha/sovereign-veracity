# pilot-02 — metrics summary

Exported: 2026-07-19T04:49:47.678699+00:00
Results: 16000 (errors: 0)
Corpus cases: 2000
Seeds: {'example_steel_brief.txt': 2000}

## Ship targets
- Recall ≥ 0.95
- FPR ≤ 0.012
- Precision @ 5% prevalence ≥ 0.8

## Per-judge headline

| Judge | n | Acc | Recall | FPR | Prec@5% | Ship? |
|-------|---|-----|--------|-----|---------|-------|
| ensemble_and | 2000 | 0.927 | 0.855 | 0.000 | 1.000 | partial |
| ensemble_or | 2000 | 0.957 | 0.989 | 0.075 | 0.410 | partial |
| hhem_gate | 2000 | 0.741 | 0.731 | 0.250 | 0.133 | N |
| node2_critic | 2000 | 0.897 | 0.869 | 0.074 | 0.382 | N |
| numeric_gate | 2000 | 0.804 | 0.608 | 0.000 | 1.000 | partial |
| precision_stack | 2000 | 0.972 | 0.944 | 0.000 | 1.000 | partial |
| secondary_judge | 2000 | 0.987 | 0.975 | 0.001 | 0.981 | Y |
| ship_stack | 2000 | 0.957 | 0.989 | 0.075 | 0.410 | partial |

## Recall by failure type

### ensemble_and
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| negation_flip | 1.000 | 121/121 |
| entity_swap | 1.000 | 118/118 |
| fabricated_detail | 0.983 | 57/58 |
| unsupported_generalization | 0.857 | 48/56 |
| number_perturb | 0.790 | 511/647 |

### ensemble_or
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| negation_flip | 1.000 | 121/121 |
| entity_swap | 1.000 | 118/118 |
| fabricated_detail | 1.000 | 58/58 |
| unsupported_generalization | 1.000 | 56/56 |
| number_perturb | 0.983 | 636/647 |

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
| fabricated_detail | 0.983 | 57/58 |
| unsupported_generalization | 0.857 | 48/56 |
| number_perturb | 0.811 | 525/647 |

### numeric_gate
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| number_perturb | 0.901 | 583/647 |
| fabricated_detail | 0.431 | 25/58 |
| negation_flip | 0.000 | 0/121 |
| entity_swap | 0.000 | 0/118 |
| unsupported_generalization | 0.000 | 0/56 |

### precision_stack
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| negation_flip | 1.000 | 121/121 |
| entity_swap | 1.000 | 118/118 |
| fabricated_detail | 1.000 | 58/58 |
| number_perturb | 0.926 | 599/647 |
| unsupported_generalization | 0.857 | 48/56 |

### secondary_judge
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| negation_flip | 1.000 | 121/121 |
| entity_swap | 1.000 | 118/118 |
| fabricated_detail | 1.000 | 58/58 |
| unsupported_generalization | 1.000 | 56/56 |
| number_perturb | 0.961 | 622/647 |

### ship_stack
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| negation_flip | 1.000 | 121/121 |
| entity_swap | 1.000 | 118/118 |
| fabricated_detail | 1.000 | 58/58 |
| unsupported_generalization | 1.000 | 56/56 |
| number_perturb | 0.983 | 636/647 |

## Recall by category

### ensemble_and
- **claim_verification**: 1.000 (250/250)
- **data_extraction**: 0.580 (145/250)
- **reasoning_numeric**: 0.904 (226/250)
- **summarization**: 0.936 (234/250)

### ensemble_or
- **claim_verification**: 1.000 (250/250)
- **data_extraction**: 0.956 (239/250)
- **reasoning_numeric**: 1.000 (250/250)
- **summarization**: 1.000 (250/250)

### hhem_gate
- **claim_verification**: 0.692 (173/250)
- **data_extraction**: 0.860 (215/250)
- **reasoning_numeric**: 0.996 (249/250)
- **summarization**: 0.376 (94/250)

### node2_critic
- **claim_verification**: 1.000 (250/250)
- **data_extraction**: 0.620 (155/250)
- **reasoning_numeric**: 0.920 (230/250)
- **summarization**: 0.936 (234/250)

### numeric_gate
- **claim_verification**: 0.340 (85/250)
- **data_extraction**: 0.772 (193/250)
- **reasoning_numeric**: 0.992 (248/250)
- **summarization**: 0.328 (82/250)

### precision_stack
- **claim_verification**: 1.000 (250/250)
- **data_extraction**: 0.812 (203/250)
- **reasoning_numeric**: 1.000 (250/250)
- **summarization**: 0.964 (241/250)

### secondary_judge
- **claim_verification**: 1.000 (250/250)
- **data_extraction**: 0.916 (229/250)
- **reasoning_numeric**: 0.984 (246/250)
- **summarization**: 1.000 (250/250)

### ship_stack
- **claim_verification**: 1.000 (250/250)
- **data_extraction**: 0.956 (239/250)
- **reasoning_numeric**: 1.000 (250/250)
- **summarization**: 1.000 (250/250)
