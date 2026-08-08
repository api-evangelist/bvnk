---
name: Create a BVNK wallet and read its balance
description: Create a merchant wallet on BVNK, list wallets, read a single wallet and pull balances, using Hawk-signed requests.
api: openapi/bvnk-api-endpoints-openapi-original.yml
operations: [walletCreate, walletList, walletRGet, walletBalanceList]
---

# Create a BVNK wallet and read its balance

## Before you start
- Authenticate every request with **Hawk** (HMAC-SHA256). Build the `Authorization` header from your
  `Hawk Auth ID` and `Hawk Auth Key`. Hawk payload validation is **not** enabled on the BVNK API — do not
  compute a payload hash. See `authentication/bvnk-authentication.yml`.
- Use `https://api.sandbox.bvnk.com` first. Production is `https://api.bvnk.com`. Credentials are per
  environment; there is no key prefix that tells the two apart.

## Steps
1. **Create the wallet** — `walletCreate` (`POST /api/wallet`). Send an `X-Idempotency-Key` header
   (UUID, 36 chars) so a network timeout cannot create two wallets. On success expect `201`.
2. **Confirm it exists** — `walletList` (`GET /api/wallet`). This endpoint is paginated: use `page`
   (zero-based), `size` (max 100) and `sort=property,asc|desc`.
3. **Read the wallet** — `walletRGet` (`GET /api/wallet/{id}`) with the id returned in step 1.
4. **Read balances** — `walletBalanceList` (`GET /api/wallet/balances`) for the balance across wallets.

## Rules
- **Retry safely.** Replaying a request with the same `X-Idempotency-Key` returns `400 Bad Request`
  saying a request with that ID already exists — that is the success signal for "already processed",
  not a failure to fix. Failed requests are not cached, so they may be retried freely.
  v.2 endpoints use `Idempotency-Key` instead of `X-Idempotency-Key`.
- **Errors** come back as `{code, status, traceId, message, details}`. Quote the `traceId` to support.
  Look the `code` up in `errors/bvnk-error-codes.yml` (e.g. `BVNK:LEDGER:5051` = wallet creation failed).
- **Timestamps** in BVNK payloads are UNIX epoch **milliseconds**.
- Wallets v.1 endpoints are deprecated in favour of Wallets v.2 — see `lifecycle/bvnk-lifecycle.yml`.
