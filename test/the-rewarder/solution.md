# The Rewarder

This exploit targets a bug in the **Rewarder** contract, where only the last token is marked as claimed when claiming rewards for multiple tokens. This allows the attacker to repeatedly claim rewards for the unmarked tokens (those before the last one).

## Vulnerability Overview

The `claimRewards` function processes multiple tokens but only flags the last token as claimed. As a result, earlier tokens in the batch remain unclaimed and can be used to repeatedly collect rewards.

### Exploit Steps

1. **Claim multiple rewards**: The attacker claims rewards for several tokens at once.
2. **Unmarked tokens**: Only the last token is flagged as claimed, leaving the rest unmarked.
3. **Re-claim rewards**: The attacker claims rewards again for the unmarked tokens.
4. **Repeat**: This process can be repeated to drain the reward pool.

## Defense Strategy

To fix this, ensure that all tokens in the claim batch are marked as claimed, not just the last one. Proper validation should be added to prevent multiple claims on the same tokens.

