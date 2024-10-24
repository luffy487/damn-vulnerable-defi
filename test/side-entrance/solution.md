# SideEntrance Challenge Solution

## Overview
The `SideEntrance` contract allows users to take flash loans without enforcing the standard repayment mechanism. This vulnerability can be exploited to drain the contract's funds by manipulating the loan process.

## Vulnerability Breakdown
The `flashLoan` function does not require immediate repayment of the loan amount. Instead, it checks the balance after the execution of the loan. This creates an opportunity for an attacker to take a flash loan equal to the pool’s total balance and deposit the tokens back into the contract within the callback.

### Attack Strategy
1. **Initiate Flash Loan**: Borrow the entire balance of the pool through the `flashLoan` function.
2. **Callback Execution**: In the callback function, deposit the same amount back into the pool.
3. **Withdraw Funds**: After the deposit, call the `withdraw` function to drain the contract, since the balance check occurs after the callback execution.

## Mitigation
To prevent such vulnerabilities:
- **Immediate Repayment**: Ensure that flash loans require immediate repayment before executing any arbitrary logic.
- **Strict Balance Checks**: Implement checks that ensure the loan is repaid within the function, rather than relying on a post-execution balance check.

## Conclusion
The `SideEntrance` contract's design flaw allows an attacker to exploit the absence of immediate repayment checks in flash loans. By taking the entire balance, depositing it back, and withdrawing it, the attacker can drain the contract's funds. Proper checks and balances are essential to secure such functionalities in smart contracts.
