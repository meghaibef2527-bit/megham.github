# Multimodal Logistics Cost & Route Optimisation — Dataset & Methodology

**Project:** Multimodal Logistics Cost & Route Optimisation using Python, Excel and Power BI
**Scope:** 20 Indian demand cities · 60 hubs · 393 network connections · 5,000 shipments · 516 route alternatives

This is a **synthetic but internally consistent** dataset — not scraped real freight-rate data. Every number is generated from realistic parameter ranges (road cheaper-but-slower than rail is enforced, air cheaper-but-slower than nothing but expensive is enforced, etc.), so the *relationships* in the data hold up under analysis, even though individual figures aren't sourced from a real freight index. Say this plainly if asked in an interview — it's a modelling and optimisation exercise, not a claim to have scraped Maersk's real rate card.

---

## 1. Table overview

| Table | Rows | Grain | Purpose |
|---|---|---|---|
| `SHIPMENTS` | 5,000 | one row per shipment | demand side — what needs to move, when, how urgently |
| `HUBS` | 60 | one row per terminal/warehouse | supply-side infrastructure — where cargo can be handled/transshipped |
| `TRANSPORT_NETWORK` | 393 | one row per city/hub-pair connection, per mode | the "edges" of the routing graph — cost, time, capacity, reliability per mode |
| `ROUTE_OPTIONS` | 516 | one row per candidate route (multiple per OD pair) | pre-built route alternatives, costed at a standard 10-tonne reference load |
| `ROUTE_LEGS` | 804 | one row per leg within a route | the underlying leg-by-leg breakdown behind each `ROUTE_OPTIONS` row |
| `SHIPMENT_ROUTE_RECOMMENDATIONS` | 5,000 | one row per shipment | **the actual optimisation output** — each shipment matched to its lowest-cost SLA- and capacity-feasible route |

---

## 2. Why six tables instead of one flat file

This mirrors how a real logistics data model is structured — dimension tables (`HUBS`) separate from fact/transaction tables (`SHIPMENTS`), and a network/graph table (`TRANSPORT_NETWORK`) that routing logic runs against, rather than pre-flattening everything. This is a stronger portfolio signal than one wide table: it shows you can model a network problem relationally, not just produce a spreadsheet.

---

## 3. Key modelling decisions (be ready to explain these)

**`cost_per_km` in `TRANSPORT_NETWORK` is USD per tonne-km, not a flat per-km rate.** This is what lets transport cost scale correctly with shipment weight across very different vehicle/vessel sizes (a 25-tonne truck vs. a 15,000-tonne ship) — a flat per-km rate would make heavier shipments artificially cheap on high-capacity modes. This is the single most important modelling choice in the dataset and the one most likely to get a follow-up question.

**Mode parameters are deliberately ordered to match real-world logistics economics:**
- **Sea** — cheapest per tonne-km, slowest, most variable reliability (port congestion, weather)
- **Rail** — cheaper than road for long-haul, slower door-to-door, needs a rail terminal at both ends
- **Road** — moderate cost, most flexible (works between any two cities), most consistent reliability
- **Air** — most expensive by a wide margin, fastest, highest emissions per tonne-km

**Multimodal routes use realistic first/last-mile logic.** A city without a rail terminal, port, or airport doesn't get direct access to that mode — instead, the route-generation engine finds the *nearest* city that has one (within a feasibility radius, e.g. 400km for rail/air, 450km for sea) and builds a `Road → [mode] → Road` route through it. If no gateway is within radius, that mode isn't offered for that city pair at all — e.g., most inland city pairs simply have no sea option, which is realistic.

**`ROUTE_OPTIONS` costs are at a standard 10-tonne reference load**, so routes can be compared like-for-like independent of any specific shipment. The optimizer (`SHIPMENT_ROUTE_RECOMMENDATIONS`) re-derives real costs per shipment from `ROUTE_LEGS`' underlying per-tonne-km rates, scaled to that shipment's actual weight — this is why the recommendation table's costs won't exactly match `total_route_cost` in `ROUTE_OPTIONS` for any given shipment.

**Capacity is checked at the leg level.** A route is only feasible for a shipment if every leg's `capacity_tonnes` can carry the shipment's weight — a single overloaded leg makes the whole multimodal route infeasible for that shipment, which is realistic (you can't run half a truckload through a leg that can't carry it).

---

## 4. The optimisation logic (`05_optimizer.py`)

For each of the 5,000 shipments:
1. Look up all pre-built candidate routes for that shipment's origin–destination pair (average ~2.7 alternatives per pair, up to 5 for well-connected pairs).
2. Rescale each candidate's cost, transit time, reliability, and emissions to the shipment's actual weight using the leg-level rates.
3. Filter to routes where `estimated_transit_time_hours <= required_delivery_hours` **and** every leg's capacity covers the shipment's weight.
4. Among feasible routes, pick the one with the lowest `estimated_total_cost_usd`.
5. If no route is feasible, flag it explicitly rather than forcing a choice — this is a deliberate, realistic outcome, not a bug.

**Headline result on this dataset:** 97.5% of shipments (4,874 / 5,000) have at least one SLA- and capacity-feasible route; the remaining 126 are overwhelmingly Express-priority shipments (95 of 126) whose delivery window is simply too tight for the network as modelled. **This is a genuine, presentable finding** — "here's where the network can't currently meet demand" is exactly the kind of insight a route-optimisation project should surface, not just "here's the cheapest option every time."

**Recommended mode mix among feasible shipments:** Road dominates (3,331 shipments) as expected for door-to-door flexibility, but multimodal Road→Rail→Road (252) and Road→Air→Road (128) routes are chosen specifically when they're cheaper than pure road for that shipment's weight/distance/SLA combination — a good chart for Power BI to visualize (cost saved by mode vs. pure road baseline).

---

## 5. Suggested Power BI build-out

- **Mode-mix by priority tier** (stacked bar): shows Express leans road/air, Economy leans rail/sea
- **Cost per tonne-km by mode** (from `ROUTE_LEGS`): validates the sea < rail < road < air ordering visually
- **SLA feasibility rate by origin city / cargo type**: surfaces which lanes are under-served
- **Map visual** using `HUBS` lat/long and `SHIPMENTS` origin/destination lat/long, with `ROUTE_LEGS` as flow lines
- **Cost savings waterfall**: pure-road cost vs. recommended-route cost, summed across all feasible shipments, to quantify the $ value of multimodal optimisation

---

## 6. Known simplifications (state these upfront if asked)

- Distances are derived from great-circle (haversine) coordinates with a mode-specific circuity factor, not real road/rail/shipping-lane geometry.
- The network is undirected (cost/time assumed symmetric in both directions), which is a simplification for road/rail/air but less true for sea (backhaul rates often differ).
- Only 20 cities and 7 named ports are modelled — a real Maersk-scale network would cover hundreds of origin/destination points and dozens of ports.
- `ROUTE_OPTIONS` is limited to the OD pairs that appear in `SHIPMENTS` (190 unique pairs) rather than all 190 possible pairs among the 20 cities, which happen to be the same in this dataset since shipments cover every pair — but this was a deliberate choice, not a coincidence to assume in general.
