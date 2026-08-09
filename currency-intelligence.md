# Currency Intelligence

## Normal form

Every fiat amount must become:

```text
currency_code: ISO-style code when confidently identified
amount: exact numeric amount as supplied
country_or_region: known region, otherwise unknown
```

Preserve the amount exactly. Do not silently round or apply a different decimal
precision.

## Recognized examples

| Input | Normalize to |
| --- | --- |
| `Rp15,000`, `15.000 rupiah`, `IDR 15,000` | `IDR`, `15000` |
| `₹500`, `INR 500`, `500 rupees` | `INR`, `500` |
| `$20`, `USD 20` | `USD`, `20` only when context supports USD |
| `€20`, `EUR 20` | `EUR`, `20` |
| `¥2,000`, `JPY 2,000` | `JPY`, `2000` when Japanese context supports JPY |
| `฿500`, `THB 500` | `THB`, `500` |
| `S$20`, `SGD 20` | `SGD`, `20` |
| `₩20,000`, `KRW 20,000` | `KRW`, `20000` |
| `£20`, `GBP 20` | `GBP`, `20` |
| `A$20`, `AUD 20` | `AUD`, `20` |
| `C$20`, `CAD 20` | `CAD`, `20` |
| `CHF 20` | `CHF`, `20` |
| `CNY 100`, `RMB 100` | `CNY`, `100` |

## Ambiguity rules

- `$` can mean USD, SGD, AUD, CAD, or other dollar currencies. Use country,
  rail, language, and conversation context; ask when confidence is insufficient.
- `¥` can be JPY or CNY. Use explicit code or strong regional context.
- Separators vary by locale. Parse them only when the locale is clear; otherwise
  ask rather than changing the amount.
- A QR's country suggests context but does not override an explicit currency in
  the payload.

Never claim a quote is live unless it came from a current Bankr pricing
capability. If no live quote is available, state that clearly.
