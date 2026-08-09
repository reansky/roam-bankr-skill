# Crypto QR

Use this reference for crypto-native QR codes that are not being treated as a
local fiat merchant rail.

## Extract

Identify only values present in the payload:

- chain/network;
- token or native asset;
- recipient;
- amount;
- payment URI and transaction parameters.

If the QR specifies a chain, token, or amount, preserve it. If it omits one,
leave it unknown and ask the user or use a verified default only when Bankr
explicitly documents one.

## Execute safely

Validate the recipient, token, chain, amount, token decimals, wallet balance,
and Bankr transaction support. Show the full payment summary and obtain explicit
confirmation. Do not confuse a crypto QR with QRIS, UPI, NETS, PromptPay, or
JPQR. Do not invent a missing token, amount, chain, or confirmation.
