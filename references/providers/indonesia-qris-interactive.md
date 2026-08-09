# Indonesia QRIS Provider: InterActive QRIS

## Provider identity

- Provider: InterActive QRIS
- Provider ID: `interactive-qris`
- Configuration selector: `ROAM_QRIS_PROVIDER=interactive`
- QR network: QRIS
- Country: Indonesia
- Fiat: IDR
- Default status: `CONFIGURATION_REQUIRED`
- Default mode: `ROAM_TEST_MODE=true`

This document is based on the official InterActive QRIS API documentation:

- General documentation: https://qris.id/api-doc/
- Create Invoice: https://qris.id/api-doc/create-invoice.php
- Check Invoice Status: https://qris.id/api-doc/check-invoice.php

The documentation displayed version is `v1.26.0701`. Re-verify the provider
documentation before enabling live execution.

## Scope and settlement boundary

The provider API is documented for QRIS merchant payment and invoice
processing. It creates a QRIS invoice for an IDR transaction and checks whether
the QRIS invoice was paid through a supported QRIS payment method.

This API does **not** automatically provide crypto settlement. InterActive
QRIS is not a crypto recipient, wallet, exchange, bridge, or Bankr transfer
destination.

ROAM must not claim that InterActive QRIS accepts `USDC` or `ETH` directly.
The QRIS provider and crypto settlement layers remain separate.

## Required onboarding

Live use requires completion and verification of all provider onboarding steps:

- merchant registration;
- `mID` (InterActive Merchant ID);
- `NMID` (National Merchant ID from PTEN/Bank Indonesia context);
- `PKS` (the provider partnership agreement);
- `NDA` (non-disclosure agreement);
- `APIKEY` issued through the provider onboarding process.

Do not mark this provider `AVAILABLE` until onboarding is complete and the
credentials are supplied through the host's secret/configuration system. Never
place an API key, merchant ID, agreement, or other credential in this skill.

## External configuration

The provider endpoint and credentials are intentionally external. Keep these
placeholders empty in the repository and inject values through the runtime
configuration system:

```text
ROAM_QRIS_PROVIDER=interactive
ROAM_QRIS_API_URL=
ROAM_QRIS_API_KEY=
ROAM_QRIS_MERCHANT_ID=
ROAM_TEST_MODE=true
```

`ROAM_QRIS_MERCHANT_ID` maps to the provider's `mID` request parameter. The
provider's `APIKEY` maps to `ROAM_QRIS_API_KEY`. No endpoint value is committed
here; `ROAM_QRIS_API_URL` must be populated only from approved provider
configuration and documentation.

`ROAM_TEST_MODE=true` is fail-closed. Do not call a live provider or create an
invoice while it is enabled. Setting it to `false` is not sufficient by itself:
onboarding, credentials, endpoint validation, capability checks, and explicit
payment confirmation are still required.

## Capability flags

```text
supportsCryptoDirect: false
supportsFiatSettlement: true
supportsDynamicQRIS: true
supportsStaticQRIS: false
```

These flags describe the documented adapter boundary. `supportsFiatSettlement`
means the provider can process the documented QRIS merchant invoice flow in IDR;
it does not mean the provider settles crypto. `supportsDynamicQRIS` is based on
the documented Create Invoice response returning QRIS content and an invoice
identifier. Static QRIS is not implemented by this adapter.

## Adapter interface

The provider-specific implementation must conform to this language-neutral
interface. Request and response schemas must be validated against the official
provider documentation and external configuration at implementation time.

```text
IndonesiaQRISProvider {
  createInvoice()
  checkInvoiceStatus()
}
```

The adapter must not expose credentials to ROAM responses or webpages. It must
return provider status and identifiers without converting them into a crypto
recipient.

## ROAM provider adapter facade

The documented provider operations above are not a crypto payment interface.
The ROAM adapter facade must expose these methods without inventing undocumented
requests:

```text
InteractiveQRISProvider {
  quotePayment()
  createPayment()
  getPaymentStatus()
  cancelPayment()
}
```

Every field below is intentionally unimplemented until provider access and a
reviewed contract are available:

