# Docs' recommended read `getPerpSystemConfig` returns insuranceFund that has code, but all seven system contracts are minimal ERC-1967 proxies — raw `eth_getCode`-based verification in the docs' spirit yields indistinguishable 354-char bytecode; docs also list the Explorer as `explorer.testnet.somnia.network/tokyo` while the `app.dreamdex.io` variant of the same page lists an internal `run.app` URL

**Severity:** Minor
**Area:** Developer docs `developers/perps-testnet` — Network/Explorer row
**Reproducible:** Yes

## Summary

Two related doc-consistency issues found while verifying every address/endpoint the perps developer page publishes:

1. **Explorer URL differs between copies of the same page.** `app.testnet.dreamdex.io/docs/developers/perps-testnet` renders `Explorer = https://explorer.testnet.somnia.network/tokyo`, while the same page served from `app.dreamdex.io/docs/developers/perps-testnet` shows `https://smk-dev-markets-explorer-hideki-euw4-uzavbo36ra-ez.a.run.app` (an internal-looking GCP URL). Only one can be intended.
2. **No published way to verify proxy implementations.** All seven listed system contracts (MarginBank `0x4787…1394`, LiquidationEngine, InsuranceFund, PerpPoolFactory, VoucherDistributor, OperatorPermissionsRegistry) return the identical 354-character minimal-proxy bytecode, and the docs give no implementation addresses or EIP-1967 guidance, so "the check worth running" (`getPerpSystemConfig`) can confirm the *proxy* addresses but a developer can never pin the implementation they are audited against.

## Repro

```
curl -s https://app.testnet.dreamdex.io/docs/developers/perps-testnet | grep -o 'explorer[^"]*' | head -1
# explorer.testnet.somnia.network/tokyo
curl -s https://app.dreamdex.io/docs/developers/perps-testnet | grep -o 'run.app[^"]*' | head -1
# smk-dev-markets-explorer-hideki-euw4-uzavbo36ra-ez.a.run.app

# per contract (all seven):
eth_getCode 0x478769E1177C0773c43A593DCCc4Ad15F15F1394 → result length 354 hex chars
eth_getCode 0xA66d3C24Ad4a389d863846905D8173e1017e9F4D → identical length/prefix (ERC-1967)
```

Both listed explorer hosts are live (HTTP 200) and the address page for MarginBank renders on the public one, so nothing is broken — the two published URLs for the same "Explorer" row just disagree.

## Expected

One canonical explorer URL per environment; published implementation addresses (or an `eip1967` implementation getter) for the system contracts.

## Actual

Two different URLs across mirrors; minimal proxies with no documented implementation lineage.

## Impact

Low. Cosmetic/ops-level, but it means address-level trust checks against these docs cannot be completed as implied.
