# Upgrade plan — book-freebie-data

Current state: **7/10** (pass 1: 5 -> 7; pass 2: 7, consumer-facing CLI fix). Offline contract, adapters and CLI are tested and linted in CI; live capture, source permission and consumers remain unverified by design.

## Backlog

- P0: Push this branch and confirm the new GitHub Actions `CI` workflow is green on both Python versions.
- P1: Strengthen drift markers for `google-maps-platform-api-credit`, `usps-free-shipping-supplies`, `nasa-open-images-digital-assets` — their `required_markers` are only fragments of the title, so a challenge or error page that repeats the title would pass. Pick body phrases from a captured page (needs a reviewed live capture; not done offline).
- P1: Replace the copy of `cli.py`/`capture.py` scaffolding shared by the sibling `book-*-data` repos with the shared runtime package once it is independently installable (README gate 3).
- P2: The non-generic items do not set `availability`; align with `schema-v2.json` when a freebie publishes an explicit window.
- P2: Add a live-capture smoke job only behind an explicit manual trigger and reviewed source permission (never on push).

## Done in this pass (pass 1)

- Added `.github/workflows/ci.yml`: `ruff check`, `pytest` and an offline CLI smoke (`contract`, `validate demo.json`, `demo --open-only`) on Python 3.10 and 3.13.
- Added `[project.optional-dependencies] dev` (pytest, pinned ruff), `[tool.ruff]` and `[tool.pytest.ini_options]` to `pyproject.toml`; fixed import-order lint.
- Added `tests/test_registry.py`: `sources.json` ↔ packaged `source*.json` consistency (product, domain, category in contract, https URLs, interval), source-ID selector rejects traversal/unknown IDs, every marker-driven adapter normalizes a synthetic page into a valid live record and rejects a challenge page, JSON adapters reject non-object payloads, CLI `validate` exit codes do not echo input.
- Removed dead GOV.UK and Chevening template branches from `capture.normalize`; unknown sources now fail closed (`unknown freebie source`).
- GitHub Contents and JetBrains Help Center adapters reject non-object JSON with `ValueError`.

## Done in this pass (pass 2)

- `contract --version {v1,v2}` (default v1, output unchanged) prints `schema-v2.json`, the
  contract every captured record uses; before, consumers of live data could only get v1.
  Unknown versions exit 2. Registry test, CI smoke and README updated (same change as the
  sibling scholarship/freebie/grant/benefit CLIs).
- Weak drift markers left unchanged: stronger phrases need a reviewed live capture.