```text
quotePayment: IMPLEMENTATION_REQUIRED
createPayment: IMPLEMENTATION_REQUIRED
getPaymentStatus: IMPLEMENTATION_REQUIRED
cancelPayment: IMPLEMENTATION_REQUIRED
endpoint: EXTERNAL_CONFIGURATION_REQUIRED
credentials: EXTERNAL_SECRET_STORE_REQUIRED
request_schema: IMPLEMENTATION_REQUIRED
response_schema: IMPLEMENTATION_REQUIRED
webhook_or_polling_contract: IMPLEMENTATION_REQUIRED
```

`createPayment()` may eventually be mapped to the documented Create Invoice
operation for an IDR QRIS invoice. `getPaymentStatus()` may eventually be mapped
to Check Invoice Status. The official documentation does not provide a quote
operation or a cancellation operation, so ROAM must not invent either call.
`cancelPayment()` remains unavailable until InterActive documents a supported
cancellation flow.

This facade handles fiat QRIS invoicing only. It must never return a crypto
recipient and must never be treated as a crypto settlement provider.

## Official API operations

### Create Invoice

The official documentation describes a `GET` operation that generates a dynamic
QRIS invoice. The runtime endpoint remains `ROAM_QRIS_API_URL`; this repository
does not hard-code an API endpoint.

The documented request fields are:

| Field | Requirement | Meaning |
| --- | --- | --- |
| `do` | Required | Must be the documented Create Invoice value: `create-invoice`. |
| `apikey` | Required | Provider-issued APIKEY, supplied externally. |
| `mID` | Required | Provider-issued merchant ID, supplied externally. |
| `cliTrxNumber` | Required | Client application's QRIS transaction/invoice number. |
| `cliTrxAmount` | Required | Final IDR amount payable by the customer. |
| `useTip` | Required | Provider-documented tip choice, such as `yes` or `no`. |

The documented successful response includes:

- `qris_content`;
- `qris_request_date`;
- `qris_invoiceid`;
- `qris_nmid`.

The returned QR content is for displaying the QRIS invoice through the host's
approved QR renderer. The documented expiration is 30 minutes from QR display.
The provider documentation warns that its API is live/production and that a
successful scan can reduce the payer's e-wallet balance without an automatic
refund. ROAM must therefore keep `ROAM_TEST_MODE=true` until live use is
deliberately enabled.

### Check Invoice Status

The official documentation describes a `GET` operation that checks a dynamic
QRIS invoice using the invoice ID. The runtime endpoint remains external via
`ROAM_QRIS_API_URL`.

The documented request fields are:

| Field | Requirement | Meaning |
| --- | --- | --- |
| `do` | Required | Must be the documented Check Invoice Status value: `checkStatus`. |
| `apikey` | Required | Provider-issued APIKEY, supplied externally. |
| `mID` | Required | Provider-issued merchant ID, supplied externally. |
| `invid` | Required | `qris_invoiceid` returned by Create Invoice. |
| `trxvalue` | Required | Final IDR transaction amount. |
| `trxdate` | Required | Payment date in the provider-documented `YYYY-mm-dd` format. |

The documented successful response reports `qris_status: paid` and may include
the payer name and payment method. A documented failed response reports
`qris_status: unpaid`.

Follow the provider's documented polling restrictions. Do not continuously
auto-poll at a tight interval. The documentation describes user-triggered
checks or limited retry schedules and warns that excessive checking can block
the APIKEY.

An InterActive status of `paid` confirms the QRIS invoice's fiat payment status
within that provider's flow. It does not confirm a crypto transfer and must not
be reported as crypto settlement.

## Crypto separation rule

Never pass `qris_content`, `qris_invoiceid`, `qris_nmid`, `mID`, or a QRIS
merchant identifier to Bankr as a crypto recipient. The crypto settlement
provider is a separate interface documented in
`references/providers/crypto-settlement.md`.

Only after that separate provider returns a verified `recipientAddress`, asset,
chain, amount, expiry, and payment ID may the Bankr execution layer validate and
perform a crypto transfer, subject to ROAM confirmation and balance rules.
