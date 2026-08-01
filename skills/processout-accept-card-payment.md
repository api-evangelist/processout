---
name: Accept a card payment
description: Create a customer, tokenize a card, create an invoice, then authorize and capture a card payment through ProcessOut Smart Router.
api: openapi/processout-openapi.json
operations: [creating-a-customer, creating-a-card, create-an-invoice, authorizing-an-invoice, capture-an-invoice]
---

# Accept a card payment

Use the ProcessOut API to take a one-off card payment. Base URL `https://api.processout.com`.

## Auth
HTTP Basic. Username = project ID, password = secret key. In the sandbox use the
`test-` prefixed project ID and a `key_sandbox_` / `key_test_` secret. Collect and
tokenize card data client-side with ProcessOut.js — never send raw PAN through your
own server unless you are PCI-scoped.

## Rules
- Send an `Idempotency-Key` header on every create/authorize/capture call so retries
  never double-charge (see `conventions/processout-conventions.yml`).
- Watch `x-ratelimit-remaining`; back off on HTTP 429.
- Payment declines return a unified code — resolve against `errors/processout-decline-codes.yml`
  (hard declines: do not retry; soft declines: may retry).

## Steps
1. `creating-a-customer` — `POST /customers` to create the payer (returns `cust_...`).
2. `creating-a-card` — `POST /cards` to store the card token in the PCI vault
   (typically the token produced by ProcessOut.js; returns `card_...`).
3. `create-an-invoice` — `POST /invoices` with `customer_id`, `name`, `amount`,
   `currency` (and optional `webhook_url`).
4. `authorizing-an-invoice` — `POST /invoices/{invoice_id}/authorize` with the card
   source to authorize; handle 3DS/SCA if required.
5. `capture-an-invoice` — `POST /invoices/{invoice_id}/capture` to capture funds.

## Testing
Use the sandbox test cards in `sandbox/processout-sandbox.yml` (e.g. `4242424242424242`
succeeds; `4000000000000101` triggers 3DS; `4000000000002206` fails `card.no-money`).
