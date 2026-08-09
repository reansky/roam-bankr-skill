# StableX Provider Adapter

## Public capability record

- Provider: StableX
- Provider ID: `stablex`
- Country focus: Indonesia and Southeast Asia
- QR networks: QRIS is mentioned in local-currency and bank-account context; arbitrary-merchant QRIS settlement is not verified
- Fiat: IDR through IDRX and other Southeast Asian local-currency contexts
- Crypto: USDC, IDRX, and other stablecoins publicly shown
- Chains: Base, Ethereum, Polygon, Arbitrum, BSC, and Solana publicly shown
- Merchant settlement: Public merchant checkout and IDRX settlement/cash-out claims
- Crypto settlement: Public crypto checkout/x402 and Base settlement claims for configured merchant checkout; no arbitrary QRIS settlement is confirmed
- API availability: Public merchant checkout API example is shown, but no reviewed public API specification or production access is verified
- Onboarding: Business/merchant registration, API credential issuance, and applicable IDRX KYC/AML/payment onboarding
- Status: `CONFIGURATION_REQUIRED`

The supplied website is `https://stablex.id/`. Its public business page
describes merchant checkout, USDC payment, IDRX settlement, Base, and webhooks.
Its public home page mentions QRIS in a local-currency/bank-account workflow, but
does not establish that StableX can pay arbitrary QRIS merchant codes. That
capability must remain unverified.

The public page contains an API example. ROAM deliberately does not copy its
endpoint, example API key, request body, or authentication details into this
skill. A marketing example is not a verified production integration contract.

## Adapter interface

```text
StableXProvider {
  quotePayment()
  createPayment()
  getPaymentStatus()
  cancelPayment()
}
```

## Implementation-required fields

```text
provider_status: CONFIGURATION_REQUIRED
endpoint: EXTERNAL_CONFIGURATION_REQUIRED
credentials: EXTERNAL_SECRET_STORE_REQUIRED
quotePayment: IMPLEMENTATION_REQUIRED
createPayment: IMPLEMENTATION_REQUIRED
getPaymentStatus: IMPLEMENTATION_REQUIRED
cancelPayment: IMPLEMENTATION_REQUIRED
request_schema: IMPLEMENTATION_REQUIRED
response_schema: IMPLEMENTATION_REQUIRED
webhook_contract: IMPLEMENTATION_REQUIRED
qr_settlement_semantics: IMPLEMENTATION_REQUIRED
merchant_checkout_settlement_semantics: IMPLEMENTATION_REQUIRED
```

No provider API call is implemented. Do not mark StableX `AVAILABLE` without
verified production credentials, reviewed API documentation, merchant
onboarding, and a route-specific confirmation that the requested QRIS merchant
payment is supported.

StableX's public crypto checkout and Base claims must not be converted into a
claim that USDC/Base can settle an arbitrary QRIS merchant.
