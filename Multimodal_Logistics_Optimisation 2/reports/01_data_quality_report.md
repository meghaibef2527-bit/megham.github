# Data Quality Report

| Table | Rows | Columns | Duplicate rows | Missing cells |
|---|---:|---:|---:|---:|
| SHIPMENTS | 5,000 | 18 | 0 | 0 |
| HUBS | 60 | 11 | 0 | 0 |
| TRANSPORT_NETWORK | 393 | 15 | 0 | 0 |
| ROUTE_OPTIONS | 516 | 15 | 0 | 0 |
| ROUTE_LEGS | 804 | 13 | 0 | 0 |
| SHIPMENT_RECOMMENDATIONS | 5,000 | 16 | 0 | 756 |

The 126 missing route-output rows in SHIPMENT_RECOMMENDATIONS correspond exactly to `NO FEASIBLE ROUTE`, so they are intentional outcome fields rather than missing operational data. No records were silently removed. Key relationships between shipments, routes and route legs were checked.
