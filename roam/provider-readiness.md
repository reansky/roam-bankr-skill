# ROAM Provider Readiness

This is a mandatory fail-closed check before any live payment attempt. It is a
readiness contract, not permission to execute a transaction. It must not send
funds, create an invoice, or disclose credentials while evaluating readiness.

## Readiness inputs

Evaluate the requested provider, country, QR network, fiat currency, crypto
asset, blockchain, quote, recipient, wallet state, and user confirmation as one
immutable payment context. Do not silently replace any requested value.

## Required checks

Every check must pass before readiness can be `true`:

1. Provider is registered in `references/providers/registry.md`.
2. Provider registry status is exactly `AVAILABLE`.
3. A production API URL exists in external configuration.
4. Required production credentials exist in environment variables or the host
   secret store.
5. Provider authentication succeeds through the provider adapter's documented
   authentication path.
6. Provider supports Indonesia for the requested payment.
7. Provider supports the `QRIS` network for the requested payment.
8. Provider supports the requested fiat currency, including `IDR` for QRIS.
9. Provider supports the requested crypto asset.
10. Provider supports the requested blockchain.
11. A separate settlement provider exposes a verified settlement destination.
12. The provider quote is valid for this exact payment context.
13. The quote has not expired.
14. The payment has a unique idempotency key that is bound to this payment
    context.
15. The user has explicitly confirmed the exact payment summary.

The requested asset and chain checks apply to the crypto settlement provider,
not to a fiat-only QRIS rail. InterActive QRIS is `QRIS_RAIL_ONLY`; it is never a
`CRYPTO_SETTLEMENT_PROVIDER` and cannot satisfy the crypto asset, blockchain, or
verified recipient checks by itself.

## Configuration-required behavior

Return `false` with a reason if any required value is absent, ambiguous, stale,
unverified, or supplied only by marketing text. Do not downgrade a failed check
to a warning.

PardhFi, Qrafted, and StableX must remain unavailable until their production API
credentials, API documentation, onboarding, capability contract, and route-
specific settlement behavior are verified. InterActive QRIS must remain
`REQUIRES_ONBOARDING` until its own onboarding and credentials are verified, and
it still cannot provide crypto settlement.

## External environment contract

These are configuration names only. No values, credentials, endpoints, or keys
are stored in this repository:

```text
ROAM_TEST_MODE=true
ROAM_QRIS_PROVIDER=interactive
ROAM_QRIS_API_URL=
ROAM_QRIS_API_KEY=
ROAM_QRIS_MERCHANT_ID=
ROAM_CRYPTO_SETTLEMENT_PROVIDER=
ROAM_CRYPTO_SETTLEMENT_API_URL=
ROAM_CRYPTO_SETTLEMENT_API_KEY=
```

The QRIS variables are used only for a configured QRIS rail adapter. The crypto
settlement variables are generic placeholders for a separately selected
provider. Provider-specific names may be mapped by the host secret/configuration
system, but their values must never be committed here.

`PKS`, `NDA`, `mID`, and `NMID` are onboarding requirements for InterActive QRIS,
not secrets that ROAM stores in this package. Bankr wallet balance and gas are
read through Bankr capabilities; no Bankr credential environment variable is
defined by ROAM. The idempotency key is generated and bound to each payment at
runtime; it is not a static environment variable.

## Readiness pseudocode

```text
providerReadiness(context):
  require context.providerRegistered
  require context.providerStatus == AVAILABLE
  require nonEmpty(context.productionApiUrl)
  require context.productionCredentialsExist
  require context.providerAuthentication == AUTHENTICATED
  require context.supportsCountry("ID")
  require context.supportsQrNetwork("QRIS")
  require context.supportsFiat("IDR")
  require context.cryptoSettlementProvider != QRIS_RAIL_ONLY
  require context.supportsAsset(context.requestedAsset)
  require context.supportsChain(context.requestedChain)
  require context.recipientAddress.verified == true
  require context.quote.valid == true
  require context.quote.expiresAt > now()
  require nonEmpty(context.idempotencyKey)
  require context.userConfirmation.explicit == true
  return true
```

The `require` operations are logical checks only. They must not invoke an
undocumented provider operation. Provider-specific calls belong in a reviewed
adapter implementation and remain external to this document.

## Production gate

The final gate is named exactly:

```text
CAN_EXECUTE_REAL_PAYMENT
```

It returns `true` only when every condition below is true:

```text
CAN_EXECUTE_REAL_PAYMENT(context):
  return (
    context.testMode == false
    and context.providerStatus == AVAILABLE
    and context.providerHealth == AVAILABLE
    and context.credentialsValid == true
    and context.quote.valid == true
    and context.quote.expiresAt > now()
    and context.recipientAddress.verified == true
    and context.bankrWalletBalance.sufficient == true
    and context.bankrGasBalance.sufficient == true
    and context.userConfirmation.explicit == true
    and context.idempotencyKey.present == true
  )
```

Otherwise it returns `false`. There is no override, bypass, fallback, or manual
exception. A provider health result of `AVAILABLE` does not override
`ROAM_TEST_MODE=true`.

## Current state

`ROAM_TEST_MODE=true` remains enabled. No provider currently satisfies all
conditions. Therefore:

```text
CAN_EXECUTE_REAL_PAYMENT = false
```

No transaction may be sent.
