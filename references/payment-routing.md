# Payment Routing

ROAM has separate states for recognition, quotation, transaction, and merchant
settlement. A successful state in one layer does not imply success in another.

## Route states

- `VERIFIED`: a current, specific route is available, its recipient/provider is
  known, and its settlement semantics are documented or confirmed.
- `UNAVAILABLE`: no route is available in the current Bankr environment.
- `UNVERIFIED`: a possible route was observed, but it is not safe to execute.
- `UNKNOWN`: required route details could not be determined.

Only `VERIFIED` permits execution, and only after all other gates pass.

## Local merchant rails

QRIS, UPI, NETS, PromptPay, and JPQR can be recognized without being payable
with crypto. Do not convert a fiat amount and send the resulting crypto to a
merchant identifier unless an actual settlement connector specifies that exact
behavior. Generic payment URLs require the same scrutiny.

When settlement is unavailable, preserve the analysis and quote but say:

> ROAM identified the payment request, but no verified crypto settlement route is currently available.

Do not offer a direct transfer as an equivalent substitute unless the user asks
and the destination is independently validated as a direct crypto recipient.

## Direct crypto routes

For EVM or crypto-native requests, the recipient, chain, token, amount, and
transaction semantics must be validated. A direct wallet transfer is not a
merchant-rail settlement unless the request itself is a direct crypto payment.

## Multi-step routes

For `swap -> bridge -> payment`, show every step, asset, chain, amount, and fee.
Verify every dependency. Stop when any dependency is unavailable or unverified.
