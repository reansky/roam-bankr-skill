# Provider Capability Matrix

This matrix records public claims and verification state. Public marketing
claims are not production API access. `AVAILABLE` is reserved for a provider
with verified production API access, completed onboarding, external credentials,
and a reviewed settlement contract. No provider below is `AVAILABLE`.

| Provider | Country | QR networks | Fiat | Crypto | Chains | Merchant settlement | Crypto settlement | API availability | Onboarding required | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| PardhFi | Indonesia | QRIS-compatible claim; format and route unverified | IDR claim | IDR stablecoins claim | Not publicly verified | QR payments and merchant APIs publicly described; production route unverified | IDR stablecoins/on-off-ramp claims; direct QRIS crypto settlement unverified | No public API specification or production access verified | Early-access/waitlist enrollment, merchant/provider onboarding, API access | EARLY_ACCESS |
| Qrafted | Indonesia and Asia | QRIS, PromptPay, VietQR, KHQR publicly shown | IDR, THB, VND, other regional currencies | USDC funding publicly shown | Solana publicly shown for USDC top-up; Base not verified | Local QR merchant payment and local-currency settlement publicly described | USDC funding/local payout claims; direct USDC/Base to arbitrary QRIS unverified | No public production API specification or credentials verified | Waitlist, account/KYC, launch and merchant/API onboarding | WAITLIST |
| StableX | Indonesia and Southeast Asia | QRIS mentioned in local-currency context; arbitrary merchant settlement unverified | IDR via IDRX and other SEA currencies | USDC, IDRX, other stablecoins | Base, Ethereum, Polygon, Arbitrum, BSC, Solana | Public merchant checkout, IDRX settlement, and cash-out claims | Public checkout/x402 and Base claims for configured merchants; QRIS route unverified | Public checkout example; reviewed production API access not verified | Business registration, API credential, and applicable IDRX compliance onboarding | CONFIGURATION_REQUIRED |
| InterActive QRIS | Indonesia | QRIS | IDR | None documented by the QRIS API | None documented by the QRIS API | Official Create Invoice and Check Invoice Status API | Not provided; not a crypto settlement provider | Official public API documentation; no production credentials verified | Merchant registration, mID, NMID, PKS, NDA, APIKEY | REQUIRES_ONBOARDING |

## Interpretation rules

- QRIS compatibility is not proof of arbitrary-merchant crypto settlement.
- A public checkout API is not verified ROAM provider access.
- A public USDC, IDRX, or Base claim is not proof that the provider can pay a
  QRIS merchant code.
- InterActive QRIS is the strongest documented QRIS fiat-invoice candidate, but
  it remains separate from crypto settlement.
- No crypto settlement provider is currently configured or verified.

## Default execution state

```text
ROAM_TEST_MODE=true
```

No matrix row may be promoted to `AVAILABLE` unless the provider's production
API access, credentials, onboarding, request/response contract, and settlement
semantics are verified. Bankr must not execute a transfer from this matrix
alone.
