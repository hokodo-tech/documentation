# Checking creditor eligibility

## Introduction

Before offering insurance on a creditor's invoices, Hokodo lets you check the eligibility of an organisation as a creditor. This check does not guarantee we will be able to offer insurance on this creditor's invoices, but it can be a good first step to increase conversion down the line.

### On thoroughness and false positives

If this check is negative, we will *not* offer insurance on this creditor's invoices.
This test being positive is only a first step before we offer insurance on an invoice. This check is not thorough and insurance may not be offered down the line. For example, we may find a particular creditor is eligible, but decline insurance on a quote based on the buyer's characteristics.

## API reference

### Endpoints

```
    PUT /v1/organisations/:organisation-id/eligibility-checks/
```

### The OrganisationEligibility object

This endpoint will return the following keys:

attribute | type | description
--------- | ---- | -----------
type | string | The type of eligibility checks, only `as_creditor` exists for now
organisation | string(uuid) | Unique identifier of the organisation in Hokodo's API
company | string(uuid) | Unique identifier of the company in Hokodo's API
status | string | Will return `eligible` if the check passes, `declined` otherwise
rejection_reason | object | Will behave the same way than `rejection_reason` from the request quote API endpoint


### Check OK

```
curl \
  --request PUT \
  --url https://api-sandbox.hokodo.co/v1/organisations/org-123abc/eligibility-checks \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
    "type": "as_creditor"
}'
```

Response:
```
{
  "type": "as_creditor",
  "organisation": "org-123abc",
  "company": "co-qwe456",
  "status": "eligible",
  "rejection_reason": null
}
```

### Check KO

```
curl \
  --request PUT \
  --url https://api-sandbox.hokodo.co/v1/organisations/org-123abc/eligibility-checks \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
    "type": "as_creditor"
}'
```

Response:
```
{
  "type": "as_creditor",
  "organisation": "org-123abc",
  "company": "co-qwe456",
  "status": "declined",
  "rejection_reason": {
    "code": "seller-profile",
    "detail": "At this stage we are not able to insure sellers fitting your profile.",
    "params": {}
  }
}
```

