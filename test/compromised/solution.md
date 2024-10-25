# Compromised

## Challenge Overview
In this challenge, the goal is to manipulate the price of a token in order to buy NFTs at a low price, sell them at a higher price, and transfer the profit to a specified recovery address.

## Solution Strategy

1. **Identify Vulnerability in Server Response**  
   The server’s unusual response might contain sensitive data, potentially including the private keys of the trusted reporters for the oracle. If we can retrieve these private keys, we can manipulate the oracle's price reports.

2. **Extract Private Keys**  
   By analyzing the server response, extract any data that resembles private keys. If successful, the oracle's price reporting mechanism can be compromised to set any desired price.

3. **Manipulate Token Price**  
   - **Lower Price**: Use the compromised oracle to drastically lower the token price. This will enable buying NFTs at a minimal cost.
   - **Buy NFTs**: Purchase as many NFTs as possible at the artificially low price.
   - **Raise Price**: After acquiring the NFTs, manipulate the price again to a much higher value.
   - **Sell NFTs**: Sell the NFTs at the increased price to maximize profit.

4. **Transfer Profits**  
   Finally, send all the acquired funds to a predefined recovery address to secure the profits.

## Important Notes
- This solution assumes direct access to oracle manipulation through compromised private keys.
- Ensure proper logging and response handling during each step to detect any anomalies.
  
## Security Lessons Learned
1. **Private Key Protection**: Sensitive keys must be secured at all times to avoid similar exploits.
2. **Oracle Hardening**: Consider multi-signature schemes or delayed updates to protect against malicious reporter actions.
3. **Server Response Scrutiny**: Never expose sensitive data, even accidentally, in server responses.
  
This attack demonstrates the critical need for secure oracle management in blockchain systems to prevent price manipulation vulnerabilities.
