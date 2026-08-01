---
name: Save a token and charge for future payments
description: Save a reusable payment token against a customer and charge it later for recurring or one-click payments.
api: openapi/processout-openapi.json
operations: [creating-a-customer, creating-a-token, create-an-invoice, authorizing-an-invoice]
---

# Save a token and charge for future payments

Tokenize a payment instrument once, then reuse it for recurring / one-click charges.
Base URL `https://api.processout.com`; HTTP Basic auth with project keys.

## Rules
- Always send an `Idempotency-Key` header on create/authorize.
- Store the returned token id (`tok_...`) against your customer record.
- On charge, inspect the transaction's unified decline code
  (`errors/processout-decline-codes.yml`) before retrying — respect hard vs soft declines.

## Steps
1. `creating-a-customer` — `POST /customers` (skip if the customer already exists).
2. `creating-a-token` — `POST /customers/{customer_id}/tokens` to save a reusable
   token (from a card the customer entered via ProcessOut.js, or an APM).
3. `create-an-invoice` — `POST /invoices` for the amount to charge, referencing the
   `customer_id`.
4. `authorizing-an-invoice` — `POST /invoices/{invoice_id}/authorize` using the saved
   token as the source; capture separately or authorize-and-capture per your flow.

## Notes
Check a token's stored balance with `fetch-a-token-balance`
(`GET /balances/tokens/{token_id}`) for stored-value instruments.
