# ROAM Provider Health

Provider health is a non-mutating status check. It must not create invoices,
submit transfers, spend funds, or expose credentials. It may use only a
provider's documented authentication or health mechanism after external
configuration is present.

## Interface

```text
providerHealthCheck(providerContext)
  -> authentication
  -> capabilities
  -> settlement availability
  -> API latency
  -> provider status
```

The implementation must use the selected provider adapter. It must not invent a
URL, endpoint, request field, authentication scheme, or health operation.

## Check stages

### Authentication

Verify that the configured production credentials authenticate successfully.
Do not print, persist, echo, or return the credential value. Missing endpoint or
credentials returns `CONFIGURATION_REQUIRED`.

### Capabilities

Verify the requested country, QR network, fiat currency, crypto asset, and
blockchain against provider documentation or a verified provider response.
Marketing claims alone are insufficient.

InterActive QRIS is permanently classified in this architecture as:

```text
QRIS_RAIL_ONLY
```

It must never be classified as:

```text
CRYPTO_SETTLEMENT_PROVIDER
```

PardhFi, Qrafted, and StableX must not be promoted to `AVAILABLE` until their
production credentials and documentation are obtained and verified.

### Settlement availability

For a crypto payment, require a separate configured settlement provider to
return a verified `recipientAddress`, `paymentId`, `asset`, `chain`, `amount`,
and `expiresAt`. A QRIS merchant ID, QR content, invoice ID, or fiat payment
status is not a crypto recipient.

If the settlement destination is missing, expired, ambiguous, or unverified,
health cannot be `AVAILABLE` for crypto execution.

### API latency

Measure the documented health or authentication request without logging secret
material. Use configured operational thresholds; do not invent a provider's
latency SLA. Excessive latency or repeated timeouts produces `DEGRADED` or
`UNAVAILABLE`, according to the host's configured policy.

### Provider status

Read the provider's registry status and current verified response. Do not mark a
provider `AVAILABLE` merely because a website loads or a marketing page exists.

## Result states

Return exactly one of:

### `AVAILABLE`

Use only when authentication, required capabilities, settlement availability,
latency, and registry status all pass. This status still does not bypass
`CAN_EXECUTE_REAL_PAYMENT`.

### `DEGRADED`

Use when the provider authenticates but latency, capability coverage, or a
non-critical health signal is impaired. Real payment execution remains blocked
unless the final production gate independently passes.

### `UNAVAILABLE`

Use when authentication fails, the provider is offline, a required capability is
absent, or a verified settlement route is unavailable.

### `CONFIGURATION_REQUIRED`

Use when registration, production endpoint, credentials, onboarding, adapter
contract, or required documentation is missing. This is the current state for
unconfigured providers and the crypto settlement placeholder.

## Pseudocode

```text
providerHealthCheck(context):
  if not context.providerRegistered:
    return CONFIGURATION_REQUIRED
  if not context.productionApiUrl or not context.credentialsExist:
    return CONFIGURATION_REQUIRED
  if not authenticateThroughDocumentedAdapter(context):
    return UNAVAILABLE
  if not capabilitiesVerified(context):
    return UNAVAILABLE
  if context.classification == QRIS_RAIL_ONLY and context.cryptoRequested:
    return UNAVAILABLE
  if context.cryptoRequested and not verifiedSettlementDestination(context):
    return UNAVAILABLE
  if apiLatencyOutsideConfiguredPolicy(context):
    return DEGRADED
  if context.registryStatus != AVAILABLE:
    return CONFIGURATION_REQUIRED
  return AVAILABLE
```

No branch may execute a transfer. The health result is only an input to
`CAN_EXECUTE_REAL_PAYMENT` in `roam/provider-readiness.md`.
