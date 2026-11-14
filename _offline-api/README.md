# `_offline-api/` — sovereign-mirror build-time fallback

When the live operator + every cached entry in `localStorage` are
**both** unavailable, the wrapped `fetch` in `js/offline-cache.js`
(rule SW Phase 3) falls through to JSON files in this directory.
This is the Tier-1 trust path: bytes shipped inside the
Electron binary, signed by the wallet's code-signing certificate.

Naming convention — direct mirror of the URL pathname:

```
GET /api/pools             →  _offline-api/api/pools.json
GET /api/tokens/verified   →  _offline-api/api/tokens/verified.json
GET /v1/balances?addrs=…   →  _offline-api/v1/balances.json     (params dropped)
```

Each file is either:

1. A **raw body** (preferred for clarity) — what a 200 response
   would return; or
2. An **envelope**: `{ "status": <number>, "body": <any> }` — for
   non-200 fallbacks.

Files are regenerated at release time by
`scripts/sync-sovereign-sites.sh`, which fetches each enumerated
endpoint from the operator and writes the body verbatim. **Never
hand-edit live data into these files** — keep them empty / minimal
so the user understands they're seeing baseline fallback data,
not a stale snapshot of someone else's portfolio.

The matching `_meta.json` in the parent directory tracks the
`bundleSize` + `rootHash` of the entire `ionswap-monerousd-org/`
tree — chain-anchored under `SITE_PUBLISH` (op `0x27`).
