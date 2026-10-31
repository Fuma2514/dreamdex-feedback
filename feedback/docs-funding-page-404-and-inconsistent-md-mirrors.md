# Docs docs-index/README fetch paths are inconsistent: `/docs/trading/funding` renders 404 in-app while the linked sibling pages resolve, and raw `.md` mirrors only exist for some pages

**Severity:** Minor
**Area:** Documentation site (`app.testnet.dreamdex.io/docs/...`)
**Reproducible:** Yes

## Summary

The perps docs link to a "Funding" page at `https://app.testnet.dreamdex.io/docs/trading/funding` (relative link `funding.md` from `docs/trading/perps-testnet/`), but that URL renders the app's "Page not found" screen. The actual page lives at `https://app.testnet.dreamdex.io/docs/trading/perps-testnet/funding`. Similarly, raw-markdown mirrors (`README.md` suffix) that work for some pages return the SPA HTML shell for others.

## Repro

1. Open `https://app.testnet.dreamdex.io/docs/trading/perps-testnet` — its "Where to go next" links include `[Funding](funding.md)`.
2. Resolve that relative link: `https://app.testnet.dreamdex.io/docs/trading/funding` → **"Page not found — The page you are looking for does not exist."** (verified in a real browser, 31 Oct 2026).
3. The working page is at `https://app.testnet.dreamdex.io/docs/trading/perps-testnet/funding`.
4. Raw mirror check:
   - `https://app.testnet.dreamdex.io/docs/developers/perps-testnet/README.md` → 200, raw Markdown (9,018 bytes) ✓
   - `https://app.testnet.dreamdex.io/docs/trading/funding.md` and `.../funding/README.md` → 200 HTTP but return the Next.js HTML shell (DOCTYPE), not Markdown ✗

## Expected

Relative doc links resolve, and the raw `.md` mirroring behaves consistently (it is a documented convenience — the developer page itself is served as `README.md`).

## Actual

Broken relative route for the Funding page and inconsistent `.md` mirroring across the docs tree.

## Impact

Low: integrators hitting the dead URL lose the funding-mechanics page (the only place the 8-hour window / hourly-settlement formula is documented); automated doc scrapers mirroring the tree silently get HTML instead of Markdown for several pages.
