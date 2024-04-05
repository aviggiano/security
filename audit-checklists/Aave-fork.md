# Aave Fork Checklist

## Checklist

### General

- [x] Common issues from Aave forks and how to mitigate them
  - [x] [List of Aave v3 issues reported on their bug bounty program](https://governance.aave.com/t/bgd-bug-bounties-proposal/13077)
    - [x] 1. Flash loan premium not passed correctly to the receiver
      - [x] Mitigation: Fork from Aave's latest commit (v3.0.2)
    - [x] 2. Misusage of e-mode oracle feed after an asset is removed from e-mode
      - [x] Mitigation: Do not enable e-mode
    - [x] 3. Griefing risk with LTV0 and isolated collateral assets
      - [x] Mitigation: Use pause instead of changing asset to LTV0. Re-evaluate this issue when more assets are necessary
    - [x] 4. Risk of price manipulation on GUNI USDC/UDST due to illiquidity
      - [x] Mitigation: Careful evaluate borrow or supply tokens added
    - [x] 5. Inconsistent amount on aToken transfer events
      - [x] Mitigation: Fork from Aave's latest commit (v3.0.1)
    - [x] 6. [Stable rate mode bug](https://governance.aave.com/t/aave-v2-v3-security-incident-04-11-2023/15335)
      - [x] Mitigation: Disable stable rate
  - [x] Forks
    - [x] [Hundred Finance (Compound Fork)](https://blog.hundred.finance/15-04-23-hundred-finance-hack-post-mortem-d895b618cf33)
      - [x] [Root cause](https://blog.hundred.finance/15-04-23-hundred-finance-hack-post-mortem-d895b618cf33): _This vulnerability has existed in the Compound v2 code since its launch despite multiple audits, presenting itself when markets are launched with a collateral value in place but no depositors or following markets becoming empty due to user withdrawal post-launch._
      - [x] Mitigation: _Minting Small cToken (or equivalent) Amounts at Market Creation_
    - [x] [Agave](https://medium.com/immunefi/a-poc-of-the-hundred-finance-heist-4121f23a098) 
      - [x] Root cause: _The root cause of both attacks is the same: post transfer hooks in non-standard ERC667 tokens, which enabled the reentrancy._
      - [x] Mitigation: Audit L2 bridged tokens
    - [x] [Radiant Capital](https://www.immunebytes.com/blog/list-of-crypto-hacks-in-the-month-of-january/)
      - [x] Root cause:
        - _It basically exploits a time window when a new market is activated in a lending market (forked from the popular Compound/Aave). The exploitation also relies on a known rounding issue in current Compound/Aave codebase.Specifically, today's actor sniped the new USDC market deployment and exploited it *6 seconds* after the activation._ [1](https://neptunemutual.com/blog/how-was-radiant-capital-exploited/), [2](https://github.com/SunWeb3Sec/DeFiHackLabs?tab=readme-ov-file#20240102-radiantcapital---loss-of-precision), [3](https://twitter.com/peckshield/status/1742334242120466580), [4](https://phalcon.blocksec.com/explorer/security-incidents)
        - _The attacker was the first individual to supply in this new USDC market, and he then used that opportunity to manipulate the liquidityIndex, a key factor in determining the AToken user balances, to borrow all the ETH_
        - [x] Mitigation: To mitigate this, Aave has a mandatory policy to deposit alongside any new listing. [1](https://twitter.com/lemiscate/status/1742343842211520646), [2](https://twitter.com/agfviggiano/status/1768589303884517714)
    - [x] [Blizz Finance](https://github.com/YAcademy-Residents/defi-fork-bugs?tab=readme-ov-file#aave)
      - [x] Root cause: _Chainlink LUNA oracle became inacurate during the Terra collapse_
      - [x] Mitigation: Choose strong borrow and collateral assets
- [x] Others
  - [x] Replay attacks around the reuse of digital signatures