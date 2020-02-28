# Partial payments

## Introduction

If on your platform, buyers can pay sellers in multiple instalments it is very
likely that you will use the scheduled payments and the partial payments
features of the API.

Note that these features are completely optional and you can safely ignore if
you don't intend to use it.

This document is about recording in the API partial payments made to a
transaction. Declaring those repayments will allow us to insure more of your
user invoices.

Payment schedule feature is decribed in [this other section](scheduled-payments.md)
of the documentation.


## Concepts

A Payment is an amount of money sent by the buyer to the seller in repayment of
a good or a service at a given date and covering all or part of the invoice
gross amount.

The amount of a payment always mean gross amount, tax included.

Initial payments can be recorded when creating a Transaction. Payments can be
added later through a dedicated endpoint but cannot be modified are deleted.

This new interface co-exists with the existing "mark transaction as paid"
endpoint, for platforms/customers that don't need all the bells and whistles
described here, but interfaces shouldn't be mixed.

## API reference

### New endpoints

```
   GET /transactions/:transaction_id/payments
  POST /transactions/:transaction_id/payments
   GET /transactions/:transaction_id/payments/:payment-id
```

### Modified endpoints

#### `/v1/transactions`

A `payments` field is added to `Transaction` objects. This field is a list of
`Payment` objects.

### The Payment object

attribute | type | flag | description
--------- | ---- | ---- | -----------
date | string(date) | optional | Date at which the payment was executed or received
amount | decimal | mandatory | Amount of this payment (in same currency as the transaction)

### Validation

Field `payments` is optional in a transaction. It can only be provided when
creating the transaction.

If date is not provided for a payment, it will default to now.


### Create a transaction with an initial repayment

```
curl --request POST \
  --url https://api-sandbox.hokodo.co/v1/transactions/ \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
	"owner": "org-EyHndGeUCC8C7sDUr8ENpX",
    "gross_amount": "1200",
	"currency": "GBP",
	"issue_date": "2020-02-13",
	"due_date": "2020-05-13",
	"debtor_name": "Test Buyer",
	"debtor_country": "GB",
	"creditor_name": "Test Seller",
	"creditor_country": "GB",
    "payments": [
      {"date": "2020-02-13", "amount": 300.0}
    ],
}'
```

### Create a payment made today

The date is optional and we default to now:

```
curl --request POST \
  --url https://api-sandbox.hokodo.co/v1/transactions/trns-FAnp2kTp5VyUMZNKvjLCJk/payments/ \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
    "date": "2020-03-13",
    "amount": 300.0
}'
```

### List payments made to a transaction

You can simply obtain the transaction or access:

```
curl --request GET \
  --url https://api-sandbox.hokodo.co/v1/transactions/trns-FAnp2kTp5VyUMZNKvjLCJk/payments/ \
  --header 'authorization: Token your_api_key_here'
```

