# SDK constant `SOMNIA_HIDEKI_TESTNET_ADDRESSES.collateral` / `.testUsdc` points to a different token than the actual perps collateral (USDso)

**Severity:** Minor
**Area:** `@somnia-chain/markets-sdk@0.32.1` — exported addresses constant
**Reproducible:** Yes (read-only)

## Summary

The SDK's exported Hideki address book ships `collateral: 0x9E9492cC533E8696A3b6afC7df730800DF75148f` (an ERC-20 whose `name()` is `"Test USDC"`, `symbol()` `"TEUSDC"`), while the real collateral for every perp market — the USDso at `0xed7993A74dAe1e6656B704A424DcD8711aAf4F72` (`name()` = `"USDso"`, `symbol()` = `"USDso"`, 18 decimals) — is only reachable via the market row's `quoteToken` or `getPerpSystemConfig()`. The dev docs contain a one-line warning ("The perps collateral is not `SOMNIA_HIDEKI_TESTNET_ADDRESSES.collateral`"), but the constant itself is wrong/misleading: any integrator who follows the standard SDK pattern of reading `addresses.collateral` for approvals gets a token that has nothing to do with perps margin.

## Repro

1. On-chain (HTTP RPC `https://api.hideki2.infra.testnet.somnia.network`, chainId 50383):

```
eth_call  to 0x9E9492cC533E8696A3b6afC7df730800DF75148f  data 0x06fdde03 (name())
→ "Test USDC"   (symbol(): "TEUSDC")

eth_call  to 0xed7993A74dAe1e6656B704A424DcD8711aAf4F72  data 0x06fdde03
→ "USDso"       (symbol(): "USDso", decimals(): 18)
```

2. SDK:

```ts
import { SOMNIA_HIDEKI_TESTNET_ADDRESSES as A, SomniaMarkets } from "@somnia-chain/markets-sdk";
import { hidekiTestnet } from "@somnia-chain/markets-sdk/chains";
const m = new SomniaMarkets({ chain: hidekiTestnet, indexerUrl: "https://hideki-dev.smk.somnia.host/v1/graphql", addresses: A });
console.log(A.collateral); // 0x9E9492cC533E8696A3b6afC7df730800DF75148f  ("Test USDC")
const perps = await m.client.listPerpMarkets();
console.log(perps[0].quoteToken); // 0xed7993a74dae1e6656b704a424dcd8711aaf4f72 ("USDso")
const cfg = await m.client.getPerpSystemConfig(perps[0].marginBank);
console.log(cfg.collateralToken); // 0xed7993A74dAe1e6656B704A424DcD8711aAf4F72
```

Both `quoteToken` on every perp market row and `getPerpSystemConfig().collateralToken` return `0xed7993…`, never `A.collateral`.

## Expected

`SOMNIA_HIDEKI_TESTNET_ADDRESSES.collateral` should be the token that perp pools and the MarginBank actually pull (`0xed7993…` USDso), or the SDK should not name the unrelated token `collateral`/`testUsdc`.

## Actual

`collateral` and `testUsdc` both alias `0x9E9492…` ("Test USDC"), an unrelated token. The developer docs patch over this with a "common pitfalls" bullet instead of the constant being corrected.

## Impact

Anything that approves/pulls `addresses.collateral` for perps funding (the most natural read of the constant) approves the wrong token; deposits then fail at the ERC-20 pull stage with a generic transfer error. Severity minor because the docs warn about it and the market row exposes the correct address.
