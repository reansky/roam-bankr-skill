# NETS

## Recognition

- Region: Singapore.
- Expected local currency when the payload or context supports it: `SGD`.
- Classify as `NETS` only with strong evidence from a format-aware decoder or
  verified NETS parser.

## Handling

- Extract merchant, amount, currency, and references only from decoded data.
- Do not infer a wallet recipient from a NETS merchant identifier.
- Recognition of a NETS QR does not imply that Bankr can settle it with crypto.
- Require a separately verified crypto-to-NETS settlement route before any
  execution.

Exact protocol fields and live settlement capabilities require verification from
authoritative NETS documentation or an installed, trusted integration.
