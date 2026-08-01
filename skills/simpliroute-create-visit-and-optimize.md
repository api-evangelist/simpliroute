---
name: Create delivery visits and optimize routes
description: Add delivery/pickup visits to SimpliRoute and run the optimization engine to build vehicle routes for a date.
api: https://documentation.simpliroute.com/
operations:
  - "POST /v1/routes/visits/"
  - "GET /v1/routes/visits/"
  - "POST /v1/plans/"
  - "POST /v1/routes/routes/"
  - "GET /v1/plans/routes/{route_id}/visits/"
---

# Create delivery visits and optimize routes

Use this skill to load delivery work into SimpliRoute and produce optimized vehicle routes.

## Auth
Every request sends `Authorization: Token <your_token>` and `Content-Type: application/json`.
The base URL is `https://api.simpliroute.com`. Verify the token with `GET /v1/accounts/me/`.

## Steps
1. **Create visits.** `POST /v1/routes/visits/` with the stop's address/coordinates, planned date, time window, load/capacity and any required `skills` (a visit can be created together with its `items`).
2. **Confirm the day's work.** `GET /v1/routes/visits/` (filter by date) to confirm all visits for the target date are present.
3. **Create a plan.** `POST /v1/plans/` to open a daily plan and attach the vehicles that will serve the visits.
4. **Optimize / create routes.** `POST /v1/routes/routes/` (or the optimization engine at optimizator.simpliroute.com) to assign visits to vehicles honoring capacity, time windows and skill compatibility.
5. **Read back the result.** `GET /v1/plans/routes/{route_id}/visits/` for the ordered visit sequence per route.

## Rules
- Vehicle/visit `skills` must be compatible or the visit is left off-route.
- When a visit is dropped, the response carries an off-route reason `code` (EXC_PAR-*, EXC_SO-*, EXC_UNK-000) — see `errors/simpliroute-error-codes.yml` to interpret and remediate (add capacity, working time, or a compatible vehicle).
- Errors are signaled with HTTP status codes + a JSON body; no idempotency-key retry contract exists, so do not blindly retry POSTs.
