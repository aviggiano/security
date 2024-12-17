# Oracle

This table shows the configuration of the Top DeFi Lending protocols (TVL > $100m) of EVM-compatible chains for non-correlated assets

| Name             | TVL ($ million) | Primary Oracle                                                                 | Secondary Oracle                        | Note                                |
|------------------|-----------------|-------------------------------------------------------------------------------|-----------------------------------------|-------------------------------------|
| Aave             | 23192           | [Chainlink latestAnswer, no checks](https://basescan.org/address/0x978D8878b53Fbe40dab7D4AB47b97AB622FFeF9f#readContract)                                                                  | None                                    |                                     |
| JustLend         | 6900            | ?                                                                             | ?                                       | TRON                                |
| Spark            | 5180            | [Chainlink latestAnswer, non-negative](https://etherscan.io/address/0x8105f69D9C41644c6A0803fDA7D03Aa70996cFD9)            | None                                    | Aave v3 fork                        |
| Morpho           | 3231            | [Chainlink latestRoundData, non-negative](https://etherscan.io/address/0x3A7bB36Ee3f3eE32A60e9f2b33c1e5f2E83ad766#code)                           | None                                    |                                     |
| Compound         | 3140            | [Chainlink latestRoundData, non-negative](https://etherscan.io/address/0x528c57A87706C31765001779168b42f24c694E1b#code)                                                 | None                                    |                                     |
| Kamino Lend      | 2016            | ?                                                                             | ?                                       | Solana                              |
| Venus            | 1967            | [Chainlink latestRoundData, non-negative, staleness](https://bscscan.com/address/0x8455EFA4D7Ff63b8BFD96AdD889483Ea7d39B70a#readProxyContract)                                 | None                                    | Compound v2 fork                    |
| Avalon Labs      | 1176            | ?                                                                             | ?                                       | [Not open source](https://github.com/avalonfinancexyz/USDa-audit-blocksec/blob/main/blocksec_avalon_v1.3-signed.pdf)                     |
| Fluid Lending    | 733             | [Uniswap v3 TWAP, single pool](https://etherscan.io/address/0x0BaD69C6DD12f3bd53cDCAd18D3526cDD5ed3141#readContract) <br/> [Chainlink latestRoundData, non-negative, staleness, sequencer](https://basescan.org/address/0x052D8cC70aC6B85BCf7aF0047ad4EDc8d546B8D0#readContract) | [Chainlink latestRoundData](https://etherscan.io/address/0x0BaD69C6DD12f3bd53cDCAd18D3526cDD5ed3141#readContract)             | `uniV3secondsAgos_ 240,60,15,1,0`     |
| Suilend          | 562             | ?                                                                             | ?                                       | Sui                                 |
| Benqi Lending    | 514             | [Owner-set price](https://snowtrace.io/address/0x316aE55EC59e0bEb2121C0e41d4BDef8bF66b32B/contract/43114/code)                                                              | [Chainlink latestAnswer, no checks](https://snowtrace.io/address/0x316aE55EC59e0bEb2121C0e41d4BDef8bF66b32B/contract/43114/code)                | Compound v2 fork                    |
| Save             | 430             | ?                                                                             | ?                                       | Solana                              |
| Aries Markets    | 410             | ?                                                                             | ?                                       | Aptos                               |
| marginfi Lending | 403             | ?                                                                             | ?                                       | Solana                              |
| NAVI Lending     | 398             | ?                                                                             | ?                                       | Sui                                 |
| Echo Lending     | 269             | ?                                                                             | ?                                       | Aptos                               |
| ZeroLend         | 266             | [Chainlink latestAnswer, non-negative](https://lineascan.build/address/0xFF679e5B4178A2f74A56f0e2c0e1FA1C80579385#code)                                                    | None                                    | Aave v3 fork                        |
| Colend Protocol  | 259             | [Pyth, no checks](https://scan.coredao.org/address/0xBc3c48E10e6EeCa877E82d17baA0cA6AE5D0a153#code)                                                            | None                                    | Aave v3 fork <br/> Core blockchain      |
| Moonwell         | 228             | [Owner-set price](https://basescan.org/address/0xEC942bE8A8114bFD0396A5052c36027f2cA6a9d0#code)                                                              | [Chainlink latestRoundData, non-negative, positive-round](https://basescan.org/address/0xEC942bE8A8114bFD0396A5052c36027f2cA6a9d0#code) | Compound v2 fork                    |
| Burrow           | 213             | ?                                                                             | ?                                       | Near                                |
| Scallop Lend     | 193             | ?                                                                             | ?                                       | Sui                                 |
| Tectonic         | 150             | ?                                                                             | ?                                       | Cronos                              |
| Liqwid           | 146             | ?                                                                             | ?                                       | Cardano                             |
| EOS REX          | 143             | ?                                                                             | ?                                       | EOS                                 |
| Yei Finance      | 140             | ?                                                                             | ?                                       | Sei                                 |
| Fraxlend         | 135             | [Chainlink latestRoundData, non-negative, staleness](https://etherscan.io/address/0x350a9841956D8B0212EAdF5E14a449CA85FAE1C0#code) <br/> [Uniswap V3 TWAP, single pool](https://etherscan.io/address/0x350a9841956D8B0212EAdF5E14a449CA85FAE1C0#code)          | Both                                    | `TWAP_DURATION 900`                   |
| Nostra           | 127             | ?                                                                             | ?                                       | Starknet                            |
| Echelon Market   | 123             | ?                                                                             | ?                                       | Aptos                               |
| INIT Capital     | 115             | [API3](https://explorer.mantle.xyz/address/0x7928419135cE5427858F0F5c0cbA3151b9b14f81?tab=contract)                                                                         | None                                    | Mantle                              |
| TakoTako         | 105             | ?                                                                             | ?                                       | Taiko                               |
| Folks Finance    | 103             | [Chainlink latestRoundData, non-negative, TWAP/spot](https://snowtrace.io/address/0x802063A23E78D0f5D158feaAc605028Ee490b03b/contract/43114/readContract?chainid=43114) <br/> [Pyth](https://snowtrace.io/address/0x802063A23E78D0f5D158feaAc605028Ee490b03b/contract/43114/code?chainid=43114)                      | None                                    |                                     |

## Disclaimers

- Updated on December 17, 2024
- Data from [DefiLlama](https://defillama.com/protocols/Lending)
- This information is provided for informational purposes only and may contain inaccuracies or errors. For definitive and up-to-date details, please verify directly on-chain or with the respective protocol's documentation
- Many protocols use `getPooledEthByShares` for the stETH/ETH conversion rate
- Protocols may incorporate multiple oracle implementations across their deployments
- Oracle implementations can vary by network, and fallback oracles, while supported, may not always be actively configured
- Several protocols operate on multiple networks, and their oracle behavior may differ depending on the specific deployment
- This analysis is for research purposes only and does not constitute a security recommendation, endorsement, or guarantee. Users and researchers are encouraged to conduct their own due diligence
