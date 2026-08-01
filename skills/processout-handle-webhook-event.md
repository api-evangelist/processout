---
name: Handle a webhook event
description: Receive a ProcessOut webhook, fetch the full event by id, and reconcile the associated transaction state idempotently.
api: openapi/processout-openapi.json
operations: [fetching-an-event, fetching-a-transaction]
---

# Handle a webhook event

ProcessOut POSTs a minimal `{ event_id, event_type }` body to your endpoint. Fetch the
full event to read resource state. Base URL `https://api.processout.com`; HTTP Basic auth.

## Rules
- Delivery is **at-least-once** and **unordered** — dedupe on `event_id` and make
  handlers idempotent (see `asyncapi/processout-webhooks.yml`).
- Disable CSRF protection on the webhook route; body is `application/json`.
- Return 2xx quickly; ProcessOut retries at least 12 times over 3 days on failure.

## Steps
1. Read `event_id` from the POST body.
2. `fetching-an-event` — `GET /events/{event_id}` to load the full event and its
   embedded resource (e.g. the transaction/invoice that changed).
3. Branch on `event_type` / event `name` (e.g. `transaction.captured`,
   `invoice.completed`, `invoice.pending`, `transaction.failed`).
4. `fetching-a-transaction` — `GET /transactions/{transaction_id}` if you need the
   authoritative current transaction state, including any unified decline code.

## Notes
`GET /events` and `GET /events/{event_id}` fall under the **secondary** rate limit —
call them in response to webhooks, not in a poll loop.
