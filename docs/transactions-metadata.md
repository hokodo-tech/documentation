# Transactions metadata

## Introduction

To ease integration and to facilitate referencing the data you are sending us, transactions can hold extra metadata. This metadata is exposed in the API and you can use it however you see fit.

The only restriction on metadata is that it should be a valid JSON. Metadata can be (almost) arbitrarily large. If you end up having issues with the size, please contact Hokodo and we will try helping with your use case.

### A note on unique_id

On top of the metadata, transactions also have a `unique_id` field. You can use this field to store the identifier corresponding to the invoice on *your* platform.
We suggest you use the `unique_id` field as much as possible, as it is more usable on our end than arbitrary metadata. For example, we offer filtering on `unique_id` in multiple endpoints: [TODO: LINK]

## API reference

### Creating a transaction with metadata

```
curl \
  --url https://api-sandbox.hokodo.co/v1/transactions/ \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
    "owner": "org-EyHndGeUCC8C7sDUr8ENpX",
    "metadata": {
        "checkout_id": 1234,
        "retailer_info": {
            "id": 456,
            "name": "Sarah Connor"
        },
        "order_ids": ["abc-123", "qwe-456"]
    }
}'
```
