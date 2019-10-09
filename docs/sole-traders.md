# Sole traders

## Introduction
By default, only certain types of companies can be insured by Hokodo. Sole traders in the United Kingdom are not registered and found in Companies House's database, and cannot be found in our company search API. They can however be used in the API as creditors, and insured by Hokodo. If a significant portion of your users are sole traders, we can enable the function for you.

## Concepts

Sole traders in the United Kingdom are not registered and found in Companies House's database. They can be used in the API as creditors, and insured by Hokodo. Get in touch with us to enable that function for you.

You will be able to create `SoleTrader` objects, which are an extension of the `Company` objects. These can be used as creditors in transactions.

## API reference
### Endpoints

```
   POST /v1/soletraders
    GET /v1/soletraders
    GET /v1/soletraders/:id
  PATCH /v1/soletraders/:id
```

### The SoleTrader object

attribute | type | flags | description
--------- | ---- | ----- | ------------
id | string(uuid) | read-only | Unique company identifier for the object. To be used where a `Company` identifier is required, such as creditor in a transaction.
soletrader_id | string(uuid) | read-only | Unique identifier for the object. To be used where a `SoleTrader` identifier is required.
owner | string(uuid) | **required** | Unique identifier of the user associated to the sole trader.
unique_id | string | **required** | Unique identifier of the sole trader
vat_number | string | optional | VAT number of the sole trader if available.
|
trading_name | string | **required** | Company's name
trading_address | string |  | Company's full address
trading_address_city | string |  | Company's city
trading_address_postcode | string |  | Company's postcode
trading_address_country | string | |  Company's country
|
proprietor_name | string | **required** | Proprietor's name
proprietor_address_line1 | string |  | Proprietor's address
proprietor_address_line2 | string |  | Proprietor's address
proprietor_address_line3 | string |  | Proprietor's address
proprietor_address_city | string |  | Proprietor's city
proprietor_address_postcode | string |  | Proprietor's postcode
proprietor_address_country | string | **required** |  Proprietor's country

**Sample**

```json
{
    "id": "co-YuNEQ4nqA6Yto9QQUBnopW",
    "owner": "user-sVr7a58Smbfqbo8cVpjEV6",
    "soletrader_id": "st-eE4un8TnDNjx9Uac2DVa6N",
    "trading_name": "The Dogfather 21",
    "trading_address": "123 Commercial St, Shoreditch",
    "trading_address_city": "London",
    "trading_address_country": "GB",
    "proprietor_name": "John John",
    "proprietor_address_line1": "Flat 5",
    "proprietor_address_line2": "10 Residential Street",
    "proprietor_address_line3": "Hackney",
    "proprietor_address_city": "London",
    "proprietor_address_postcode": "EC2 8AA",
    "proprietor_address_country": "GB",
    "vat_number": "15141312",
    "unique_id": "0d201ab1-46df-4274-8c90-256eaa66c135"
}
```

### Create a sole trader
To create a sole trader, you create a `SoleTrader` object.

```
curl --url https://api-sandbox.hokodo.co/v1/soletraders/ \
  --header 'authorization: Token your_api_key_here' \
  --header 'content-type: application/json' \
  --data '{
    "owner": "user-KZcuB2oT6BPTjmQJiHm7ha",
    "trading_name": "The Dogfather ",
    "trading_address": "123 Commercial St, Shoreditch",
    "trading_address_city": "London",
    "trading_address_postcode": "EC2 8AA",
    "trading_address_country": "GB",
    "proprietor_name": "John John",
    "proprietor_address_line1": "Flat 5",
    "proprietor_address_line2": "10 Residential Street",
    "proprietor_address_line3": "Hackney",
    "proprietor_address_city": "London",
    "proprietor_address_postcode": "EC2 8AA",
    "proprietor_address_country": "GB",
    "vat_number": "15141312",
    "unique_id": "0d201ab1-46df-4274-8c90-256eaa66c135"
}'
```
