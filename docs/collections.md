# Collections and claims

## Introduction
If an insured invoice doesn't get paid past a certain date, the user can hand it over to Hokodo. We will then try to collect, and if that is unsuccessful pay a claim.

## Concepts
To hand over an unpaid invoice to collections, you create a `Collection` object, attached to a `Policy`. Collections are identified by a unique, random ID.

The collection object will be pre-filled with known information about the creditor and the debtor. That information can still be modified by the customer, for example to indicate the address of the proper branch of the debtor company to contact, or the right contact person.

Some documents must be attached to the collection request, for example a copy of the original invoice. To do so, create a `CollectionDocument` object.

Once a `Collection` object is created and attached to a policy, it cannot be modified to be attached to a different policy.

Once a collection request has been sent, the object cannot be modified anymore (this rule may be relaxed in the future) and documents attached to it cannot be deleted through the API.

## API reference

### Endpoints

```
  POST /v1/collections
   GET /v1/collections
   GET /v1/collections/:id
 PATCH /v1/collections/:id
   PUT /v1/collections/:id/send

  POST /v1/collectiondocuments
   GET /v1/collectiondocuments
   GET /v1/collectiondocuments/?policy_id=:policy-id
   GET /v1/collectiondocuments/:id
DELETE /v1/collectiondocuments/:id
```

### The Collection object

