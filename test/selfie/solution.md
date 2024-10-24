# Selfie

This exploit leverages a vulnerability in the **Selfie** challenge, where an attacker can temporarily gain governance power through a flash loan and drain the pool using the `emergencyExit` function.

## Vulnerability Overview

The `emergencyExit` function transfers all funds from the pool to a recovery address, but it can only be called by governance. To queue this action, the sender must hold at least 50% of the governance tokens. However, the attacker can use a **flash loan** to temporarily hold the required tokens, queue the action, and then repay the loan.

**Key Issue**: The contract does not have a `hasEnoughVotes` check during the execution of governance actions (`executeAction`). This allows the attacker to queue the `emergencyExit` with flash loaned tokens, and it will be automatically executed after the delay, regardless of the voter's current token holdings.

### Exploit Steps

1. **Flash Loan Governance Tokens**: The attacker borrows a large number of governance tokens through a flash loan to temporarily hold more than 50% of the voting power.
2. **Queue `emergencyExit`**: While holding the borrowed tokens, the attacker queues the `emergencyExit` function using the `simpleGovernance` contract. This action will transfer all the pool’s funds to a recovery address.
3. **Repay the Flash Loan**: After successfully queuing the action, the attacker repays the flash loan, returning the governance tokens to the lender.
4. **Wait for Automatic Execution**: After the required delay (2 days), the queued `emergencyExit` action is automatically executed, transferring all the funds to the attacker’s recovery address.

### Key Exploit Factor

- The absence of a **`hasEnoughVotes` check during `executeAction`** allows the attacker to queue a malicious proposal using temporarily borrowed tokens and have it executed later, even after the attacker no longer holds the governance tokens.

## Prevention Strategy

To prevent this exploit:
- **Add a `hasEnoughVotes` check during `executeAction`**: The contract should check whether the proposer still holds at least 50% of the governance tokens at the time of execution, not just when the action is queued. This would block flash loan attacks by requiring continuous voting power.