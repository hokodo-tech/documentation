# Unique ids

## Introduction

To ease integration, some resources have a `unique_id` field, that you can use to store the corresponding id from your platform. These unique ids should be string of less then 255 characters.

### A note on uniqueness

Although this field is called `unique_id`, we do not enforce uniqueness on it. You are in charge of making sure the ids are unique, if you care about it.

### Affected resources

Here is a list of resources providing unique ids:

- Users
- Organisations
- Transactions

### Filtering on unique ids

On top of helping you link objects between your platform and ours, Hokodo's API lets you filter on unique ids in the list endpoint. This filter uses the query string, see the example below for more information.


## API reference

### Endpoints

```
    /v1/users/
    /v1/organisations/
    /v1/transactions/
```

#### Step 1: Creating a user

```
curl --request POST \
  --url https://api-sandbox.hokodo.co/v1/users/ \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
    "unique_id": "my_unique_id",
    "email": "jane@example.com",
    "name": "Jane",
}'
```

#### Step 2: Listing all users

```
curl --request POST \
  --url https://api-sandbox.hokodo.co/v1/users/ \
  --header 'authorization: Token your_api_key_here'
```

#### Step 3: Filtering users based on a `unique_id`

```
curl --request POST \
  --url https://api-sandbox.hokodo.co/v1/users/?unique_id=my_unique_id \
  --header 'authorization: Token your_api_key_here'
```
