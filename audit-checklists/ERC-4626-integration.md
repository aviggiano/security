# ERC-4626 Integration Checklist

This checklist is a compilation of the following references prepared during a [Cantina security](https://github.com/SizeCredit/size-solidity/blob/main/audits/2025-06-14-Cantina.pdf) review for the [Size Credit](https://www.size.credit/) protocol:
- https://gist.github.com/dmitriia/ba29f19b5247b348603dbd8460654d88 by https://cantina.xyz/u/dmitriia
- https://gist.github.com/aviggiano/cf172a4d6b92233a18e6e4251abffa28 by https://cantina.xyz/u/elhaj

## ✅ Technical Compatibility

- [ ] Vault share minting must be deterministic, based only on deposit amounts  
  _Avoid share calculations based on external exchange rates (e.g., LP tokens from DEX pools), which are subject to manipulation._

- [ ] No position NFTs  
  _Vaults that mint ERC-721 position NFTs need to support `onERC721Received` callbacks._

- [ ] No non-ERC4626-compliant withdrawal fee logic  
  _Vaults should deduct fees by increasing the number of shares required, not by reducing the underlying returned, to avoid breaking `transferFrom` operations._

- [ ] No non-immediate withdrawals  
  _Avoid vaults that require pre-requesting, use withdrawal queues, or delay withdrawal processing, which break core market actions._

## ✅ Operational Limits

- [ ] No restrictive deposit/withdrawal limits  
  _Min/max constraints can break debt repayments, liquidations, and vault migrations._

## ✅ Liquidity & Risk Profile

- [ ] High and consistent TVL relative to the corresponding market  
  _Prefer vaults with TVL at least ~700% of the market to avoid liquidity issues when utilization reaches critical levels (85–90%)._

- [ ] Prefer vanilla institutional-grade vaults with low risk, low return profiles  
  _Exotic or high-risk vaults are discouraged due to return and liquidity volatility, which may disrupt operations._

- [ ] Fees, slippages, and withdrawal limits must be minimal or nonexistent  
  _Avoid vaults that reduce predictability and reliability for users (e.g., causing transaction reverts or unavailable balances)._

## ✅ Governance & Collateral Consistency

- [ ] Prefer vaults curated by reputable managers with high total TVL  
  _Curators with more TVL at stake are less likely to change parameters in harmful ways._

- [ ] Underlying vault collateral should match or be compatible with the corresponding market  
  _e.g., a `CBBTC` market should prefer vaults with similar collateral types like `WBTC`, `tBTC`, etc._

