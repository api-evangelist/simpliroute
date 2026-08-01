---
name: Track deliveries with webhooks and checkout
description: Subscribe to SimpliRoute route/visit lifecycle webhooks and read checkout results to track deliveries in real time.
api: https://documentation.simpliroute.com/
operations:
  - "POST /v1/addons/webhooks/"
  - "GET /v1/addons/webhooks/"
  - "POST /v1/mobile/visit/{visit_id}/checkout/"
  - "GET /v1/routes/visits/{visit_id}/"
---

# Track deliveries with webhooks and checkout

Use this skill to receive real-time delivery status from SimpliRoute and reconcile it into your system.

## Auth
Send `Authorization: Token <your_token>` and `Content-Type: application/json`. Base URL `https://api.simpliroute.com`.

## Steps
1. **Register a webhook.** `POST /v1/addons/webhooks/` with your receiving URL and the event(s) to subscribe to.
2. **List / confirm subscriptions.** `GET /v1/addons/webhooks/`.
3. **Receive events.** SimpliRoute POSTs to your URL on: `route.started`, `on_its_way`, `checkout`, `checkout.detailed`, `route.finish`, plus plan/route creation & edition (see `asyncapi/simpliroute-webhooks.yml`).
4. **On a checkout event, fetch detail.** `GET /v1/routes/visits/{visit_id}/` to read the final visit state, extra-field values, observations and pictures.
5. **(Driver/mobile flows)** `POST /v1/mobile/visit/{visit_id}/checkout/` to record a checkout programmatically.

## Rules
- Treat webhook delivery as at-least-once; dedupe on the visit/route id since there is no idempotency contract.
- The only verbatim payload event string observed in the docs is `ROUTE_STARTED`; other event slugs are normalized from the reference — confirm exact strings against a live subscription.
- Reconcile against the REST resource as the source of truth after each event.
