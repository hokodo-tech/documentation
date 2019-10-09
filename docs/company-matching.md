# Matching companies

## Introduction

When a user fills in details of an invoice, or when importing invoices from an external accounting/invoicing provider, often enough only the **name** of the debtor is filled in. To provide accurate quotes, we need to match that debtor name to a precise legal entity.

The API provides a number of ways to do that, depending on your use case. The user could be typing a company name every time, or creating companies and typing the name only once, or importing that data from an external accounting package. Here are some options:
- When a user types in a company name, always offer a choice of companies to match it, for example a drop down from the result of our company search endpoint, and pass that company API identifier with the invoice.
- When a user creates a company and types the name, offer a choice of companies to match it, for example a drop down from the result of our company search endpoint, save that company API identifier and pass it with every invoice later.
- Pass the information you have to us (company name, country, address, registration number, etc.) when creating the invoice in the API, without trying to match the company name to the actual company. When you request a quote, we will try to provide it with the best match we found. You can then redirect the user to our quote portal (which can be co-branded). The user will be asked to confirm the match before proceeding to purchase insurance. In this case, we take care of the company matching UX.
- In the case a user has to create companies and type their name, or you are importing data from such a system, essentially when you know the name is consistent across invoices, ie "Test customer" will always designate the same company, we provide an endpoint to store this match on our side so you don't have to take care of it. Create the invoices in the API with the information you have (company name, country, address, registration number, etc.). Before offering an insurance quote, make it visible to the user that they need to match the name to an actual company. Once they do that, you can call the match company endpoint described below.

The rest of this document describes that last option.

## Concepts

Let's start with an example. The user has to create customers before assigning them to their invoices. To do that, they have to enter their name and country. For example the user creates "Test customer" in the United Kingdom. Every time the user wants to create an invoice to "Test customer", they pick them from a list of options.

Before offering an insurance quote, make it visible to the user that they need to match the name to an actual company. Once they do that, you can inform the API:
1. provide the user with a list of choices of possible companies matching the free text they typed,
2. create a `CompanyMatch` object that saves this choice.

All the transactions of this user that had "Test customer" for debtor name will be automatically updated to point to the correct legal entity, for example "Test Customer LTD". Once the user imports new invoices with the same debtor name, the match is made automatically as well.

If one of these transactions was already insured, we do not modify it but trigger a warning, to be handled by our customer support.

## API reference

### Endpoints

```
   PUT /v1/transactions/:transaction-id/match-debtor/
   GET /v1/companymatches (TBD)
```

### The CompanyMatch object

attribute | type | flags | description
--------- | ---- | ----- | ------------
company | string(uuid) | **required** | Unique identifier of the company in Hokodo's API
organisation | string(uuid) | read-only | Unique identifier of the organisation in Hokodo's API
name | string | read-only | Free text entered and matched by the user to the `company`

### Create a match
To create a match, you associate the `debtor` of a `transaction` to a company identified by its Hokodo API identifier. That identifier can be obtained through the company search endpoint.

#### Step 1: create an invoice with just the debtor name

```
curl \
  --url https://api-sandbox.hokodo.co/v1/transactions/ \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
    "owner": "org-EyHndGeUCC8C7sDUr8ENpX",
    "net_amount": "1000",
    "gross_amount": "1200",
    "currency": "GBP",
    "issue_date": "2019-10-01",
    "due_date": "2019-12-01",
    "number": "1910-004",
    "debtor_name": "Test customer",
    "debtor_country": "GB",
    "creditor": "co-validcreditor000000000",
    "unique_id": "3809a104-ea52-412d-bf37-b1ef9a2ea279"
}'
```

#### Step 2: search company to provide matches

```
curl \
  --url https://api-sandbox.hokodo.co/v1/companies/search/ \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
    "country": "GB",
    "name": "Test customer"
}'
```

#### Step 3: save the match

```
curl \
  --request PUT \
  --url https://api-sandbox.hokodo.co/v1/transactions/trns-SXDpa8sohRy5ZdZShGCqLJ/match_debtor/ \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
    "debtor": "co-CWzEFpHwrmxx9NWsXXL7u8"
}'
```
