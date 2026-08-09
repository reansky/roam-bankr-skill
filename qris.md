# QRIS

## Recognition

- Region: Indonesia.
- Expected local currency when the payload or context supports it: `IDR`.
- Classify as `QRIS` only with strong evidence from a format-aware decoder or
  verified QRIS parser. Text that merely contains "QRIS" is not sufficient.

## Handling

- Extract merchant identifiers, merchant name, amount, currency, and references
  only when they are actually present in the decoded payload.
- A QRIS payload may identify an Indonesian merchant request without providing a
  crypto recipient or crypto settlement route.
- Do not infer IDR from a merchant's country if the payload explicitly provides
  another currency.
- Do not claim that QRIS is payable with USDC or ETH unless a verified,
  currently available settlement connector says so.

Exact field-level parsing and settlement behavior require verification against
an authoritative QRIS specification or an installed, trusted parser.
