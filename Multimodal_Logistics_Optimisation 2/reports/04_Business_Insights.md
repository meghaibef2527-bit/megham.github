# Business Insights

1. **Network feasibility:** 4,875/5,000 shipments are feasible under the independent model (97.50%). 125 have no feasible route. Express feasibility is 89.08%, making tight delivery windows a clear network constraint in this model.

2. **Mode trade-offs:** Road is selected for 3,317 feasible shipments and Rail for 909. Multimodal routes account for 8.29% of feasible selections. These are trade-offs between cost, speed, capacity, reliability and network access, not a universal ranking.

3. **Road baseline:** 4,401 shipments have a direct-road route that is itself feasible for SLA and capacity. Against that logically valid baseline, the independent optimisation shows approximately $198,968 of modelled savings, averaging $45.21 (5.54%). These are synthetic-model outputs, not real carrier savings.

4. **SLA sensitivity:** tightening SLA by 30% reduces feasibility to 94.44% and increases average feasible cost as faster/more constrained routes become necessary.

5. **Weight sensitivity:** increasing weight by 50% reduces feasibility to 95.86% and increases average feasible cost.

6. **Operational implication:** route choice should be treated as a constrained decision problem: generate candidates → test capacity and SLA → calculate shipment-specific economics → select the lowest-cost feasible route.
