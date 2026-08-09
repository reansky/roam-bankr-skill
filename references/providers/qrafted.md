# Qrafted Provider Adapter

## Public capability record

- Provider: Qrafted
- Provider ID: `qrafted`
- Country focus: Indonesia within an Asia-wide product
- QR networks: QRIS, PromptPay, VietQR, and KHQR are publicly shown by the product page
- Fiat: IDR, THB, VND, and other regional currencies described but not fully enumerated
- Crypto: USDC top-up is publicly described
- Chains: Solana is publicly described for USDC top-up; Base is not verified for this provider
- Merchant settlement: The public product page describes local QR merchant payment and local-currency settlement
- Crypto settlement: The public product page describes USDC funding and local payouts; direct USDC/Base to arbitrary QRIS settlement is not verified
- API availability: No public provider API specification, production endpoint, or credentials verified
- Onboarding: Waitlist, account creation, KYC, and any merchant/API onboarding required
- Status: `WAITLIST`

The supplied website is `https://qrafted.com/`. The public page states “Bangkok
pilot · Q3 2026” and invites users to join a waitlist. It describes QRIS
scanning, USDC top-up, Solana infrastructure, and local payouts as product
claims. Those claims do not provide a production API contract or authorize ROAM
to execute payments.

## Adapter interface

```text
QraftedProvider {
  quotePayment()
  createPayment()
  getPaymentStatus()
  cancelPayment()
}
```

## Implementation-required fields

```text
provider_status: WAITLIST
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
crypto_funding_and_settlement_semantics: IMPLEMENTATION_REQUIRED
```

No provider API call is implemented. Do not mark Qrafted `AVAILABLE` until the
waitlist/launch status changes, production API access is granted, credentials
are provisioned through a secure channel, and QRIS settlement semantics are
verified.

Do not infer Base support from ROAM's own Base support. Do not claim that the
public USDC funding description proves direct USDC/Base settlement to arbitrary
QRIS merchants.
