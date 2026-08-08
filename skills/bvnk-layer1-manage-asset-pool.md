---
name: Manage a Layer1 asset pool
description: Create and inspect asset pools, read balances and withdrawable amounts, manage key pairs, and export balances.
api: openapi/bvnk-layer1-digital-asset-openapi-original.yml
operations: [createAssetPool, listAssetPools, getAssetPool, getAssetPoolBalance, getAssetPoolMaxWithdrawableAmount, getAssetPoolSummary, createKeyPair, listKeyPairs, getKeyPair, createBalanceExport, getExports, getExport, getNetworks, listAddresses]
---

# Manage a Layer1 asset pool

## Steps
1. **See what networks exist** — `getNetworks` (`GET /digital/v1/networks`).
2. **Create key material** — `createKeyPair` (`POST /digital/v1/key-pairs`); read back with
   `listKeyPairs` / `getKeyPair`.
3. **Create the pool** — `createAssetPool` (`POST /digital/v1/asset-pools`).
4. **Inspect** — `listAssetPools`, `getAssetPool` (`GET /digital/v1/asset-pools/{assetPoolId}`),
   `getAssetPoolSummary`, and `listAddresses` for the addresses attached to it.
5. **Read money** — `getAssetPoolBalance` (`GET /digital/v1/asset-pools/{assetPoolId}/balances`) and
   `getAssetPoolMaxWithdrawableAmount` before proposing any transfer.
6. **Export** — `createBalanceExport` (`POST /digital/v1/exports`), then `getExports` / `getExport`.

## Rules
- **Check `getAssetPoolMaxWithdrawableAmount`, not the raw balance**, before sizing a withdrawal.
  Balance includes amounts that are not withdrawable.
- `deleteAssetPool` returns `412 Precondition Failed` when the pool is not in a deletable state —
  that is a state problem, not a request problem. Do not retry it unchanged.
- `createBalanceExport` returns `409` when an equivalent export is already running; poll `getExport`
  instead of re-submitting.
- Scopes: `asset-pools:view` / `asset-pools:edit`, `keypairs:view` / `keypairs:edit`,
  `exports:view` / `exports:edit`, `networks:view`, `addresses:view`.
