# PardhFi Provider Adapter

## Public capability record

- Provider: PardhFi
- Provider ID: `pardhfi`
- Country focus: Indonesia
- QR networks: QRIS-compatible, based on the supplied public capability description
- Fiat: IDR, based on the supplied public capability description
- Crypto: IDR stablecoins, based on the supplied public capability description
- Chains: Not publicly verified
- Merchant settlement: QR payments and merchant APIs are publicly described, but production access is not verified
- Crypto settlement: IDR stablecoins and on/off-ramp capabilities are publicly described; direct USDC/Base to arbitrary QRIS settlement is not verified
- API availability: No public API specification or production credentials verified
- Onboarding: Early-access or waitlist enrollment and provider/API onboarding required
- Status: `EARLY_ACCESS`

The supplied website is `https://www.pardh.fi/`. Browser access during this
review returned a TLS certificate error, so the claims above are not treated as
verified API documentation. No endpoint, authentication scheme, request field,
response field, chain, or credential is inferred from the website name or
marketing description.

## Adapter interface

```text
PardhFiProvider {
  quotePayment()
  createPayment()
  getPaymentStatus()
  cancelPayment()
}
```

## Implementation-required fields

```text
provider_status: EARLY_ACCESS
endpoint: EXTERNAL_CONFIGURATION_REQUIRED
credentials: EXTERNAL_SECRET_STORE_REQUIRED
quotePayment: IMPLEMENTATION_REQUIRED
createPayment: IMPLEMENTATION_REQUIRED
getPaymentStatus: IMPLEMENTATION_REQUIRED
cancelPayment: IMPLEMENTATION_REQUIRED
request_schema: IMPLEMENTATION_REQUIRED
response_schema: IMPLEMENTATION_REQUIRED
webhook_or_polling_contract: IMPLEMENTATION_REQUIRED
qr_settlement_semantics: IMPLEMENTATION_REQUIRED
crypto_settlement_semantics: IMPLEMENTATION_REQUIRED
```

No provider API call is implemented. Do not mark PardhFi `AVAILABLE` without
verified production API access, onboarding completion, documented credentials,
and a reviewed payment/settlement contract.

Do not claim that PardhFi enables USDC/Base payment to an arbitrary QRIS
merchant. A QRIS-compatible claim is not proof of a crypto settlement route.
