# Docs omit `symbol`/`name` from `listPerpMarkets()` rows while every other market-listing surface keys on symbol — `markets.fetchOrderBook(...)` fails with the pool row alone

**Severity:** Minor
**Area:** `@somnia-chain/markets-sdk@0.32.1` — `client.listPerpMarkets()` vs docs "Connect" example
**Reproducible:** Yes

## Summary

The developer docs "Connect" example ends with:

```ts
const perps = await markets.client.listPerpMarkets()
const btc = perps.find((m) => m.baseSymbol === "BTC")!
```

but the returned rows contain no `symbol` and no `name` field, and the SDK's CCXT-style reads (`fetchOrderBook`, `fetchTicker`) key strictly on symbol and throw if the market wasn't registered via `loadMarkets()`. A developer following the docs example then cannot feed the found `btc` row into anything — `markets.fetchOrderBook(btc.symbol, …)` throws both because `btc.symbol` is `undefined` and because perp symbols were never loaded (`unknown symbol BTC-PERP — call loadMarkets() first`). The docs never mention that perp order books require `loadMarkets()` and the unified symbol format `BTC/USDso:USDso`.

## Repro

```ts
const perps = await markets.client.listPerpMarkets();
const btc = perps.find(m => m.baseSymbol === "BTC");
console.log(btc.symbol);                 // undefined
await markets.fetchOrderBook(btc.symbol, 3);
// ERR: unknown symbol BTC-PERP — call loadMarkets() first   (or TypeError reading 'indexOf')
// works only after:  await markets.loadMarkets();
//                     markets.fetchOrderBook("BTC/USDso:USDso", 3)
```

Verified output (31 Oct 2026): `listPerpMarkets()` rows have 33 keys including `baseSymbol`, `poolAddress`, `markPrice`, `fundingRate` — but **no** `symbol`/`name`; `fetchOrderBook(undefined)` → `TypeError: Cannot read properties of undefined (reading 'indexOf')`; guessing `"BTC-PERP"` → `unknown symbol BTC-PERP — call loadMarkets() first`. The correct unified symbol `BTC/USDso:USDso` appears nowhere in the perps dev docs.

## Expected

Docs example should either use a field that exists on the row, or state that order-book/CCXT reads require `loadMarkets()` and the `BASE/QUOTE:QUOTE` symbol form.

## Actual

Docs example dead-ends; error messages do not suggest the fix (they reference a `BTC-PERP`-style symbol form that also does not exist anywhere).

## Impact

Low: all read paths still work once you know the trick; every write path (`placePerpOrder` with `poolAddress`) is unaffected.
