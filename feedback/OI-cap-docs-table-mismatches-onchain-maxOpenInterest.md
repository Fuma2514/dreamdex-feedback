# OI-cap table in "Perps on Testnet" docs contradicts on-chain `maxOpenInterest` (2x–4x off) and live OI already exceeds the documented cap on 3 markets

**Severity:** Major
**Area:** Docs / API data consistency (`https://app.testnet.dreamdex.io/docs/trading/perps-testnet`)
**Reproducible:** Yes (read-only, no account needed)

## Summary

The "Open interest cap" column of the markets table in *Perps on Testnet* does not match the on-chain per-pool `maxOpenInterest` value returned by the SDK's `client.getPerpRiskParams(poolAddress)`. The docs values are exactly 1/2 of on-chain for the 20x/10x-tie markets (BTC, ETH, HYPE, SOL, TAO) and exactly 1/4 for every 4x-ratio market (XRP, ADA, NEAR, ZEC, AVAX, BNB, LINK, SUI, XLM, DOGE, WLD, ZRO, 1000PEPE, LIT, PUMP, VVV). Additionally, live open interest already exceeds the documented cap on three markets, which is impossible if the docs value were the real cap.

## Environment

- SDK: `@somnia-chain/markets-sdk@0.32.1` (the version the app runs, per docs)
- Chain: Hideki testnet, chainId 50383, RPC `https://api.hideki2.infra.testnet.somnia.network`
- Indexer: `https://hideki-dev.smk.somnia.host/v1/graphql`

## Repro

```ts
import { SOMNIA_HIDEKI_TESTNET_ADDRESSES as A, SomniaMarkets } from "@somnia-chain/markets-sdk";
import { hidekiTestnet } from "@somnia-chain/markets-sdk/chains";
const markets = new SomniaMarkets({ chain: hidekiTestnet, indexerUrl: "https://hideki-dev.smk.somnia.host/v1/graphql", addresses: A });
const perps = await markets.client.listPerpMarkets();
const btc = perps.find(p => p.baseSymbol === "BTC");
const rp = await markets.client.getPerpRiskParams(btc.poolAddress);
console.log(rp.maxOpenInterest);   // 49363090000  => 493.6309 BTC (btc.baseDecimals = 8)
console.log(rp.maxPositionSize);   // 10000000000  => 100 BTC
```

Docs table says BTC open interest cap = **246.81545 BTC** and "a single account is capped at a tenth of it" = **24.68 BTC**.

Measured on 31 Oct 2026, block ~37,741,700:

| Market | Docs OI cap | On-chain `maxOpenInterest` | Ratio (onchain/docs) | Docs "tenth" claim | On-chain `maxPositionSize` | Live OI (indexer) |
|---|---|---|---|---|---|---|
| BTC | 246.81545 | 493.6309 | 2.00 | 24.68 | 100.0 | **333.08 (exceeds docs cap)** |
| ETH | 3,995.6125 | 7,991.225 | 2.00 | 399.56 | 399.56 (OK) | 952.62 |
| SOL | 47,654.44 | 95,308.88 | 2.00 | 4,765.44 | 4,765.44 (OK) | 19,772.5 |
| TAO | 2,185.491 | 2,185.491 | 1.00 | 218.55 | 218.55 (OK) | 285.61 |
| XRP | 344,632 | 1,378,528 | 4.00 | 34,463 | 68,926.4 | **928,153 (exceeds docs cap)** |
| NEAR | 250,410 | 1,001,640 | 4.00 | 25,041 | 50,000 | **338,594 (exceeds docs cap)** |
| ZEC | 420 | 1,680 | 4.00 | 42 | **420 (= full docs cap, not a tenth)** | 136.08 |
| all of AVAX/BNB/LINK/SUI/XLM/DOGE/WLD/ZRO/1000PEPE/LIT/PUMP/VVV | — | — | 4.00 | — | 0.10–0.20× docs cap | within |

Full raw outputs captured on 2026-10-31; every ratio is exactly 2.00 or 4.00 (not a rounding artifact).

## Expected

Docs table OI cap matches on-chain `maxOpenInterest`, and `maxPositionSize` ≈ cap/10 as the docs footnote claims ("a single account is capped at a tenth of it on most markets").

## Actual

- Docs cap = 1/2 or 1/4 of the on-chain `maxOpenInterest` depending on market.
- Per-account `maxPositionSize` is cap/10 for some markets (ETH, HYPE, SOL, TAO, WLD, ZRO, 1000PEPE, LIT, PUMP, VVV), cap/5 for the 4x group (XRP, ADA, NEAR, AVAX, BNB, LINK, SUI, XLM, DOGE), but 100 BTC for BTC (= cap/4.9) and the *full* documented cap for ZEC — so the blanket "a tenth on most markets" claim is wrong for at least 10 of 21 markets.
- Live OI on BTC (333), XRP (928k) and NEAR (338k) is already above the documented cap, proving the docs column cannot be the enforced cap.

## Impact

Integrators sizing positions against the documented cap will wrongly reject trades (they think the market is full when it is not) or wrongly expect a per-account limit that is 2–10× off the real one, and `getMaxPerpOrderSize.limitedBy === "maxPositionSize"` will appear to trigger at values far from anything in the docs.

## Suggested fix

Regenerate the markets table from `getPerpRiskParams` per pool, and reword the per-account cap footnote (it is not uniformly a tenth).
