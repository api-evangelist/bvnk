---
name: Set up a BVNK merchant and pull a transaction report
description: Create and list merchant IDs, discover supported currencies, and export a transaction report for reconciliation.
api: openapi/bvnk-api-endpoints-openapi-original.yml
operations: [merchantIdCreate, merchantIdList, listCurrenciesFiat, listCurrenciesCrypto, listCurrenciesDeposit, transactionReport]
---

# Set up a BVNK merchant and pull a transaction report

## Steps
1. **Create the merchant** — `merchantIdCreate` (`POST /api/v1/merchant`) with an idempotency key.
2. **List merchants** — `merchantIdList` (`GET /api/v1/merchant`) to confirm and to resolve the
   `merchantId` used elsewhere (e.g. by `quoteList`).
3. **Discover currencies** — `listCurrenciesFiat`, `listCurrenciesCrypto`, `listCurrenciesDeposit`.
   Currency codes are ISO-4217. Do not hard-code the supported set; it changes.
4. **Report** — `transactionReport` (`GET /api/transaction/report`) for reconciliation. Compare the
   result against your own ledger; see the reconciliation guides on docs.bvnk.com.

## Rules
- Attach up to **10** `metadata` key/value pairs to requests that support it (keys ≤ 40 chars,
  values ≤ 255 chars, no `<` or `>`). Metadata is echoed on status endpoints and on related webhooks —
  it is the cheapest way to carry your own order id through the whole lifecycle.
- Reports and ledger events also arrive as webhooks (`bvnk:ledger:report:ready`) — subscribe rather
  than poll. See `asyncapi/bvnk-webhooks.yml`.
