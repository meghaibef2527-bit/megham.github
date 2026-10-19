# megham.github
Author - Megha Maitreyi
# Multimodal Logistics Optimisation

**A supply chain analytics project focused on cost minimisation, route feasibility, service-level agreements (SLAs), and transport-mode trade-offs.**

## Project Overview

How can a shipment be transported at the lowest possible logistics cost without violating its delivery deadline or the capacity limits of the transport network?

This project develops a shipment-level route optimisation framework using synthetic logistics data. It evaluates alternative transport routes, recalculates costs based on shipment weight and route legs, checks service-level and capacity constraints, and selects the lowest-cost feasible candidate.

The analysis explores the trade-offs between cost, transit time, capacity, reliability, emissions, and transport-mode selection.

> **Data disclaimer:** All data is synthetic and created for analytical demonstration. This project does not use actual Maersk operational data, live freight rates, carrier contracts, or realised commercial savings.

## Business Problem

Choosing the cheapest route in isolation can lead to missed delivery deadlines, capacity violations, or unsuitable transport arrangements.

A logistics decision-support system must therefore answer four questions:

1. Which shipments have at least one feasible route?
2. Which route offers the lowest cost while meeting the shipment's SLA and capacity requirements?
3. How do transport modes and route alternatives affect cost and transit time?
4. How sensitive are routing decisions to fuel-cost changes, tighter delivery deadlines, capacity reductions, and heavier shipments?

## Project Objectives

- Minimise shipment-specific logistics costs subject to operational constraints.
- Evaluate alternative routes using shipment weight and route-leg economics.
- Measure route feasibility and SLA compliance.
- Compare road, rail, and multimodal transport choices.
- Quantify modelled cost savings against a feasible direct-road baseline.
- Assess operational sensitivity under alternative scenarios.
- Validate optimisation outputs against reference recommendations.

## Dataset

The project contains synthetic shipment and transport-network data.

| Dataset | Records | Purpose |
|---|---:|---|
| Shipments | 5,000 | Shipment characteristics, origins, destinations, priorities, and delivery requirements |
| Hubs | 60 | Transport hubs and network attributes |
| Transport network | 393 | Network connections and transport attributes |
| Route options | 516 | Candidate routes for shipment origin–destination pairs |
| Route legs | 804 | Leg-level route information and cost inputs |
| Shipment recommendations | 5,000 | Reference route recommendations and feasibility outcomes |

The data-quality report found no duplicate rows in these tables. The 126 blank route outputs in the reference recommendations correspond to records marked as having no feasible route.

## Tools and Technologies

- **Microsoft Excel:** analytical workbook, scenario comparisons, and reporting.
- **Power BI:** data modelling, DAX measures, and dashboard design.
- **CSV datasets:** structured inputs, outputs, and validation tables.
- **Data analysis and optimisation logic:** route evaluation, feasibility checks, and shipment-specific cost calculations.

## Methodology

### 1. Route generation and evaluation

Candidate routes are evaluated for the relevant shipment origin–destination pair. Route economics are recalculated using the actual shipment weight and route-leg data.

### 2. Cost calculation

Variable transport cost is calculated as:

`Shipment weight × Distance × Cost per tonne-km`

Total route cost combines variable transport cost, fixed costs, and handling costs across the route's legs.

### 3. Feasibility constraints

Each candidate is evaluated against:

- **SLA:** total transit time must meet the shipment's delivery requirement.
- **Capacity:** every route leg must have sufficient capacity.
- **Network feasibility:** the candidate must be a valid route for the shipment's origin and destination.

The lowest-cost candidate that satisfies the model's constraints is selected.

### 4. Operational metrics

The analysis also considers route reliability and estimated emissions. Route reliability is calculated as the product of leg-level reliability probabilities, while emissions are estimated from shipment weight, distance, and emission factors.

### 5. Scenario analysis

The model evaluates sensitivity to:

- Fuel-cost shocks, applied as a proxy to variable transport costs.
- Delivery deadlines tightened by 30%.
- Transport capacity reductions.
- Shipment weights increased by 50%.

These scenarios illustrate how changes in operating conditions may affect route feasibility and cost.

## Key Findings

The following results are from the synthetic model's business-insights report. They are analytical outputs, not verified commercial outcomes.

### Network feasibility

- **4,875 of 5,000 shipments** were feasible in the independent optimisation model, equivalent to 97.50%.
- **125 shipments** had no feasible route under the model's constraints.
- Express shipments had a reported feasibility rate of 89.08%, indicating that tighter delivery windows can constrain route availability.

### Transport-mode trade-offs

- Road was selected for 3,317 feasible shipments.
- Rail was selected for 909 feasible shipments.
- Multimodal routes represented 8.29% of feasible selections.

