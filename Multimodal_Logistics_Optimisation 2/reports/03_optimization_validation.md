# Optimization Validation Report

| Metric | Result |
|---|---:|
| Shipments tested | 5,000 |
| Route agreement | 99.64% |
| Mode agreement | 99.64% |
| SLA agreement | 99.98% |
| Route discrepancies | 18 |
| Reference infeasible | 126 |
| Independent infeasible | 125 |

The independent model was not forced to match the supplied recommendation table. The discrepancies are retained for investigation. Most route differences arise from the independent leg-level cost calculation selecting a different feasible route; this is treated as a model-validation finding rather than hidden.
