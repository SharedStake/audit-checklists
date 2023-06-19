# Liquid Staking Derivatives (LSD) Checklist
## General

- [ ] How is data inserted into the validators registry? How are `WithdrawCredentials` set for the validator in **DepositContract**? Is it possible that a deposit transaction is frontrunned by a malicious validator to set immutable `WithdrawCredentials`?
      Withdrawal credentials are pre-set and inserted into deposit calls with user ETH by the contract
      v2.0 credentials are deposited via the same gas minimizing audited trusted approach as v1.
      Trusted validator operator with 2+ yrs deposits via batch deposit calls to the ETH deposit vault
      Further enhancements in phase 3 put the node operators validator credentials on-chain at cost but allow a secondary delayed validation step to ensure withdrawal credentials are respected.  
- [ ] The derivatives must be collateralized by 100% ETH. However, the amount of ETH may decrease due to slashing. Does the implementation of the derivative token support burning? Is a depeg between the *Derivative* and *UnderlyingToken* possible?
      Todo - currently we have 2 years of battle tested experience running validators with 0 slashings
- [ ] TVL (Total Value Locked) of LSD protocols is the largest among all types of DeFi systems. Centralization of key functions leads to great risk. An ideal LSD protocol should be fully decentralized (e.g., support multisig).Who can add validator data to the registry? Who can mint or burn derivatives? Who can add roles for addresses? Is it a single address or a multisig?
      Multisig controls key operating parameters of Sharedstake protocol
      phase 1 features a simple rollout based on v1 with a single deposit minter
      phase 3 introduces diverse node operator deployments via both permissioned and permissionless routes
- [ ] How often is `DepositContract.deposit()` called? Who initiates the function call? If a significant amount of ETH has accumulated in the protocol contract balance and `DepositContract.deposit()` is called, how many iterations of the call can be executed? How much gas will these iterations require? At what amount of ETH will an out-of-gas issue occur on the contract?
      the deposit contract is called at MIN(1 week, 320 ETH in contract)
      a batchDeposit fn call is verified to pass offchain then broadcast onchain
      TODO: iteration gas costs
- [ ] Are there operations in contracts where an array with data from all validators is iterated? How much gas does each iteration require? Is an out-of-gas issue possible with a large number of array elements (i.e., a large number of validators)?
[Example](https://github.com/code-423n4/2022-09-frax/blob/55ea6b1ef3857a277e2f47d42029bc0f3d6f9173/src/OperatorRegistry.sol#L113-L118)
- [ ] Is there a possibility of exploiting an inflation (first deposit) attack? How are empty pools created?  When creating an empty pool, are underlying tokens deposited? Is it possible to manipulate the price of shares immediately after creating a pool? A good solution to avoid an inflationary attack is to create a user with a non-zero balance when creating a pool.
      share price and rewards are paid out via a xERC4626 contract - wsgETH - with a variable cycle length set by the DAO. As rewards can only begin accruing after validators become active. ~45 days atm. An appropriate cycle length relative to the activation queue ensures rewards are linearly distributed relative to the users time spent staking for rewards. This prevents inflation attacks. Read more in the user scenario here https://docs.sharedstake.finance/shareddeposit-v2/protocol-upgrades/user-scenarios
- [ ] Is slashing of the operator's balance possible in the protocol? What happens if the amount of the slashing penalty is greater than the operator's balance? Can the operator withdraw the balance of collateral tokens while continuing to validate blocks?
- [ ] Special attention should be paid to the functions of withdrawing staked ETH. Is a reentrancy attack possible during withdrawal?
      Withdrawal architecture has re-entrancy gaurds
- [ ] Is a reentrancy attack possible in the user's rewards distribution function?
- [ ] Does the accounting of user rewards work correctly? If the protocol calculates the user's reward based on their share of the pool, are there any issues with the calculation of the share?
- [ ] Who determines the price of the derivative token? If the price is determined by oracles, is it possible to manipulate the price via the sandwich attack when a price update transaction is sent?
