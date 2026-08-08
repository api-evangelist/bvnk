---
name: Review and action a held Layer1 transfer
description: List compliance screenings and manually release or reject a transfer that has been held.
api: openapi/bvnk-layer1-digital-asset-openapi-original.yml
operations: [listScreenings, manuallyActionHeldTransfer]
---

# Review and action a held Layer1 transfer

## Steps
1. **Find the hold** — `listScreenings` (`GET /digital/v1/screenings`).
2. **Action it** — `manuallyActionHeldTransfer` (`PUT /digital/v1/screenings/action`).

## Rules
- **This is a human decision.** Releasing a screened transfer is a compliance action with financial
  and regulatory consequence. An agent may gather and present the screening record; it must not
  auto-release. Treat this operation as human-in-the-loop.
- `410 Gone` on the action means the screening is no longer actionable — someone else already
  resolved it, or it expired. Re-read `listScreenings` rather than retrying.
- Five query parameters on `listScreenings` (`hash`, `operation`, `state`, `startDate`, `endDate`)
  are marked **deprecated** in the published spec. Prefer the non-deprecated filters.
- Scopes: `screenings:view`, `screenings:edit`; `screenings:override` and `screenings:remediate`
  exist in the realm for stronger actions.
- BVNK also emits a **compliance information request** webhook for Travel Rule holds on the payments
  side — see `asyncapi/bvnk-webhooks.yml`.
