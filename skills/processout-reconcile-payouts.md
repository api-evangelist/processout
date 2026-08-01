---
name: Reconcile payouts and reports
description: List and inspect PSP settlement payouts, their line items, and reconciliation reports.
api: openapi/processout-openapi.json
operations: [listing-payouts, fetching-a-payout, listing-payout-items, listing-reports, fetching-report]
---

# Reconcile payouts and reports

Pull ProcessOut's normalized reconciliation data — settlement payouts, their items, and
PSP reports — to reconcile against your ledger. Base URL `https://api.processout.com`;
HTTP Basic auth. `GET /events`-style read endpoints follow standard rate limits; watch
`x-ratelimit-remaining`.

## Steps
1. `listing-payouts` — `GET /payouts` to enumerate settlement payouts (paginate).
2. `fetching-a-payout` — `GET /payouts/{payout_id}` for one payout's totals and status.
3. `listing-payout-items` — `GET /payouts/{payout_id}/items` for the per-transaction
   lines that make up the payout (fees, refunds, chargebacks).
4. `listing-reports` — `GET /uploads/reports` to find reconciliation reports.
5. `fetching-report` — `GET /uploads/reports/{report_id}` (or `downloading-raw-psp-report`
   for the raw PSP file) to pull report detail.

## Notes
Reconciliation ids resolve against the entities in `data-model/processout-data-model.yml`.
Reports can also be delivered via SFTP export (`setting-up-sftp-payouts-export`).
