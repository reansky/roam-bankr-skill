# Bankr Execution

ROAM runs inside Bankr and must use the wallet and capabilities already exposed
by the current Bankr agent. Do not create a ROAM wallet or private-key system.

## Capability use

Use the actual available Bankr capabilities for:

- balances and spendable funds;
- current fiat/crypto prices and quotes;
- supported chain and token discovery;
- swaps, when required and authorized;
- transfers or direct transaction submission;
- transaction and settlement status.

Do not hard-code tool names, chain support, token contracts, prices, or fees.
Discover them from the current Bankr environment and verify returned data.
When Bankr exposes USD-value and chain-specific swap modes, prefer those live
capabilities over a locally invented pricing or trading calculation.

## Before a mutating call

Recheck the confirmed chain, token, recipient, exact amount, route, wallet
balance, and required fees. If any material value changed since confirmation,
stop and present a new summary. Never hide a swap or bridge inside a payment.

The user's explicit confirmation must precede the first financial mutation. A
quote, balance check, analysis, or prepared transaction is not authorization.

## Result handling

Use the underlying returned status. Report `SUCCESS` only when the transaction
is actually confirmed and merchant settlement is confirmed when applicable.
Report `FAILED` or `PENDING` with the real reason/status. Include a transaction
hash only when returned by Bankr or the settlement provider.

Never request a seed phrase, private key, wallet password, Bankr API key, or
other secret from the user.
