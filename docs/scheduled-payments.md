# Scheduled payments

## Introduction

If on your platform, buyers can pay sellers in multiple instalments it is very
likely that you will use the scheduled payments and the partial payments
features of the API.

Note that these features are completely optional and you can safely ignore if
you don't intend to use it.

This document is about defining a payemnt schedule. Declaring the schedule to
us will allow the seller to recover their payment earlier if the buyer can't
pay one instalment.

Partial payment feature is decribed in [this other section](partial-payments.md)
of the documentation.

## Concepts

A payment schedule is a list of ScheduledPayments or expected payments. Each
represent an amount that the buyer is expected to pay at a given date. The sum
of the expected payments amounts will naturally be equal to the gross amount of
the transaction. ScheduledPayments are always understood in the same currency
as the transaction.

A schedule is provided with the creation of a transaction and cannot be modified.

## API reference

### Modified endpoint

#### `/v1/transactions`

A `scheduled_payments` field is added to `Transaction` objects. This field is a
list of `ScheduledPayment` objects.

### The ScheduledPayment object

attribute | type | flag | description
--------- | ---- | ---- | -----------
date | string(date) | mandatory | Expected due date of this instalment
amount | decimal | mandatory | Expected amount of this instalment (positive in same currency as the transaction)

### Validation

Field `scheduled_payments` is optional. If you provide it, fields
`gross_amount`, `issue_date` and `due_date` of the transaction are mandatory.

Coherence of the schedule is verified and first scheduled payment date shall
be on or after `issue_date` and last scheduled payment date shall be on `due_date`.
Sum of scheduled payment amounts must be equal to `gross_amount`.

Because of the coherence restriction, if a transaction has been created with
a `scheduled_payments`, then `gross_amount`, `issue_date` and `due_date` cannot
be later modified. You'll have to delete this transaction and create a new one
with the correct schedule.

### Create a transaction with a payment schedule

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
    "scheduled_payments": [
      {"date": "2020-02-13", "amount": 300.0},
      {"date": "2020-03-13", "amount": 300.0},
      {"date": "2020-04-13", "amount": 300.0},
      {"date": "2020-05-13", "amount": 300.0}
    ],
}'
```
