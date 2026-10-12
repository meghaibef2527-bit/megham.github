# Methodology

The model minimises shipment-specific logistics cost subject to SLA, leg-level capacity and network feasibility.

**Leg transport cost** = shipment tonnes × distance km × USD per tonne-km.

**Route cost** = sum of variable transport cost + fixed cost + handling cost across route legs.

Transit time is the sum of leg times. Capacity feasibility requires every leg to have enough capacity. Reliability is calculated as the product of leg reliability probabilities. Emissions equal shipment tonnes multiplied by the sum of distance × emission factor across legs.

Candidate routes are selected from the supplied route set by origin-destination pair. The model recalculates economics from route-leg data at actual shipment weight, filters infeasible candidates, and selects the minimum-cost feasible route.

Scenario fuel-cost shocks are a sensitivity proxy applied to variable transport cost because the synthetic data does not separately identify fuel cost.

The dataset is synthetic. It is not actual Maersk operational data, carrier contract rates, or live freight pricing.
