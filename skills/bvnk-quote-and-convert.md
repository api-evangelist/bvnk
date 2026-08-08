---
name: Quote and convert currency on BVNK
description: Create a conversion quote, accept it inside its validity window, and read quotes back.
api: openapi/bvnk-api-endpoints-openapi-original.yml
operations: [quoteCreate, quoteAccept, quoteRead, quoteList]
---

# Quote and convert currency on BVNK

## Before you start
- Hawk-signed requests, as in the wallet skill.
- Know which currencies are available: `listCurrenciesCrypto`, `listCurrenciesFiat` and
  `listCurrenciesDeposit` return the supported sets.

## Steps
1. **Create a quote** — `quoteCreate` (`POST /api/v1/quote`). The quote is a priced, time-limited offer.
2. **Accept it** — `quoteAccept` (`PUT /api/v1/quote/accept/{uuid}`) before it expires. This is the
   step that moves funds; send an idempotency key.
3. **Read it back** — `quoteRead` (`GET /api/v1/quote/{uuid}`) to confirm the accepted state.
4. **List quotes for a merchant** — `quoteList` (`GET /api/v1/quote/{merchantId}`), paginated.

## Rules
- **Do not re-create a quote to retry an accept.** Re-issue the same `quoteAccept` with the same
  idempotency key; a `400` "request with this ID already exists" means the first accept landed.
- A quote that has expired must be re-created, not re-accepted.
- The legacy Quote API is superseded by **Transfers v.3** for wallet-to-wallet conversion —
  see the migration guide in `lifecycle/bvnk-lifecycle.yml` before building new integrations.
- `404` on `quoteRead` means the uuid is wrong or belongs to another account.
