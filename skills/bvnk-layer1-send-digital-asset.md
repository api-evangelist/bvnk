---
name: Send a digital asset with Layer1
description: Create a receiving address, validate a destination, estimate the fee, submit a transaction request and track it to confirmation.
api: openapi/bvnk-layer1-digital-asset-openapi-original.yml
operations: [createAddress, validateAddress, estimateTransactionFee, createTransaction, getTransactionRequestById, listTransactions, getTransactionById]
---

# Send a digital asset with Layer1

## Before you start
- Layer1 is authenticated with **OAuth 2.0 client credentials** against the Keycloak realm `bvnk`
  (`https://auth.layer1.com/auth/realms/bvnk/protocol/openid-connect/token`, sandbox issuer
  `auth.sandbox.layer1.com`), **plus RFC 9421 HTTP Message Signatures** on the request.
  Request only the scopes you need — see `scopes/bvnk-scopes.yml`.
- Scopes used by this flow: `addresses:edit`, `addresses:view`, `fee-estimate:edit`,
  `transactions:edit`, `transactions:view`.

## Steps
1. **Create a receiving address** — `createAddress` (`POST /digital/v1/addresses`) with `network` and
   your own `reference`. Returns `200` or `202` (async issuance).
2. **Validate the destination** — `validateAddress` (`POST /digital/v1/addresses/validate`) before
   sending anywhere. Never skip this: a bad chain/address pair is unrecoverable.
3. **Estimate the fee** — `estimateTransactionFee` (`POST /digital/v1/fee-estimate`).
4. **Submit the transfer** — `createTransaction` (`POST /digital/v1/transaction-requests`). Returns
   `202 Accepted` — this is asynchronous, the transfer has not settled.
5. **Track it** — poll `getTransactionRequestById` (`GET /digital/v1/transaction-requests/{requestId}`),
   then `listTransactions` / `getTransactionById` once on chain.

## Rules
- **`202` is not success.** Never treat the create response as settlement; wait for the on-chain
  transaction. Prefer webhooks over polling where the BVNK webhook catalog covers the event.
- Supply a **unique `reference`** per transfer. It is your idempotency handle and your reconciliation key.
- A transfer can be **held by screening**. If it is, it appears via `listScreenings`; see the
  screening skill. Do not resubmit a held transfer — that creates a duplicate.
- `503` is declared on every Layer1 operation; treat it as retryable with backoff. `400` is not.
