# DVT Challenge: Puppet Solution

## Challenge Overview
The **Puppet** challenge involves exploiting the collateral calculation mechanism of a lending platform where users can borrow either ETH or a specific token by providing the other asset as collateral. The collateral requirement is based on the pool’s ETH-to-Token balance ratio. The goal is to manipulate this ratio, allowing the tokens to be borrowed with minimal collateral, and then transfer the borrowed tokens to a designated recovery address.

## Solution Strategy

1. **Understand Collateral Calculation**  
   The Puppet lending platform requires collateral based on the ratio between ETH and Token balances in its pool. By affecting this ratio, the collateral requirement can be minimized, enabling a favorable borrowing situation.

2. **Manipulate the Price Ratio Using Uniswap**  
   The ratio is indirectly set by the **UniswapExchange** contract, where large enough swaps can impact the ETH-to-Token price ratio:
   - **Swap Execution**: Conduct a swap that significantly shifts the balance in Uniswap, creating a drastic change in the ETH-to-Token ratio. This will lower the collateral requirement in the Puppet pool.
   - **Imbalance Creation**: The swap should be large enough to ensure that the pool's balance ratio is heavily skewed, allowing minimal collateral to borrow tokens.

3. **Borrow Tokens with Minimal Collateral**  
   With the manipulated price ratio:
   - **Collateral Reduction**: Borrow the desired tokens while putting down an insignificant amount of collateral.
   - **Token Transfer**: Transfer the borrowed tokens to a specified recovery address.

## Important Notes
- **Risk of Detection**: Large swaps can be detected on-chain, so careful timing may be needed.
- **Uniswap Liquidity Effect**: Understand the impact on Uniswap liquidity, as drastic price movements could attract attention from other market participants.

## Security Lessons Learned
1. **Price Oracle Hardening**: Avoid relying on easily manipulated price sources, such as single-DEX price feeds, for sensitive calculations.
2. **Collateralization Ratios**: Use robust and resistant calculation methods to maintain the intended level of risk mitigation.
3. **On-Chain Monitoring**: Implement tracking and alerting for significant swaps, which may indicate price manipulation attempts.

This attack demonstrates the critical need for resilient price oracle management and accurate collateral mechanisms in DeFi platforms to prevent price manipulation vulnerabilities.
