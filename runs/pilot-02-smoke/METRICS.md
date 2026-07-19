# pilot-02-smoke — metrics summary

Exported: 2026-07-19T04:49:47.761070+00:00
Results: 120 (errors: 0)
Corpus cases: 2000
Seeds: {'example_steel_brief.txt': 2000}

## Ship targets
- Recall ≥ 0.95
- FPR ≤ 0.012
- Precision @ 5% prevalence ≥ 0.8

## Per-judge headline

| Judge | n | Acc | Recall | FPR | Prec@5% | Ship? |
|-------|---|-----|--------|-----|---------|-------|
| ensemble_and | 15 | 0.867 | 0.818 | 0.000 | 1.000 | partial |
| ensemble_or | 15 | 0.933 | 1.000 | 0.250 | 0.174 | partial |
| hhem_gate | 15 | 0.933 | 0.909 | 0.000 | 1.000 | partial |
| node2_critic | 15 | 0.800 | 0.818 | 0.250 | 0.147 | N |
| numeric_gate | 15 | 0.733 | 0.636 | 0.000 | 1.000 | partial |
| precision_stack | 15 | 1.000 | 1.000 | 0.000 | 1.000 | partial |
| secondary_judge | 15 | 1.000 | 1.000 | 0.000 | 1.000 | partial |
| ship_stack | 15 | 0.933 | 1.000 | 0.250 | 0.174 | partial |

## Recall by failure type

### ensemble_and
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| negation_flip | 1.000 | 3/3 |
| entity_swap | 1.000 | 1/1 |
| fabricated_detail | 1.000 | 1/1 |
| number_perturb | 0.667 | 4/6 |

### ensemble_or
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| number_perturb | 1.000 | 6/6 |
| negation_flip | 1.000 | 3/3 |
| entity_swap | 1.000 | 1/1 |
| fabricated_detail | 1.000 | 1/1 |

### hhem_gate
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| number_perturb | 1.000 | 6/6 |
| entity_swap | 1.000 | 1/1 |
| fabricated_detail | 1.000 | 1/1 |
| negation_flip | 0.667 | 2/3 |

### node2_critic
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| negation_flip | 1.000 | 3/3 |
| entity_swap | 1.000 | 1/1 |
| fabricated_detail | 1.000 | 1/1 |
| number_perturb | 0.667 | 4/6 |

### numeric_gate
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| number_perturb | 1.000 | 6/6 |
| fabricated_detail | 1.000 | 1/1 |
| negation_flip | 0.000 | 0/3 |
| entity_swap | 0.000 | 0/1 |

### precision_stack
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| number_perturb | 1.000 | 6/6 |
| negation_flip | 1.000 | 3/3 |
| entity_swap | 1.000 | 1/1 |
| fabricated_detail | 1.000 | 1/1 |

### secondary_judge
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| number_perturb | 1.000 | 6/6 |
| negation_flip | 1.000 | 3/3 |
| entity_swap | 1.000 | 1/1 |
| fabricated_detail | 1.000 | 1/1 |

### ship_stack
| failure_type | recall | hit/total |
|--------------|--------|-----------|
| number_perturb | 1.000 | 6/6 |
| negation_flip | 1.000 | 3/3 |
| entity_swap | 1.000 | 1/1 |
| fabricated_detail | 1.000 | 1/1 |

## Recall by category

### ensemble_and
- **claim_verification**: 1.000 (4/4)
- **data_extraction**: 0.500 (1/2)
- **reasoning_numeric**: 0.667 (2/3)
- **summarization**: 1.000 (2/2)

### ensemble_or
- **claim_verification**: 1.000 (4/4)
- **data_extraction**: 1.000 (2/2)
- **reasoning_numeric**: 1.000 (3/3)
- **summarization**: 1.000 (2/2)

### hhem_gate
- **claim_verification**: 0.750 (3/4)
- **data_extraction**: 1.000 (2/2)
- **reasoning_numeric**: 1.000 (3/3)
- **summarization**: 1.000 (2/2)

### node2_critic
- **claim_verification**: 1.000 (4/4)
- **data_extraction**: 0.500 (1/2)
- **reasoning_numeric**: 0.667 (2/3)
- **summarization**: 1.000 (2/2)

### numeric_gate
- **claim_verification**: 0.000 (0/4)
- **data_extraction**: 1.000 (2/2)
- **reasoning_numeric**: 1.000 (3/3)
- **summarization**: 1.000 (2/2)

### precision_stack
- **claim_verification**: 1.000 (4/4)
- **data_extraction**: 1.000 (2/2)
- **reasoning_numeric**: 1.000 (3/3)
- **summarization**: 1.000 (2/2)

### secondary_judge
- **claim_verification**: 1.000 (4/4)
- **data_extraction**: 1.000 (2/2)
- **reasoning_numeric**: 1.000 (3/3)
- **summarization**: 1.000 (2/2)

### ship_stack
- **claim_verification**: 1.000 (4/4)
- **data_extraction**: 1.000 (2/2)
- **reasoning_numeric**: 1.000 (3/3)
- **summarization**: 1.000 (2/2)
