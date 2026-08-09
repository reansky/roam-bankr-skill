# PromptPay

## Recognition

- Region: Thailand.
- Expected local currency when the payload or context supports it: `THB`.
- Classify as `PROMPTPAY` only with strong evidence from a format-aware decoder
  or verified PromptPay parser.

## Handling

- Preserve decoded recipient, merchant, amount, currency, and references.
- A PromptPay recipient is not automatically an EVM recipient and must not be
  converted into a wallet address by guesswork.
- Do not treat a readable PromptPay QR as crypto-payable without a verified
  settlement connector.
- Verify all exact protocol details before implementing parser-specific logic.

Exact field semantics and settlement capabilities require authoritative
verification or a trusted installed parser.
