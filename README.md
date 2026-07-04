# QSR Operational Digital Twin (Discrete-Event Simulation)

A discrete-event simulation of a high-volume quick-service restaurant, built in SIMUL8 and calibrated against a KFC franchise on Queen Street, Cardiff. The model is used as a "what-if" tool to test operational changes — staffing, menu routing, seating policy, front-end configuration — before committing to them in the real world.

## About this model

The model traces the full customer journey across four modules: order placement, kitchen production, assembly and collection, and dine-in seating. It handles multiple order channels (eat-in, takeaway, delivery, app pre-order) with channel-specific arrival patterns and a delivery-priority rule, and it models real constraints: staff resources, equipment capacity, and table availability.

Arrivals are driven by a time-dependent distribution built from the site's hourly demand pattern, so the model reproduces the daily lunch and evening peaks rather than assuming a flat arrival rate.

## Data and calibration

Inputs were triangulated from three sources rather than assumed, to ensure accuracy in simulation:

- **Manager survey** — channel split (50/25/25 eat-in/takeaway/delivery), staffing by shift, kitchen processing times, balking estimates.
- **Direct timed observation** — kiosk, cashier, and table-occupancy times sampled from 15 customer journeys, used to fit the on-site service-time distributions. 
- **Google Maps popular-times data** — hourly demand shape, used to parameterise the time-dependent arrival rate.

## Validation

Being a mechanistic model informing a capacity decision, validation is about matching aggregate observed behaviour, not held-out prediction. The model was checked against two independent field-observed quantities:

- **Daily throughput:** simulated output of 503 completed orders against ~518 estimated from site data — a 2.9% variance.
- **Balking:** the model produced 4–11 lost customers per busy day, matching the manager's independently-stated estimate of 3–10. This figure was never an input — it emerged from the process logic — which makes it the stronger of the two checks.

## Key findings

- **Kitchen speed is the critical constraint.** Increasing complex-meal cook time from sub-minute to 5 minutes didn't degrade the system gracefully — it collapsed it, with service level dropping to ~39% and balking rising from near-zero to 259 customers. Sub-minute kitchen times aren't an optimisation target; they're a survival condition.
- **The bottleneck shifts.** Adding a fifth cook during a simulated promotion barely moved balking and dropped cook utilisation from 71% to 57% — the constraint had already moved downstream to packing. Adding capacity in the wrong place buys nothing.
- **"Reserve-at-order" seating is worse than first-come-first-served.** Reserving a table at order time blocks it through the whole food-prep wait, cutting turnover. Under constrained seating (10 tables), automated allocation raised weekly balking from 240 to 260. Decoupling parallel activities beat imposing a rigid dependency.
- **Cashiers are largely redundant at volume.** Kiosk-only (384 orders) and hybrid (385 orders) throughput were within 1%, while cashier-only failed under load (25-min+ waits, 19 balkers).
 SIMUL8 to open; included as the source artifact rather than something most readers will run.

## Tools

SIMUL8 (discrete-event simulation), with spreadsheet-driven Visual Logic for data-driven routing and profit calculation. Menu routing, combo logic, and per-item profit are held in a custom external spreadsheet (`ss_KitchenRouting`) so scenarios can be reconfigured without editing model internals.
