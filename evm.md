# Direct EVM Payments

Use this reference when the decoded payload is a direct EVM payment request,
not a local merchant rail.

## Required extraction

Capture, when present:

- recipient address;
- chain or network;
- token or native asset;
- amount and token decimals;
- payment URI and transaction parameters;
- reference or memo, if the supported transaction mechanism preserves it.

Missing values remain `unknown` or `null`. Never reconstruct omitted values from
context.

## Validation

- Use a standards-aware EVM URI decoder and network-aware address validation.
- Validate recipient, chain, token contract, amount, decimals, and Bankr support.
- Preserve a specified chain such as Base or Robinhood Chain. Do not switch it
  silently.
- Do not treat an arbitrary address-looking string as a valid payment request.
- Never send a token to a contract or chain that has not been validated.

A valid direct EVM request may be executable through Bankr after balance checks,
route validation, and explicit confirmation. A valid URI does not override
ROAM's security or confirmation rules.
