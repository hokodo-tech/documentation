# Webhooks

## Introduction

_Note: this is also covered in the [existing public API](https://hokodo.docs.apiary.io/#introduction/webhooks), but this document gives more details and examples._

Hokodo can send webhook events to notify your application when an event related to a quote or active policy happens. This is useful for example for events that are triggered by a user action outside of your application, or events that are triggered at a certain point in time on our side. For example, but not limited to:
* user accepts a quote and proceeds to buy a policy on our website,
* a new quote is offered,
* an invoice is marked as settled,
* ...

It allows you to avoid polling the quote view endpoint and be notified instead. It is entirely optional. If you wish to use this feature, please let us know at platform-support@hokodo.co and we will be happy to configure your account.

## Concepts

Currently, the webhooks only cover changes related to the `Transaction` objects (including changes to associated `Quote` and `Policy`).

Your callback endpoint will receive the [`Transaction` object](https://hokodo.docs.apiary.io/#reference/transactions/transactions-collection) (see below) representation, in expanded form to include the latest quote or policy attached to the transaction. Hokodo calls your endpoint with the content of the transaction, as you would get if you were polling `GET /v1/transactions/:transaction-id/?expand=quote,policy`.

What you need to build on your side is an endpoint ready to receive our requests, with that expanded `Transaction` in the body. Provide us with:
- the URL of that endpoint
- (optional) an authentication string our webhook will send in the `Authorization` header

### Notes
1. Your endpoint must be using HTTPS.
2. In production, our API will verify the validity of the HTTPS certificate. In sandbox, you can use a self-signed certitificate.
3. Our API will send a POST request, with the content of the `Transaction` in the POST request body as JSON.
4. Our API expects a `2xx` status code from your endpoint for a succesful response.
4. Our API will retry up to 3 times if it can't connect sucessfully to your endpoint, with an exponential backoff between each request. It will wait 0, 2, and 4 seconds respectively before the 1st, 2nd and 3rd retry.

### Events of interest
If a quote has been offered and the customer has purchased an insurance policy, both the `quote` and `policy` field in the transaction JSON will be filled and expanded. What will probably be of most interest to you then is looking at the `quote` and `policy` fields in the webhook request body.
- `"policy": null, "quote": null` means no quote and no policy,
- `"policy": null, "quote": {...}` means there is a quote and no policy has been purchased, you can check `"status"` in the quote sub-object to know if it is offered, declined or expired.
- `"policy": {...}, "quote": {...}` means a policy has been purchased.

## Sample content

### Active insurance policy
- `policy` is filled
- `quote.status` is `"accepted"`

```json
{
    "url": "https://api-sandbox.hokodo.co/v1/transactions/trns-5DpFHDgVpv87wJFa9dXyvW",
    "id": "trns-5DpFHDgVpv87wJFa9dXyvW",
    "owner": "user-MzPbafH78Byb65U7PshRpF",
    "debtor": "co-Tv5pezgn7VAYEbLXfexbS2",
    "debtor_name": "Test debtor",
    "debtor_country": "GB",
    "debtor_regnum": "",
    "debtor_address": "",
    "creditor": "co-WUaZHXbdsbAvxpYbBPhYh8",
    "creditor_name": "Test creditor",
    "creditor_country": "GB",
    "creditor_regnum": "",
    "creditor_address": "",
    "status": "pending",
    "pay_method": "unknown",
    "net_amount": "1234.00",
    "gross_amount": null,
    "currency": "GBP",
    "issue_date": "2019-10-16",
    "due_date": "2019-12-16",
    "paid_date": null,
    "number": "",
    "items": [],
    "source": null,
    "unique_id": "",
    "policy": {
        "url": "https://api-sandbox.hokodo.co/v1/policies/pol-4wvXe9URiecFYiLxuXkkxf",
        "id": "pol-4wvXe9URiecFYiLxuXkkxf",
        "number": "T-4ZW5-611D",
        "status": "active",
        "created": "2019-11-07T11:17:40.451738Z",
        "documents": [
            {
                "category": "policy",
                "url": "https://removed.for.this.example"
            }
        ],
        "collection": null,
        "loss_payee": "Not applicable"
    },
    "quote": {
        "url": "https://api-sandbox.hokodo.co/v1/quotes/quo-jaetYa8f9TmFDBYsb33KAT",
        "id": "quo-jaetYa8f9TmFDBYsb33KAT",
        "client": {
            "url": "https://api-sandbox.hokodo.co/v1/companies/co-WUaZHXbdsbAvxpYbBPhYh8",
            "id": "co-WUaZHXbdsbAvxpYbBPhYh8",
            "country": "GB",
            "name": "Test creditor Limited",
            "address": "123 Main Street, City, County, Region, Postcode",
            "city": "Test city",
            "postcode": "Test postcode",
            "legal_form": "Private limited with share capital",
            "sectors": [
                {
                    "system": "SIC2007",
                    "code": "47710"
                }
            ],
            "creation_date": "1995-07-12",
            "identifiers": [
                {
                    "idtype": "reg_number",
                    "country": "GB",
                    "value": "01234567"
                }
            ],
            "email": "",
            "phone": "",
            "status": "Active",
            "accounts_type": "Total exemption full"
        },
        "status": "accepted",
        "proposed_quote": null,
        "rejection_reason": null,
        "created": "2019-11-07T11:17:38.166454Z",
        "valid_until": "2019-11-07T13:17:38.158274Z",
        "price": {
            "currency": "GBP",
            "total_price": "20.45",
            "premium": "18.26",
            "ipt": "2.19"
        },
        "insured": {
            "currency": "GBP",
            "insured_amount": "1110.60"
        },
        "insured_pc_net": 90.0,
        "insured_pc_gross": null,
        "referral_url": null
    },
    "estimated_quote_price": null
}
```

### Quote, but no insurance policy purchased

- `policy` is `null`
- `quote.status` is `"offered"`

```json
{
    "url": "https://api-sandbox.hokodo.co/v1/transactions/trns-5DpFHDgVpv87wJFa9dXyvW",
    "id": "trns-5DpFHDgVpv87wJFa9dXyvW",
    "owner": "user-MzPbafH78Byb65U7PshRpF",
    "debtor": "co-Tv5pezgn7VAYEbLXfexbS2",
    "debtor_name": "Test debtor",
    "debtor_country": "GB",
    "debtor_regnum": "",
    "debtor_address": "",
    "creditor": "co-WUaZHXbdsbAvxpYbBPhYh8",
    "creditor_name": "Test creditor",
    "creditor_country": "GB",
    "creditor_regnum": "",
    "creditor_address": "",
    "status": "pending",
    "pay_method": "unknown",
    "net_amount": "1234.00",
    "gross_amount": null,
    "currency": "GBP",
    "issue_date": "2019-10-16",
    "due_date": "2019-12-16",
    "paid_date": null,
    "number": "",
    "items": [],
    "source": null,
    "unique_id": "",
    "policy": null,
    "quote": {
        "url": "https://api-sandbox.hokodo.co/v1/quotes/quo-jaetYa8f9TmFDBYsb33KAT",
        "id": "quo-jaetYa8f9TmFDBYsb33KAT",
        "client": {
            "url": "https://api-sandbox.hokodo.co/v1/companies/co-WUaZHXbdsbAvxpYbBPhYh8",
            "id": "co-WUaZHXbdsbAvxpYbBPhYh8",
            "country": "GB",
            "name": "Test creditor Limited",
            "address": "123 Main Street, City, County, Region, Postcode",
            "city": "Test city",
            "postcode": "Test postcode",
            "legal_form": "Private limited with share capital",
            "sectors": [
                {
                    "system": "SIC2007",
                    "code": "47710"
                }
            ],
            "creation_date": "1995-07-12",
            "identifiers": [
                {
                    "idtype": "reg_number",
                    "country": "GB",
                    "value": "01234567"
                }
            ],
            "email": "",
            "phone": "",
            "status": "Active",
            "accounts_type": "Total exemption full"
        },
        "status": "offered",
        "proposed_quote": null,
        "rejection_reason": null,
        "created": "2019-11-07T11:17:38.166454Z",
        "valid_until": "2019-11-07T13:17:38.158274Z",
        "price": {
            "currency": "GBP",
            "total_price": "20.45",
            "premium": "18.26",
            "ipt": "2.19"
        },
        "insured": {
            "currency": "GBP",
            "insured_amount": "1110.60"
        },
        "insured_pc_net": 90.0,
        "insured_pc_gross": null,
        "referral_url": null
    },
    "estimated_quote_price": null
}
```


### No valid quote, nor insurance policy

- `policy` is `null`
- `quote.status` is `null`

```json
{
    "url": "https://api-sandbox.hokodo.co/v1/transactions/trns-5DpFHDgVpv87wJFa9dXyvW",
    "id": "trns-5DpFHDgVpv87wJFa9dXyvW",
    "owner": "user-MzPbafH78Byb65U7PshRpF",
    "debtor": "co-Tv5pezgn7VAYEbLXfexbS2",
    "debtor_name": "Test debtor",
    "debtor_country": "GB",
    "debtor_regnum": "",
    "debtor_address": "",
    "creditor": "co-WUaZHXbdsbAvxpYbBPhYh8",
    "creditor_name": "Test creditor",
    "creditor_country": "GB",
    "creditor_regnum": "",
    "creditor_address": "",
    "status": "pending",
    "pay_method": "unknown",
    "net_amount": "1234.00",
    "gross_amount": null,
    "currency": "GBP",
    "issue_date": "2019-10-16",
    "due_date": "2019-12-16",
    "paid_date": null,
    "number": "",
    "items": [],
    "source": null,
    "unique_id": "",
    "policy": null,
    "quote": null,
    "estimated_quote_price": null
}
```
