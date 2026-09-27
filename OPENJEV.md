# OpenJEV Support

This fork adds optional [OpenJEV](https://openjev.sh) support alongside the original
[TypeSafe](https://typesafe.ai) Jev integration. OpenJEV is a free community gateway to the
same Jev model. TypeSafe remains the default — anyone with a TypeSafe key sees zero behaviour
change.

## What was added

- **`src/jevpandas/client.py`** — Added `provider` parameter to `JevClient.__init__` and a
  `_resolve_provider()` helper that implements the selection rule (see below). OpenJEV defaults:
  endpoint `https://api.openjev.sh/v1`, model `openjev`, key env `OPENJEV_API_KEY`.
  Added 503 to the list of retryable HTTP statuses (already covered by the 5xx retry path;
  now documented explicitly).
- **`.env.example`** — Added optional `OPENJEV_API_KEY` and `JEV_PROVIDER` env vars.
- **`README.md`** — Added an OpenJEV support note after the intro and an "OpenJEV (optional)"
  configuration section. TypeSafe is credited first throughout.

## Provider selection rule

1. **Explicit choice wins** — `provider="openjev"` argument or `JEV_PROVIDER=openjev` env var.
2. **TypeSafe if its key is set** — `TYPESAFE_API_KEY` set (and not `dummy`) → TypeSafe (default unchanged).
3. **OpenJEV if only its key is set** — `OPENJEV_API_KEY` set, no TypeSafe key → OpenJEV.
4. **TypeSafe default** — no keys set → TypeSafe defaults (unchanged).

## How to configure

```bash
# Option A: auto-detect (only OPENJEV_API_KEY set, no TYPESAFE_API_KEY)
export OPENJEV_API_KEY=your-openjev-key

# Option B: explicit
export JEV_PROVIDER=openjev
export OPENJEV_API_KEY=your-openjev-key
```

Or in code:

```python
from jevpandas import JevClient
client = JevClient(provider="openjev")
```

## How it was verified

- A live POST request was sent to `https://api.openjev.sh/v1/systemone` with model `openjev`,
  state `ping`, and one `noul` question — HTTP 200 returned.
- Re-grep confirmed no hardcoded `api.typesafe.ai` default was introduced; the TypeSafe default
  remains `https://api.typesafe.ai/v1` / `jev-latest` when TypeSafe is selected.

## Upstream

Original project: https://github.com/yalindogusahin/jev-pandas by @yalindogusahin
