# Organisations

## Introduction
We have _beta_ support for organisations in the API, for platforms where this concept is relevant.

If you have no support for organisations with multiple users or users with multiple organisations on your platform, this change is transparent and backwards-compatible, you can continue using the API in the same way. If you do have the concept of users and organisations in your platform, read on.

- **Single-user platform** (e.g. lead generation, no sign-up required, etc.): continue using single user API calls
- **Organisations enabled platform** (multiple users per organisation, or multiple organisations per user): use the new organisations-enabled API calls

## Concepts
Instead of a user being the `owner` of a transaction, the organisation is the `owner`, and the users are members of the organisations. An organisation can have multiple users, with different roles and permissions. A user can be part of multiple organisations.

In general, that means users of the same organisation share the same view of their invoices, the same company matching, etc.
Multiple users have access to all the invoices of an organisation. For example Jane, Bob, and Alice are members of the organisation "Test Ltd" from the "Cash flow for SMEs" platform.

When an invoice is synced with the Hokodo API by the "Cash flow for SMEs" platform, when a quote is made, when a customer name is matched to a company, etc. all this applies at the organisation level, not at the user level. It is shared between all the members of the organisation.

To handle this, the API includes the concept of `Organisations`. The steps to on-board users via the API become:
1. create `Organisation`
2. create `User` A attached to that `Organisation`
3. create `User` B attached to that `Organisation`
4. create `User` C attached to that `Organisation`
5. ...
6. create `Transaction` (invoice) attached to that `Organisation`
7. match company from that invoice
8. offer a quote
9. ...
10. all users A, B, C... from the organisation have access to their insurance on Hokodo's customer portal

## API reference

### New endpoints

```
  POST /v1/organisations/
   GET /v1/organisations/
   GET /v1/organisations/:org-id
 PATCH /v1/organisations/:org-id
DELETE /v1/organisations/:org-id

  POST /v1/organisations/:org-id/users/
   GET /v1/organisations/:org-id/users/
   GET /v1/organisations/:org-id/users/:user-id
 PATCH /v1/organisations/:org-id/users/:user-id
DELETE /v1/organisations/:org-id/users/:user-id

  POST /v1/users/:user-id/organisations/
   GET /v1/users/:user-id/organisations/
   GET /v1/users/:user-id/organisations/:user-id
 PATCH /v1/users/:user-id/organisations/:user-id
DELETE /v1/users/:user-id/organisations/:user-id
```

### Modified endpoints

#### `/v1/users`
- less information required, as most of it moves to `/organisations/`
- you can provide the identifier(s) of the associated organisation(s) at creation of the `user`

#### `/v1/transactions`
- the `owner` is now the `organisation` not a `user`, attempting to attach an invoice to a `user` will result in a 400 error.
- (TBD) filter list of `transactions` by `organisation` id

### The Organisation object

attribute | type | flags | description
--------- | ---- | ----- | ------------
id | string(uuid) | read-only | Unique identifier for the object.
created | string(datetime) | read-only | created time
modified | string(datetime) | read-only | last modified time
||
unique_id | string | **required** | Unique identifier of the organisation on your platform.
||
registered | string(datetime) | **required**  | When the organisation registered on your platform (ISO 8601)
plan_type | string | optional | Type of plan (free, paying, premium)
||
company | string(uuid) | optional | Hokodo unique identifier of the company
company_name | string | optional | Company name
company_regnum | string | optional | Company registration number
company_address | string | optional | Company address
company_postcode | string | optional | Company postal/zip code
company_city | string | optional | Company city
company_country | string | optional | Company country (ISO 3166-1 2 letter code)

**Sample**
```json
{
  "id": "org-9RxFsXTgnWqwjK4apxBAn8",
  "unique_id": "c105b862-f1ba-4197-9d97-57db63196b00",
  "registered": "2017-06-01T14:37:12Z",
  "plan_type": "free",
  "company": null,
  "company_name": "Test Ltd",
  "company_country": "GB",
  "company_regnum": "",
  "company_address": "",
  "company_postcode": "",
}
```

### Create an organisation
To create an organisation, you create an `Organisation` object.

```
curl \
  --url https://api-sandbox.hokodo.co/v1/organisations/ \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
    "unique_id": "c105b862-f1ba-4197-9d97-57db63196b00",
    "registered": "2017-06-01T14:37:12Z",
    "company_name": "Test Ltd",
    "company_country": "GB",
}'
```

### Create a user
To create a user, you create a `User` object associated to at least one `Organisation`.

```
curl \
  --url https://api-sandbox.hokodo.co/v1/users/ \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
    "unique_id": "b43983b6-099d-4933-bb75-c0920e92334c",
    "email": "jane@example.com",
    "name": "Jane",
    "organisations": [
      {"id":"org-9RxFsXTgnWqwjK4apxBAn8", "role":"admin"},
    ]
}'
```

Possible roles for a user are: `admin`, `member`, or `read-only`.

- A `read-only` user can only see resources (transactions, quotes and policies) attached to the organisation.
- A `member` user has the rights of a `read-only` user and can also edit transactions, get quotes and pay for policies.
- An `admin` user has the rights of a `member` user and can also add members, remove members, or change role of members of the organisation.

### Changing role of a user in an organisation

```
curl \
  --request PATCH \
  --url http://api-sandbox.hokodo.co/v1/users/user-nNqgg5AFymQMumaU7EdrLa/organisations/org-9RxFsXTgnWqwjK4apxBAn8 \
  --header 'authorization: Token your_api_key_here-J3vTvfA' \
  --header 'content-type: application/json' \
  --data '{
    "role": "read-only"
}'
```

or conversely using `PATCH /organisations/org-9RxFsXTgnWqwjK4apxBAn8/users/user-nNqgg5AFymQMumaU7EdrLa`.

### Removing a user from an organisation

```
curl \
  -X "DELETE"
  --url https://api-sandbox.hokodo.co/v1/organisations/org-9RxFsXTgnWqwjK4apxBAn8/users/user-nNqgg5AFymQMumaU7EdrLa \
  --header 'authorization: Token your_api_key_here'
```

or conversely using `DELETE /users/user-nNqgg5AFymQMumaU7EdrLa/organisations/org-9RxFsXTgnWqwjK4apxBAn8`.
