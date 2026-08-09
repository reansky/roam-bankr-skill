# UPI

## Recognition

- Region: India.
- Expected local currency when the payload or context supports it: `INR`.
- Classify as `UPI` only with strong evidence from a format-aware decoder or
  verified UPI parser. Do not classify from arbitrary text containing "upi".

## Handling

- Preserve the exact decoded payee, merchant identity, amount, currency, and
  transaction/reference values.
- A UPI identifier or payment URI is not itself a crypto settlement route.
- Do not invent a recipient wallet, exchange rate, payment status, or merchant
  confirmation.
- Do not claim direct USDC or ETH payment to UPI unless a verified connector is
  available in the current Bankr environment.

Exact URI fields and settlement behavior require verification against an
authoritative UPI source or an installed, trusted parser.