attribute | type | flags | description
--------- | ---- | ----- | ------------
id | string(uuid) | read-only | Unique identifier for the object.
policy | string(uuid) | **required** | Policy the collection is attached to.
|
sent | string(datetime) | read-only | Timestamp at which the collection request has been sent (set by PUT .../:id/send)
created | string(datetime) | read-only | Created time.
modified | string(datetime) | read-only | Last modified time.
|
confirm_correct | boolean |  | Customer has confirmed the information is correct
collections_terms_acceptance | boolean |  | Customer has accepted the collections terms and conditions.
collect_fees_from_debtor | boolean |  | Should the collection fees be collected from debtor?
|
confirmed_correct | string(datetime) | read-only | Timestamp of `confirm_correct` true
accepted_collections_terms | string(datetime) |read-only  | Timestamp of `collections_terms_acceptance` true
|
creditor_contact_name | string |  | Creditor contact name (defaults to the user's name)
creditor_contact_email | string(email) |  | Creditor contact email address (defaults to the user's email address)
creditor_contact_phone | string |  | Creditor contact phone number (defaults to the user's phone if available)
debtor_address | string |  | Debtor address
debtor_postcode | string |  | Debtor postal/zip code
debtor_city | string |  | Debtor city
debtor_country | string |  | Debtor country (ISO 3166-1 2 letter code)
debtor_contact_name | string |  | Debtor contact name
debtor_contact_email | string(email) |  | Debtor contact email address
debtor_contact_phone | string |  | Debtor contact phone number
|
bank_account_name | string |  | Name on the bank account
bank_account_sortcode | string |  | Bank account sortcode
bank_account_number | string |  | Bank account number
bank_account_email | string(email) |  | Email address of person checking the bank account
|
||||Optional fields
debtor_website | string |  | Debtor website
actions_taken | string |  | Actions already taken by the creditor
partial_payment | string(decimal) |  | Partial payment received (defaults to 0)
disputed | boolean |  | Is the invoice disputed by the debtor? (defaults to false)
other_invoices | boolean |  | Are there other outstanding invoices with this debtor? (defaults to false)
reasons | string |  | Non-payment reasons if known
comments | string |  | Other comments and instructions

**Sample**

```json
{
    "id": "coll-TWyj6XFLYfCnw95KgBhHDf",
    "policy": "pol-WwX8y7hKWyje2ttm6nAbvU",
    "created": "2019-06-18T10:56:05.933635Z",
    "modified": "2019-06-26T08:42:34.254546Z",
    "sent": "2019-06-26T08:42:33.069006Z",
    "creditor_contact_name": "This Is Me",
    "creditor_contact_email": "realuser@usercompany.com",
    "creditor_contact_phone": "+441234567890",
    "debtor_address": "1 Bad Payer Street, London, E2 8AA",
    "debtor_postcode": "E2 8AA",
    "debtor_city": "London",
    "debtor_country": "GB",
    "debtor_contact_name": "Mean Buyer",
    "debtor_contact_email": "mean@buyer.com",
    "debtor_contact_phone": "+440987654321",
    "debtor_website": "",
    "actions_taken": "",
    "partial_payment": "0.00",
    "disputed": false,
    "other_invoices": false,
    "reasons": "",
    "comments": "",
    "bank_account_name": "User Company",
    "bank_account_sortcode": "12-10-24",
    "bank_account_number": "80804040",
    "bank_account_email": "accounts@usercompany.com",
    "confirmed_correct": "2019-06-26T08:42:32.388611Z",
    "accepted_collections_terms": "2019-06-26T08:42:32.388621Z",
    "collect_fees_from_debtor": true
}
```

### The CollectionDocument object

attribute | type | description
--------- | ---- | ------------
id | string(uuid) | Unique identifier for the object. (read-only)
policy | string(uuid) | Policy the collection document is attached to.
||
type | string | Type of document. Possible values are: `invoice`, `statement` (statement of account), `proof_order` (proof of order, contract of sale), `proof_delivery` (proof and terms of delivery), `shipping` (shipping documents), `correspondence` (recent correspondence), `other` (other types of documents)
description | string | Description of the (type of) document
file | string(uri) | Link to the document (the link is not a permanent link, but expires)

**Sample**

```json
{
    "id": "cdoc-afipG98cKcQL5VRjVfPP2S",
    "policy": "pol-WwX8y7hKWyje2ttm6nAbvU",
    "type": "invoice",
    "description": "",
    "file": "https://hokodo-sandbox-clientdocuments.s3.amazonaws.com/media/user_uploaded_documents/..."
}
```

### Create a collection request
To create a collection request, you create a `Collection` object attached to the `Policy` of the unpaid invoice.

```
curl --url https://api-sandbox.hokodo.co/v1/collections/ \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
	"policy": "pol-WwX8y7hKWyje2ttm6nAbvU"
}'
```

### Update a collection request
Update a collection request with additional details.

```
curl --request PATCH \
  --url https://api-sandbox.hokodo.co/v1/collections/coll-TWyj6XFLYfCnw95KgBhHDf/ \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
    "creditor_contact_phone": "+441234567890",
    "debtor_contact_name": "Mean Buyer",
    "debtor_contact_email": "mean@buyer.com",
    "debtor_contact_phone": "+440987654321",
	"confirm_correct": true,
	"collections_terms_acceptance": true,
    "collect_fees_from_debtor": false
}'
```

### Attach a collection document
To attach a collection document, you create a `CollectionDocument` object attached to the `Policy` of the unpaid invoice.

**This is one of the rare API endpoints that does not take JSON input, but multipart form data.**

```
curl --request POST \
  --url https://api-sandbox.hokodo.co/v1/collectiondocuments/ \
  --header 'authorization: Token your_api_key_here' \
  --form policy=pol-WwX8y7hKWyje2ttm6nAbvU \
  --form type=invoice \
  --form file=@/path/to/file
```

which results in a request of the form

```
POST /v1/collectiondocuments/ HTTP/1.1
Host: api-sandbox.hokodo.co
Content-Type: multipart/form-data; boundary=X-CLIENT-BOUNDARY
Authorization: Token your_api_key_here
Accept: */*
Content-Length: 35475

(33.5 KB hidden)
```

### Send a collection request
Once all the information has been filled in correctly, calling this hands over the collection request to our collections partner.

```
curl --request PUT \
  --url https://api-sandbox.hokodo.co/v1/collections/coll-TWyj6XFLYfCnw95KgBhHDf/send/ \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json'
```

### Notes and possible causes of error
A `Policy` has to be in the state `late_can_handover` for a collection request to be possible. The policy reaches that state automatically depending on the due date and the terms of the insurance (how soon can a customer hand over the invoice after the due date). Trying to start or modify a collection request in another state will result in an HTTP 409 error in the response.

When a policy is insured, it will go in the following states:
- `active`: the invoice has been insured, and the invoice is not due yet.
- `late`: the invoice is insured and due, the customer or the platform hasn't indicated it has been paid yet.
- `paid`: the customer has indicated the invoice has been paid, the policy is now inactive.
- `late_can_handover`: the invoice is insured and due, and has reached the date the customer can hand it over for collections and claims. From this point in time the customer has a limited time (typically 30 days) to hand it over, and should be reminded either by our emails or your notifications to either hand it over or mark the invoice as paid.
- `lapsed`: the policy has lapsed with the customer neither handing it over to collections nor marking it as paid.
- `under_collection`: the invoice has been handed over by the customer for collections and claims.

There are a number of additional states after a policy goes through the collections and claims process, that can be useful to display the current status of collections to the users.

#### Collection
- `under_collection`: Under collection
- `pay_installments`: Collection by installments
- `not_collectable`: Not collectable
- `not_collectable_dispute`: Not collectable due to dispute
- `under_collection_installments`: Under collection by installments
- `dispute_pursual`: Dispute pursual
- `under_collection_dispute_solved`: Under collection, dispute has been solved

#### Claim
- `claim_submitted`: Claim submitted
- `claim_approved`: Claim approved
- `claim_rejected`: Claim rejected
- `recovery_installments`: Recovery by installments
- `recovery_underway`: Recovery underway
- `recovery_complete`: Recovery complete
- `recovery_rejected`: Recovery rejected
