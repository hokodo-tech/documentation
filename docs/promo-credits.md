## API interface documentation

This changes nothing for existing API clients who don't support it yet. If a promotion/credit applies, they will see the price post-promotion/credit in lieu of the usual price.

Notes:
- `total_price` always reflects the price **after** discounts have been applied
- `total_price_without_discount` always reflects the price **before** discounts have been applied
- `premium` and `ipt` always reflect the price **before** discounts have been applied


### Quote offered with no promotion/credit

```json
{
    "status": "offered",
    "price": {
        "currency": "EUR",
        "total_price": "109.00",
        "premium": "100.00",
        "ipt": "9.00",
        "total_price_without_discount": "109.00",
        "discount_amount": null,
        "discounts": [],
    },
    "payment_required": true
}
```

### Quote offered with promotion/credit

- EUR 100 credits available.
- EUR 10 credits applied to the EUR 109 quote.
- EUR 99 left to pay
- EUR 90 credits left (artificial case, we would use all available credits usually)

```json
{
    "status": "offered",
    "price": {
        "currency": "EUR",
        "total_price": "99.00",
        "premium": "100.00",
        "ipt": "9.00",
        "total_price_without_discount": "109.00",
        "discount_amount": "10.00",
        "discounts": [
            {
                "type": "credits",
                "discount_reason": "Because we like offered quotes!",
                "discount_amount": "10.00",
                "remaining_credits_amount_after": "90.00",
                "remaining_credits_amount_after_currency": "EUR",
            },
        ],
    },
    "payment_required": true
}
```


### Quote offered with promotion/credit – different currency

- GBP 100 credits available.
- EUR 10 credits applied to the EUR 109 quote.
- EUR 99 left to pay
- GBP 91.22 credits left (artificial case, we would use all available credits usually)

```json
{
    "status": "offered",
    "price": {
        "currency": "EUR",
        "total_price": "99.00",
        "premium": "100.00",
        "ipt": "9.00",
        "total_price_without_discount": "109.00",
        "discount_amount": "10.00",
        "discounts": [
            {
                "type": "credits",
                "discount_reason": "Because we like offered quotes!",
                "discount_amount": "10.00",
                "remaining_credits_amount_after": "91.22",
                "remaining_credits_amount_after_currency": "GBP",
            },
        ],
    },
    "payment_required": true
}
```

### Quote offered with promotion/credit – nothing left to pay

- EUR 1000 credits available.
- EUR 109 credits applied to the EUR 109 quote.
- EUR 0 left to pay
- EUR 891 credits left

```json
{
    "status": "offered",
    "price": {
        "currency": "EUR",
        "total_price": "0.00",
        "premium": "100.00",
        "ipt": "9.00",
        "total_price_without_discount": "109.00",
        "discount_amount": "109.00",
        "discounts": [
            {
                "type": "credits",
                "discount_reason": "Because we like offered quotes!",
                "discount_amount": "109.00",
                "remaining_credits_amount_after": "891.00",
                "remaining_credits_amount_after_currency": "EUR",
            },
        ],
    },
    "payment_required": false
}
```
