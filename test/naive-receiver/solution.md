# NaiveReceiver Flash Loan Attack

## Overview

This project explores a potential attack on the **NaiveReceiver** smart contract, which provides flash loans along with deposit and withdrawal functionalities. The contract's reliance on a vulnerable `_msgSender()` function makes it susceptible to exploitation.

## Vulnerability Description

The `NaiveReceiver` contract has a critical vulnerability in the `_msgSender()` function, which processes calls from a `trustedForwarder`. If the `trustedForwarder` is the caller and the `msg.data` exceeds 20 bytes, the contract splits the `msg.data` to extract extra bytes. It then parses these bytes into an address, which becomes the new response for `_msgSender()`.

### Attack Strategy

To exploit this vulnerability, the attacker can call the `withdraw` function of `NaiveReceiver`, forwarding the call through the `trustedForwarder` with crafted `msg.data`. This data will be manipulated to parse as the address of a user who has an existing deposit in the contract.

## Steps to Execute the Attack

1. **Identify a User with Deposits**: 
   - In the `flashLoan` function, fees are not immediately transferred to the `feeReceiver`. Instead, they are stored as the deposit balance of the `feeReceiver`. 
   - The flash loan incurs a fixed fee of **1 ETH** for each loan.

2. **Execute Flash Loans**:
   - The `NaiveReceivePool` has a balance of **10 ETH**.
   - Perform a series of flash loans (up to 10 times), borrowing a feasible amount of WETH while ensuring you can repay the 1 ETH fee. This action will increase the `deposits[feeReceiver]` to **10 ETH**.

3. **Withdraw Attack**:
   - Once the deposit balance of `feeReceiver` is updated to 10 ETH, invoke the `withdraw` function using the crafted `msg.data` to represent the address of the `feeReceiver`. This will allow you to withdraw funds as if you were the actual deposit holder.

## Conclusion

This project highlights a significant vulnerability in the `NaiveReceiver` contract that can be exploited through clever use of the `trustedForwarder`. By manipulating the deposit and withdrawal process, an attacker can gain access to funds that they did not deposit themselves.

## Mitigation Strategies

To safeguard against this type of attack, consider the following:

- **Input Validation**: Ensure that any external calls, especially those involving addresses, are strictly validated and authenticated.
- **Modify `_msgSender()` Logic**: Rework the logic in `_msgSender()` to prevent unauthorized address parsing and ensure it only returns the original caller's address.
- **Immediate Fee Transfers**: Implement a mechanism to transfer fees immediately to the `feeReceiver` instead of accumulating them as deposits.

