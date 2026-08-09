# Provider Registry

This registry records recognition and adapter configuration state. A provider
is not `AVAILABLE` until onboarding, credentials, external endpoint
configuration, and capability checks are complete.

## Provider status vocabulary

Use only these statuses:

`AVAILABLE`, `EARLY_ACCESS`, `WAITLIST`, `REQUIRES_ONBOARDING`,
`CONFIGURATION_REQUIRED`, `UNSUPPORTED`.

`AVAILABLE` requires verified production API access. Public marketing claims,
waitlist access, documentation without credentials, or an unverified route are
not sufficient.

## QRIS and QR-related providers

| Country | QR networks | Fiat | Provider | Status |
| --- | --- | --- | --- | --- |
| ID | QRIS-compatible claim | IDR | pardhfi | EARLY_ACCESS |
| ID | QRIS, PromptPay, VietQR, KHQR claims | IDR and regional fiat | qrafted | WAITLIST |
| ID | QRIS context only; arbitrary merchant route unverified | IDR via IDRX | stablex | CONFIGURATION_REQUIRED |
| ID | QRIS | IDR | interactive-qris | REQUIRES_ONBOARDING |

InterActive QRIS is a fiat QRIS merchant-invoice provider. It is not a crypto
recipient and does not provide direct USDC or ETH settlement. The other rows
are not verified arbitrary-merchant crypto settlement routes.

## Crypto settlement providers

| Asset | Chain | Provider registration | Interface | Status |
| --- | --- | --- | --- | --- |
| USDC | Base | external-configuration-required | CryptoSettlementProvider | CONFIGURATION_REQUIRED |

This is a placeholder registration only. No crypto settlement provider,
endpoint, credential, or recipient address is configured in this repository.

See `references/providers/capability-matrix.md` for the detailed public-claim
matrix and these files for the provider-specific adapter interfaces:

- `references/providers/pardhfi.md`
- `references/providers/qrafted.md`
- `references/providers/stablex.md`
- `references/providers/indonesia-qris-interactive.md`

Each interface is
`IMPLEMENTATION_REQUIRED`; no undocumented API call is implemented.

## Runtime defaults

```text
ROAM_TEST_MODE=true
```

Test mode is enabled by default. Live QRIS or crypto execution requires an
explicitly changed runtime mode plus completed onboarding, external
configuration, verified capabilities, balance checks, and user confirmation.

The mandatory readiness and health gates are documented in
`roam/provider-readiness.md` and `roam/provider-health.md`. They must return
`CAN_EXECUTE_REAL_PAYMENT=true` before any live transaction. The current
`ROAM_TEST_MODE=true` setting guarantees that result is `false`.

## Settlement gate

The QRIS adapter may create and check an IDR invoice only when its own provider
configuration is complete. It must never be used as the crypto settlement
layer.

The crypto settlement adapter must return a verified `recipientAddress`,
`paymentId`, `asset`, `chain`, `amount`, and `expiresAt` before Bankr may be
asked to transfer crypto. If that result is unavailable, the route remains
`CONFIGURATION_REQUIRED` and execution must stop.
