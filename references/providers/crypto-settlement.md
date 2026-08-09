# Crypto Settlement Provider

Crypto settlement is intentionally separate from QRIS merchant invoicing.
InterActive QRIS does not implement this interface and must never be used as a
crypto recipient.

## Interface

Any actual crypto settlement integration must conform to this language-neutral
interface:

```text
CryptoSettlementProvider {
  quote()
  createPayment()
  getPaymentStatus()
}
```

The concrete provider, endpoint, credentials, supported assets, and supported
chains remain external configuration. This repository does not register a live
crypto settlement vendor, endpoint, credential, or wallet address.

## Required payment result

For a successful payment creation response, the provider must return these
verified fields:

```text
paymentId
recipientAddress
asset
chain
amount
expiresAt
```

`recipientAddress` must be independently verified as the intended settlement
destination for the payment ID, asset, chain, amount, and expiry. Missing,
ambiguous, expired, or unverified fields are a hard stop.

## Bankr handoff

Only after the crypto settlement provider returns a verified `recipientAddress`
may Bankr perform the crypto transfer. Before transfer, ROAM must also validate
the asset, chain, amount, expiry, wallet balance, fees, and user confirmation.

The QRIS provider's merchant ID, NMID, invoice ID, QR content, and status are
not crypto recipients and must not be substituted for `recipientAddress`.

If no configured crypto settlement provider returns a verified destination,
ROAM must not execute a crypto transfer and must report that no verified crypto
settlement route is available.

## Default state

```text
provider: external-configuration-required
status: CONFIGURATION_REQUIRED
ROAM_TEST_MODE=true
```

`external-configuration-required` is a placeholder registration, not a real
provider and not an available settlement route.