These results describe this synthetic network only. They do not establish a universal cost or performance ranking for transport modes.

### Modelled cost savings

For 4,401 shipments, a direct-road route was available that satisfied the model's SLA and capacity requirements.

Against this feasible baseline, the independent optimisation reported:

- **Total modelled savings:** approximately $198,968.
- **Average saving per shipment:** approximately $45.21.
- **Average savings rate:** approximately 5.54%.

These figures remain provisional until the outstanding route-integrity and cost-reconciliation checks are resolved. They must not be interpreted as actual carrier savings or guaranteed business benefits.

### Scenario sensitivity

- Tightening delivery deadlines by 30% reduced modelled feasibility to 94.44%.
- Increasing shipment weight by 50% reduced modelled feasibility to 95.86%.

The results illustrate how service requirements and shipment characteristics can affect the set of feasible routes and the resulting logistics cost.

## Validation and Data Quality

Validation is an important part of this project because a low-cost recommendation is only useful if the route is valid and its economics are consistent.

The validation report recorded:

| Validation metric | Reported result |
|---|---:|
| Route agreement with reference | 99.64% |
| Transport-mode agreement | 99.64% |
| SLA agreement | 99.98% |
| Route discrepancies | 18 |
| Reference infeasible shipments | 126 |
| Independently modelled infeasible shipments | 125 |

The independent model was not forced to reproduce the reference recommendations. Differences were retained for investigation rather than hidden.

**Outstanding validation requirement:** route agreement alone does not establish that each route begins at the shipment's origin and ends at its destination. Origin–destination integrity, route-leg continuity, cost reconciliation, and feasibility counts must be checked together before the final recommendations and savings figures are treated as validated.

## Power BI Data Model

The reporting model is organised around three fact tables:

- `FactShipments`
- `FactOptimizedRoutes`
- `FactRouteLegs`

The dimension tables cover date, city, hub, transport mode, cargo, and priority.

The planned dashboard pages are:

1. **Executive Overview:** shipment volume, logistics cost, feasibility, SLA compliance, mode mix, and savings.
2. **Route Optimisation:** shipment-level recommendations and road-versus-optimised comparisons.
3. **Network Analysis:** hub and city patterns, demand lanes, and feasibility constraints.
4. **Scenario Analysis:** sensitivity to fuel costs, SLAs, capacity, and shipment weight.

The DAX measures include total shipments, total and average logistics cost, cost per tonne, transit time, feasible shipments, SLA compliance, savings, reliability, and estimated emissions.

## Business Implications

The model demonstrates a structured way to approach logistics decisions:

1. Generate candidate routes.
2. Validate network connectivity.
3. Test SLA and capacity constraints.
4. Calculate shipment-specific route economics.
5. Select the lowest-cost feasible option.
6. Validate the recommendation and compare alternative scenarios.

For a real logistics operation, this framework would require verified network data, current carrier rates, operational capacity, calibrated transit-time estimates, and business-approved service constraints before being used for decisions.

## Limitations

- The dataset and operating conditions are synthetic.
- The high-level fuel-cost shock is a sensitivity proxy, not a separately measured fuel-cost component.
- Modelled savings depend on the chosen baseline, candidate routes, cost assumptions, and constraints.
- The independent recommendations and reference outputs contain unresolved discrepancies.
- The results do not establish real-world savings, service improvements, or environmental benefits.

## Repository Structure

```text
Multimodal_Logistics_Optimisation/
├── data/
│   └── raw/
├── excel/
├── outputs/
├── reports/
└── README.md
```

The `data/raw` directory contains input data, `excel` contains the analytical workbook, `outputs` contains generated tables and model results, and `reports` contains documentation and validation findings.

## How to Explore the Project

1. Start with the Excel workbook to understand the dashboard and scenario analysis.
2. Read the methodology report to understand the objective function, cost calculations, and constraints.
3. Review the data-quality and optimisation-validation reports.
4. Explore the output tables and compare them with the reference recommendations.
5. Treat the reported savings as provisional until the outstanding validation checks are resolved.

The repository currently documents the analytical workflow and outputs. A fully reproducible end-to-end run guide should be added if the optimisation-generation code is made available.

## Conclusion

This project demonstrates how data analytics can support constrained logistics decision-making by bringing together route economics, delivery deadlines, capacity, transport-mode trade-offs, and scenario sensitivity.

Its central lesson is that **the cheapest route is not necessarily the best route: the decision must account for feasibility, service requirements, and the reliability of the underlying analysis.**

---

**Project type:** Supply Chain Analytics · Operations Research · Cost Optimisation

**Data:** Synthetic

**Status:** Analytical case study with outstanding route-integrity and cost-validation checks.
