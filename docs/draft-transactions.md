# Draft transactions

## Introduction

We have introduced a new state for transactions: `draft`. This can be used for transactions that the user has not yet commited to, eg. they put something in their cart but did not go through with the checkout.

## Concepts

A draft transaction can be updated, like any other uninsured transaction. Its status can only go from `draft` to `pending` though, and a `pending` transaction cannot go back to the `draft` state.

An insured transaction cannot be updated. In particular an insured draft transaction cannot be updated via the transaction update API endpoint. The only change you can make to an insured draft transaction is to change its status to pending. To do so you will need to use a new API endpoint.

## API reference

### New endpoints

```
   PUT /v1/transactions/:transaction-id/confirm
```

### Modified endpoints

#### `/v1/transactions`

- the (optional) `status` field now also accepts the value `draft`.

### Confirm a draft transaction

This will change the `status` of a transaction from `draft` to `pending`, and works even if the transaction has already been insured.

```
curl \
  -X PUT
  --url https://api-sandbox.hokodo.co/v1/transactions/trns-qvUNp84WXhVGxVYYeLrtT9/confirm \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
```
