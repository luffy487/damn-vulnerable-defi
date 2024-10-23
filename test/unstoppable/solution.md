# Unstoppable

## Overview

This project dives into a potential **Denial of Service (DoS)** vulnerability found in a flash loan contract that uses the **ERC-4626** token standard. The issue arises from how the contract relies on matching token balances and share allocations. We found that it’s possible to disrupt this relationship by not using the proper deposit method.

## What’s the Problem?

The flash loan function has three important checks in place before it gives out a loan:

1. **Check if the Amount is Greater than Zero**: This ensures that you’re asking for a valid loan amount.
   - **No Vulnerability Here**: There’s no way to get around this check.

2. **Token Address Check**: The contract confirms that the address of the ERC-4626 token you’re trying to use matches what it expects.
   - **No Vulnerability Here**: This is a straightforward check that can’t easily be manipulated.

3. **Share Balance Check**: This is the critical part. The contract checks if the calculated shares match the token balance before the loan is processed. 
   - **Potential Vulnerability**: If tokens are sent using the standard ERC-20 `transfer` or `transferFrom` methods instead of going through the `deposit` function, the share balance won’t update correctly. This can cause the contract to fail the check, leading to a revert and making the loan impossible to execute.

### How Could This Be Exploited?

The key to the attack is to send ERC-4626 tokens to the contract without using the `deposit` function. By doing this, you can increase the token balance without updating the shares. When the contract tries to check the shares against the token balance, it will find a mismatch, causing the loan request to fail.

## Steps to Carry Out the Attack

1. **Deploy an ERC-4626 Token**: First, create a token that follows the ERC-4626 standard, including functions for deposit and withdraw.

2. **Deploy the Flash Loan Contract**: This contract should have the three checks mentioned earlier to manage loans.

3. **Execute the Attack**:
   - Send tokens directly to the flash loan contract using the ERC-20 `transfer` function instead of the `deposit` function.
   - Try to take out a flash loan. The transaction will revert because of the mismatch between the expected shares and the actual token balance, effectively blocking the loan process.

## Conclusion

This investigation highlights a potential vulnerability in flash loan contracts that use the ERC-4626 standard. By taking advantage of the share calculation process, an attacker can make it impossible for the contract to execute flash loans.

## How to Fix This Issue

To prevent this type of attack, here are some steps you can take:

- **Make Sure to Use the Deposit Function**: Require that all token transfers to the contract go through the `deposit` function to keep share calculations accurate.
- **Add Extra Checks**: Implement checks that ensure any token transfers follow the expected process, preventing direct transfers that could cause issues.

