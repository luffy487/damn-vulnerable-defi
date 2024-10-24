# TrusterLenderPool Exploit Solution

## Overview
This writeup details the vulnerability in the `TrusterLenderPool` contract, which allows for the complete draining of the pool’s token balance. The exploit is possible due to the unrestricted ability to execute arbitrary function calls on external contracts during a flash loan.

## Vulnerability Breakdown
The key vulnerability lies in the `flashLoan` function:
- It accepts arbitrary calldata and executes it via `target.functionCall(data)`.
- This allows the attacker to call the `approve()` function on the token contract, granting themselves permission to spend tokens on behalf of the pool.

### Steps to Exploit
1. **Flash Loan Execution**: Request a flash loan of 0 tokens. This does not require any funds but allows executing arbitrary calldata.
   
2. **Approve Attacker**: Within the calldata, encode a call to the token’s `approve()` function, allowing the attacker to approve themselves for the total balance of tokens held by the pool.

3. **Token Transfer**: After obtaining approval, transfer all of the pool’s tokens to the attacker's address using `transferFrom()`.

## Attack Strategy
- Flash loan any amount (0 tokens).
- Pass calldata that encodes the `approve()` function, approving the attacker.
- Execute `transferFrom()` to drain the pool.

## Mitigation
To prevent this vulnerability:
- **Restrict External Calls**: Limit what functions can be called during flash loans.
- **Access Control**: Implement proper access controls around approvals and other critical token operations.
- **Calldata Validation**: Ensure that only safe function calls are allowed through calldata.

## Conclusion
The exploit leverages the fact that the pool allows arbitrary function calls during flash loans. By exploiting the `approve()` function, the attacker can take control of the pool’s token balance and transfer it to their own address, effectively draining the entire pool.
