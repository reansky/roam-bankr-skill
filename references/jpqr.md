# JPQR

## Recognition

- Region: Japan.
- Expected local currency when the payload or context supports it: `JPY`.
- Classify as `JPQR` only with strong evidence from a format-aware decoder or
  verified JPQR parser. A Japanese merchant QR is not automatically JPQR.

## Handling

- Extract merchant, amount, currency, and references only when the decoded
  format supplies them.
- Do not assume every Japanese QR accepts USDC, ETH, or any crypto asset.
- Require a verified crypto settlement route specific to the payment request
  before execution.
- If the format or provider is unclear, use `UNKNOWN` or `PAYMENT_URL` rather
  than guessing.

Exact field-level behavior and settlement support require authoritative
verification.
