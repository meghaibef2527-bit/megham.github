# Power BI Model and DAX

## Model
Facts: `FactShipments`, `FactOptimizedRoutes`, `FactRouteLegs`.
Dimensions: `DimDate`, `DimCity`, `DimHub`, `DimMode`, `DimCargo`, `DimPriority`.

## Core DAX

```DAX
Total Shipments = DISTINCTCOUNT(FactShipments[shipment_id])

Total Logistics Cost = SUM(FactOptimizedRoutes[total_cost_usd])

Average Logistics Cost = DIVIDE([Total Logistics Cost], [Total Shipments])

Average Cost per Tonne =
DIVIDE([Total Logistics Cost], SUM(FactOptimizedRoutes[shipment_weight_tonnes]))

Average Transit Time = AVERAGE(FactOptimizedRoutes[transit_time_hours])

Feasible Shipments =
CALCULATE(
    DISTINCTCOUNT(FactOptimizedRoutes[shipment_id]),
    FactOptimizedRoutes[route_status] = "Feasible"
)

Infeasible Shipments = [Total Shipments] - [Feasible Shipments]

SLA Compliance % = DIVIDE([Feasible Shipments], [Total Shipments])

Total Road Baseline Cost = SUM(FactOptimizedRoutes[road_cost_usd])

Total Optimised Cost = [Total Logistics Cost]

Total Cost Savings = SUM(FactOptimizedRoutes[cost_saving_usd])

Cost Savings % = DIVIDE([Total Cost Savings], [Total Road Baseline Cost])

Average Reliability = AVERAGE(FactOptimizedRoutes[reliability_percentage])

Total Emissions = SUM(FactOptimizedRoutes[emissions_kg])
```

## Dashboard pages
1. Executive Overview — KPIs, mode mix, cost, SLA, savings.
2. Route Optimisation — shipment route table and road-vs-optimised comparison.
3. Network Analysis — hub/city maps, demand lanes, bottlenecks and SLA feasibility.
4. Scenario Analysis — fuel, SLA, capacity and weight sensitivity.
