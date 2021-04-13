---
title: Cape Crypto API Documentation

# language_tabs: # must be one of https://git.io/vQNgJ
#   - shell

toc_footers:
  - <a href='https://trade.capecrypto.com'>Cape Crypto</a>

includes:
  - errors

search: true

code_clipboard: true
---

# Introduction

Welcome to the Cape Crypto API Documentation! You can use the API to create and cancel orders, view your balance, view market orders and perform all the account actions you can perform from the app.

# Getting started

Before getting going, head to the <strong>Profile screen</strong>. Enable 2FA and then create an API key.

1Save the API key and Secret and do not share it with anyone.

The public endpoints do not require authentication, but the private endpoints do

# Authentication

> Authentication Example:

> 1) Create a Nonce:

```shell
date +%s%3N
# => "1584087661035"
```

> 2) Create the signature by concatenating the Nonce and the Api Key:

```shell
require 'openssl'

nonce = '1584087661035' # The nonce created above'
api_key = 'changeme' # Your API key (see getting started above)'
secret = 'changeme' # Your API secret (see getting started above)'
OpenSSL::HMAC.hexdigest("SHA256", secret, nonce + api_key)
# => "6cc108cb3427b655ccf0870fc7fa807ef3756506d4db3f3c93f8d4cd8ef0e611" 
```
>  3) Perform the authenticated request using the 3x headers:

```shell
curl -X GET https://trade.capecrypto.com/api/v2/trade/account/balances \
-H "X-Auth-Apikey: changeme" \
-H "X-Auth-Nonce: changeme" \
-H "X-Auth-Signature: changeme"
```

> 4) Example response:
```json
{
    "balance": "1.4995",
    "currency": "btc",
    "locked": "0.0"
}
```


<aside class="notice">
Make sure you've <strong>Enabled 2FA</strong> and <strong>Created your API key</strong> as mentioned in the Getting Started section above.
</aside>

Before calling a private endpoint you will need to generate three headers to accompany your request:


1. <strong>X-Auth-Nonce</strong>: A nonce is an arbitrary number that can be used only once. In our environment you MUST use a millisecond timestamp in UTC time. 
2. <strong>X-Auth-Apikey</strong>: Your API key that you created in the previous step
3. <strong>X-Auth-Signature</strong>: HMAC-SHA256 signature created using a concatenation of the X-Auth-Nonce and X-Auth-Apikey

# Example without Authentication
## <span class="request-type__get">GET</span> List available currencies

```shell
# http GET https://trade.capecrypto.com/api/v2/trade/public/markets
```
> The above command returns JSON structured like this:

```json
{
    "amount_precision": 5,
    "base_unit": "btc",
    "id": "btcusdt",
    "max_price": "1000000.0",
    "min_amount": "0.0001",
    "min_price": "10000.0",
    "name": "BTC/ZAR",
    "price_precision": 5,
    "quote_unit": "zar",
    "state": "enabled"
}
```

Example using httpie:

`http GET https://trade.capecrypto.com/api/v2/trade/public/markets`

Returns a list of all available currencies


# Example with Authentication

For the private endpoints, you need to authenticate your request. See the getting started and authentication sections above for how to create your Api Key and Secret, as well as generating a Nonce and Signature.

## <span class="request-type__post">POST</span> Create Order

```shell
http POST https://trade.capecrypto.com/api/v2/trade/market/orders \
"X-Auth-Apikey: changeme" \ # See getting started section above
"X-Auth-Nonce: changeme" \ # See authentication section above
"X-Auth-Signature: changeme" \ # See authentication section above
market=btczar side=buy volume=31 ord_type=limit price=160.82
```

> Example Response:

``` json
{
  "avg_price": "0.0",
  "created_at": "2020-03-12T17:01:56+01:00",
  "executed_volume": "0.0",
  "id": 10440269,
  "market": "btczar",
  "ord_type": "limit",
  "origin_volume": "31.0",
  "price": "160.82",
  "remaining_volume": "31.0",
  "side": "buy",
  "state": "pending",
  "trades_count": 0,
  "updated_at": "2020-03-12T17:01:56+01:00"
}
```

Example using httpie

`https://trade.capecrypto.com/api/v2/trade/market/orders`

Creates a new order on the Order Book, and returns information about that newly created order

## <span class="request-type__post">POST</span> Check Trade history

```shell
http POST https://trade.capecrypto.com/api/v2/trade/market/trades \
"X-Auth-Apikey: changeme" \
"X-Auth-Nonce: changeme" \
"X-Auth-Signature: changeme" \
```

> Example response:

``` json
{
  "amount": "5.0",
  "created_at": "2020-03-12T17:01:56+01:00",
  "fee": "0.002",
  "fee_amount": "0.01",
  "fee_currency": "btc",
  "id": 1834499,
  "market": "btczar",
  "order_id": 10440269,
  "price": "160.82",
  "side": "buy",
  "taker_type": "buy",
  "total": "804.1"
}
```

`https://trade.capecrypto.com/api/v2/trade/market/trades`

Get your trade history

# Public APIs

## <span class="request-type__get">GET</span> Trading fees

`/public/trading_fees`

### Description

Returns the current trading fees table as paginated collection

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| group | query | Member group for define maker/taker fee. | No | string |
| market_id | query | Market id for define maker/taker fee. | No | string |
| limit | query | Limit the number of returned paginations. Defaults to 100. | No | integer |
| page | query | Specify the page of paginated results. | No | integer |
| ordering | query | If set, returned values will be sorted in specific order, defaults to 'asc'. | No | string |
| order_by | query | Name of the field, which result will be ordered by. | No | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Returns trading_fees table as paginated collection | [ [TradingFee](#tradingfee) ] |

## <span class="request-type__get">GET</span> Health

`/public/health/ready`

### Description

Get application readiness status

### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Get application readiness status |

## <span class="request-type__get">GET</span> Alive

`/public/health/alive`

### Description

Get application liveness status

### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Get application liveness status |


## <span class="request-type__get">GET</span> Timestamp

`/public/timestamp`

### Description

Get server time, in seconds since Unix epoch.

### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Get server current time, in seconds since Unix epoch. |

## <span class="request-type__get">GET</span> Market Ticker

`/public/markets/{market}/tickers`

### Description

Get ticker of specific market.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| market | path |  | Yes | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get ticker of specific market. | [Ticker](#ticker) |

## <span class="request-type__get">GET</span> All tickers

`/public/markets/tickers`

### Description

Get ticker of all markets (For response doc see /:market/tickers/ response).

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get ticker of all markets (For response doc see /:market/tickers/ response). | [Ticker](#ticker) |

## <span class="request-type__get">GET</span> K Line

`/public/markets/{market}/k-line`

### Description

Get OHLC(k line) of specific market.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| market | path |  | Yes | string |
| period | query | Time period of K line, default to 1. You can choose between 1, 5, 15, 30, 60, 120, 240, 360, 720, 1440, 4320, 10080 | No | integer |
| time_from | query | An integer represents the seconds elapsed since Unix epoch. If set, only k-line data after that time will be returned. | No | integer |
| time_to | query | An integer represents the seconds elapsed since Unix epoch. If set, only k-line data till that time will be returned. | No | integer |
| limit | query | Limit the number of returned data points default to 30. Ignored if time_from and time_to are given. | No | integer |

### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Get OHLC(k line) of specific market. |

## <span class="request-type__get">GET</span> Market Depth

`/public/markets/{market}/depth`

### Description

Get depth or specified market. Both asks and bids are sorted from highest price to lowest.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| market | path |  | Yes | string |
| limit | query | Limit the number of returned price levels. Default to 300. | No | integer |

### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Get depth or specified market. Both asks and bids are sorted from highest price to lowest. |

## <span class="request-type__get">GET</span> Trades

`/public/markets/{market}/trades`

### Description

Get recent trades on market, each trade is included only once. Trades are sorted in reverse creation order.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| market | path |  | Yes | string |
| limit | query | Limit the number of returned trades. Default to 100. | No | integer |
| timestamp | query | An integer represents the seconds elapsed since Unix epoch.If set, only trades executed before the time will be returned. | No | integer |
| order_by | query | If set, returned trades will be sorted in specific order, default to 'desc'. | No | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get recent trades on market, each trade is included only once. Trades are sorted in reverse creation order. | [ [Trade](#trade) ] |

## <span class="request-type__get">GET</span> Order Book

`/public/markets/{market}/order-book`

### Description

Get the order book of specified market.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| market | path |  | Yes | string |
| asks_limit | query | Limit the number of returned sell orders. Default to 20. | No | integer |
| bids_limit | query | Limit the number of returned buy orders. Default to 20. | No | integer |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get the order book of specified market. | [ [OrderBook](#orderbook) ] |

## <span class="request-type__get">GET</span> Markets

`/public/markets`

### Description

Get all available markets.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| limit | query | Limit the number of returned paginations. Defaults to 100. | No | integer |
| page | query | Specify the page of paginated results. | No | integer |
| ordering | query | If set, returned values will be sorted in specific order, defaults to 'asc'. | No | string |
| order_by | query | Name of the field, which result will be ordered by. | No | string |
| base_unit | query | Strict filter for base unit | No | string |
| quote_unit | query | Strict filter for quote unit | No | string |
| search | query |  | No | json |
| search[base_code] | query | Search base currency code using LIKE | No | string |
| search[quote_code] | query | Search qoute currency code using LIKE | No | string |
| search[base_name] | query | Search base currency name using LIKE | No | string |
| search[quote_name] | query | Search quote currency name using LIKE | No | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get all available markets. | [ [Market](#market) ] |

## <span class="request-type__get">GET</span> All Currencies

`/public/currencies`

### Description

Get list of currencies

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| limit | query | Limit the number of returned paginations. Defaults to 100. | No | integer |
| page | query | Specify the page of paginated results. | No | integer |
| type | query | Currency type | No | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get list of currencies | [ [Currency](#currency) ] |

## <span class="request-type__get">GET</span> Single Currency

`/public/currencies/{id}`

### Description

Get a single currency

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| id | path | Currency code. | Yes | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get a currency | [Currency](#currency) |





# Account


## <span class="request-type__get">GET</span> Transaction History

` /account/transactions`

### Description

Get your transactions history.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| currency | query | Currency code | No | string |
| order_by | query | Sorting order | No | string |
| time_from | query | An integer represents the seconds elapsed since Unix epoch. | No | integer |
| time_to | query | An integer represents the seconds elapsed since Unix epoch. | No | integer |
| deposit_state | query | Filter deposits by states. | No | string |
| withdraw_state | query | Filter withdraws by states. | No | string |
| txid | query | Transaction id. | No | string |
| limit | query | Limit the number of returned transactions. Default to 100. | No | integer |
| page | query | Specify the page of paginated results. | No | integer |

### Responses

| Code | Description |
| ---- | ----------- |
| 200 | Get your transactions history. |

## <span class="request-type__post">POST</span> Create New Withdrawal

`/account/withdraws`

### Description

Creates new withdrawal to active beneficiary.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| otp | formData | OTP to perform action | Yes | integer |
| beneficiary_id | formData | ID of Active Beneficiary belonging to user. | Yes | integer |
| currency | formData | The currency code. | Yes | string |
| amount | formData | The amount to withdraw. | Yes | double |
| note | formData | Optional metadata to be applied to the transaction. Used to tag transactions with memorable comments. | No | string |

### Responses

| Code | Description |
| ---- | ----------- |
| 201 | Creates new withdrawal to active beneficiary. |

## <span class="request-type__post">POST</span> List Withdrawals

### Description

List your withdraws as paginated collection.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| currency | query | Currency code. | No | string |
| limit | query | Number of withdraws per page (defaults to 100, maximum is 100). | No | integer |
| state | query | Filter withdrawals by states. | No | string |
| rid | query | Wallet address on the Blockchain. | No | string |
| page | query | Page number (defaults to 1). | No | integer |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | List your withdraws as paginated collection. | [ [Withdraw](#withdraw) ] |

## <span class="request-type__delete">DELETE</span> Delete Beneficiary

` /account/beneficiaries/{id}`

### Description

Delete beneficiary

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| id | path | Beneficiary Identifier in Database | Yes | integer |

### Responses

| Code | Description |
| ---- | ----------- |
| 204 | Delete beneficiary |

## <span class="request-type__get">GET</span> Beneficiary ID

`/account/beneficiaries/{id}`

### Description

Get beneficiary by ID

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| id | path | Beneficiary Identifier in Database | Yes | integer |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get beneficiary by ID | [Beneficiary](#beneficiary) |

## <span class="request-type__patch">PATCH</span> Activate New Beneficiary

`/account/beneficiaries/{id}/activate`

### Description

Activates a new beneficiary with pin

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| id | path | Beneficiary Identifier in Database | Yes | integer |
| pin | formData | Pin code for beneficiary activation | Yes | integer |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Activates beneficiary with pin | [Beneficiary](#beneficiary) |

## <span class="request-type__post">POST</span> Create new Beneficiary

`/account/beneficiaries`

### Description

Create a new beneficiary

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| currency | formData | Beneficiary currency code. | Yes | string |
| name | formData | Human rememberable name which refer beneficiary. | Yes | string |
| description | formData | Human rememberable name which refer beneficiary. | No | string |
| data | formData | Beneficiary data in JSON format | Yes | json |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 201 | Create new beneficiary | [Beneficiary](#beneficiary) |

## <span class="request-type__get">GET</span> List Beneficiaries

`/account/beneficiaries`

### Description

Get list of user beneficiaries

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| currency | query | Beneficiary currency code. | No | string |
| state | query | Defines either beneficiary active - user can use it to withdraw moneyor pending - requires beneficiary activation with pin. | No | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get list of user beneficiaries | [ [Beneficiary](#beneficiary) ] |

## <span class="request-type__get">GET</span> Deposit Address

`/account/deposit_address/{currency}`

### Description

Returns the deposit address for account you want to deposit into, by currency. (The address may be blank because the address generation process is still in progress. If this is that case you, please wait a short while and then try again later.)

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| currency | path | The account you want to deposit to. | Yes | string |
| address_format | query | Address format legacy/cash | No | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Returns deposit address for account you want to deposit to by currency. The address may be blank because address generation process is still in progress. If this case you should try again later. | [Deposit](#deposit) |

## <span class="request-type__get">GET</span> Deposit Details

`/account/deposits/{txid}`

### Description

Get details of specific deposit.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| txid | path | Deposit transaction id | Yes | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get details of specific deposit. | [Deposit](#deposit) |

## <span class="request-type__get">GET</span> Deposit History

`/account/deposits`

### Description

Get your deposit history.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| currency | query | Currency code | No | string |
| state | query | Filter deposits by states. | No | string |
| txid | query | Deposit transaction id. | No | string |
| limit | query | Number of deposits per page (defaults to 100, maximum is 100). | No | integer |
| page | query | Page number (defaults to 1). | No | integer |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get your deposits history. | [ [Deposit](#deposit) ] |

## <span class="request-type__get">GET</span> User Account by Currency

`/account/balances/{currency}`

### Description

Get user account by currency

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| currency | path | The currency code. | Yes | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get user account by currency | [Account](#account) |

## <span class="request-type__get">GET</span> List User Accounts

`/account/balances`

### Description

Get list of user accounts

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| limit | query | Limit the number of returned paginations. Defaults to 100. | No | integer |
| page | query | Specify the page of paginated results. | No | integer |
| nonzero | query | Filter non zero balances. | No | Boolean |
| search | query |  | No | json |
| search[currency_code] | query |  | No | string |
| search[currency_name] | query |  | No | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get list of user accounts | [ [Account](#account) ] |


# Trade

## <span class="request-type__get">GET</span> List Own Trades

 `/market/trades`

### Description

Get your executed trades. Trades are sorted in reverse creation order.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| market | query |  | No | string |
| limit | query | Limit the number of returned trades. Default to 100. | No | integer |
| page | query | Specify the page of paginated results. | No | integer |
| time_from | query | An integer represents the seconds elapsed since Unix epoch.If set, only trades executed after the time will be returned. | No | integer |
| time_to | query | An integer represents the seconds elapsed since Unix epoch.If set, only trades executed before the time will be returned. | No | integer |
| order_by | query | If set, returned trades will be sorted in specific order, default to 'desc'. | No | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get your executed trades. Trades are sorted in reverse creation order. | [ [Trade](#trade) ] |

## <span class="request-type__post">POST</span> Cancel All Orders

`/market/orders/cancel`

### Description

Cancel all of your open orders.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| market | formData |  | No | string |
| side | formData | If present, only sell orders (asks) or buy orders (bids) will be canncelled. | No | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 201 | Cancel all my orders. | [Order](#order) |

## <span class="request-type__post">POST</span> Cancel Specific Order

`/market/orders/{id}/cancel`

### Description

Cancel a specific order by it's Order ID.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| id | path |  | Yes | string |

### Responses

| Code | Description |
| ---- | ----------- |
| 201 | Cancel an order. |

## <span class="request-type__post">POST</span> Create Order

`/market/orders`

### Description

Create a Sell/Buy order.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| market | formData |  | Yes | string |
| side | formData |  | Yes | string |
| volume | formData |  | Yes | double |
| ord_type | formData |  | No | string |
| price | formData |  | Yes | double |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 201 | Create a Sell/Buy order. | [Order](#order) |

## <span class="request-type__post">POST</span> List Orders

### Description

Get a list of your orders, result is paginated.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| market | query |  | No | string |
| base_unit | query |  | No | string |
| quote_unit | query |  | No | string |
| state | query | Filter order by state. | No | string |
| limit | query | Limit the number of returned orders, default to 100. | No | integer |
| page | query | Specify the page of paginated results. | No | integer |
| order_by | query | If set, returned orders will be sorted in specific order, default to "desc". | No | string |
| ord_type | query | Filter order by ord_type. | No | string |
| type | query | Filter order by type. | No | string |
| time_from | query | An integer represents the seconds elapsed since Unix epoch.If set, only orders created after the time will be returned. | No | integer |
| time_to | query | An integer represents the seconds elapsed since Unix epoch.If set, only orders created before the time will be returned. | No | integer |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get your orders, result is paginated. | [ [Order](#order) ] |

## <span class="request-type__get">GET</span> Order Info

`/market/orders/{id}`

### Description

Get information of specified order.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| id | path |  | Yes | string |

### Responses

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | Get information of specified order. | [Order](#order) |

 
# Models


### TradingFee

Returns trading_fees table as paginated collection

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| id | integer | Unique trading fee table identifier in database. | No |
| group | string | Member group for define maker/taker fee. | No |
| market_id | string | Market id for define maker/taker fee. | No |
| maker | double | Market maker fee. | No |
| taker | double | Market taker fee. | No |
| created_at | string | Trading fee table created time in iso8601 format. | No |
| updated_at | string | Trading fee table updated time in iso8601 format. | No |

### Ticker

Get ticker of all markets (For response doc see /:market/tickers/ response).

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| at | integer | Timestamp of ticker | No |
| ticker | [TickerEntry](#tickerentry) | Ticker entry for specified time | No |

### TickerEntry

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| low | double | The lowest trade price during last 24 hours (0.0 if no trades executed during last 24 hours) | No |
| high | double | The highest trade price during last 24 hours (0.0 if no trades executed during last 24 hours) | No |
| open | double | Price of the first trade executed 24 hours ago or less | No |
| last | double | The last executed trade price | No |
| volume | double | Total volume of trades executed during last 24 hours | No |
| amount | double | Total amount of trades executed during last 24 hours | No |
| vol | double | Alias to volume | No |
| avg_price | double | Average price more precisely VWAP is calculated by adding up the total traded for every transaction(price multiplied by the number of shares traded) and then dividing by the total shares traded | No |
| price_change_percent | string | Price change in the next format +3.19%.Price change is calculated using next formula (last - open) / open * 100% | No |
| at | integer | Timestamp of ticker | No |

### Trade

Get your executed trades. Trades are sorted in reverse creation order.

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| id | string | Trade ID. | No |
| price | double | Trade price. | No |
| amount | double | Trade amount. | No |
| total | double | Trade total (Amount * Price). | No |
| fee_currency | double | Currency user's fees were charged in. | No |
| fee | double | Percentage of fee user was charged for performed trade. | No |
| fee_amount | double | Amount of fee user was charged for performed trade. | No |
| market | string | Trade market id. | No |
| created_at | string | Trade create time in iso8601 format. | No |
| taker_type | string | Trade taker order type (sell or buy). | No |
| side | string | Trade side. | No |
| order_id | integer | Order id. | No |

### OrderBook

Get the order book of specified market.

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| asks | [ [Order](#order) ] | Asks in orderbook | No |
| bids | [ [Order](#order) ] | Bids in orderbook | No |

### Order

Get your orders, result is paginated.

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| id | integer | Unique order id. | No |
| uuid | string | Unique order UUID. | No |
| side | string | Either 'sell' or 'buy'. | No |
| ord_type | string | Type of order, either 'limit' or 'market'. | No |
| price | double | Price for each unit. e.g.If you want to sell/buy 1 btc at 3000 usd, the price is '3000.0' | No |
| avg_price | double | Average execution price, average of price in trades. | No |
| state | string | One of 'wait', 'done', or 'cancel'.An order in 'wait' is an active order, waiting fulfillment;a 'done' order is an order fulfilled;'cancel' means the order has been canceled. | No |
| market | string | The market in which the order is placed, e.g. 'btcusd'.All available markets can be found at /api/v2/markets. | No |
| created_at | string | Order create time in iso8601 format. | No |
| updated_at | string | Order updated time in iso8601 format. | No |
| origin_volume | double | The amount user want to sell/buy.An order could be partially executed,e.g. an order sell 5 btc can be matched with a buy 3 btc order,left 2 btc to be sold; in this case the order's volume would be '5.0',its remaining_volume would be '2.0', its executed volume is '3.0'. | No |
| remaining_volume | double | The remaining volume, see 'volume'. | No |
| executed_volume | double | The executed volume, see 'volume'. | No |
| trades_count | integer | Count of trades. | No |
| trades | [ [Trade](#trade) ] | Trades wiht this order. | No |

### Market

Get all available markets.

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| id | string | Unique market id. It's always in the form of xxxyyy,where xxx is the base currency code, yyy is the quotecurrency code, e.g. 'btcusd'. All available markets canbe found at /api/v2/markets. | No |
| name | string | Market name. | No |
| base_unit | string | Market Base unit. | No |
| quote_unit | string | Market Quote unit. | No |
| min_price | double | Minimum order price. | No |
| max_price | double | Maximum order price. | No |
| min_amount | double | Minimum order amount. | No |
| amount_precision | double | Precision for order amount. | No |
| price_precision | double | Precision for order price. | No |
| state | string | Market state defines if user can see/trade on current market. | No |

### Currency

Get a currency

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| id | string | Currency code. | No |
| name | string | Currency name | No |
| symbol | string | Currency symbol | No |
| explorer_transaction | string | Currency transaction exprorer url template | No |
| explorer_address | string | Currency address exprorer url template | No |
| type | string | Currency type | No |
| deposit_enabled | string | Currency deposit possibility status (true/false). | No |
| withdrawal_enabled | string | Currency withdrawal possibility status (true/false). | No |
| deposit_fee | string | Currency deposit fee | No |
| min_deposit_amount | string | Minimal deposit amount | No |
| withdraw_fee | string | Currency withdraw fee | No |
| min_withdraw_amount | string | Minimal withdraw amount | No |
| withdraw_limit_24h | string | Currency 24h withdraw limit | No |
| withdraw_limit_72h | string | Currency 72h withdraw limit | No |
| base_factor | string | Currency base factor | No |
| precision | string | Currency precision | No |
| position | string | Position used for defining currencies order | No |
| icon_url | string | Currency icon | No |
| min_confirmations | string | Number of confirmations required for confirming deposit or withdrawal | No |

### Withdraw

List your withdraws as paginated collection.

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| id | integer | The withdrawal id. | No |
| currency | string | The currency code. | No |
| type | string | The withdrawal type | No |
| amount | string | The withdrawal amount | No |
| fee | double | The exchange fee. | No |
| blockchain_txid | string | The withdrawal transaction id. | No |
| rid | string | The beneficiary ID or wallet address on the Blockchain. | No |
| state | string | The withdrawal state. | No |
| confirmations | integer | Number of confirmations. | No |
| note | string | Withdraw note. | No |
| created_at | string | The datetimes for the withdrawal. | No |
| updated_at | string | The datetimes for the withdrawal. | No |
| done_at | string | The datetime when withdraw was completed | No |

### Beneficiary

Get list of user beneficiaries

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| id | integer | Beneficiary Identifier in Database | No |
| currency | string | Beneficiary currency code. | No |
| name | string | Human rememberable name which refer beneficiary. | No |
| description | string | Human rememberable description of beneficiary. | No |
| data | json | Bank Account details for fiat Beneficiary in JSON format.For crypto it's blockchain address. | No |
| state | string | Defines either beneficiary active - user can use it to withdraw moneyor pending - requires beneficiary activation with pin. | No |

### Deposit

Get your deposits history.

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| id | integer | Unique deposit id. | No |
| currency | string | Deposit currency id. | No |
| amount | double | Deposit amount. | No |
| fee | double | Deposit fee. | No |
| txid | string | Deposit transaction id. | No |
| confirmations | integer | Number of deposit confirmations. | No |
| state | string | Deposit state. | No |
| created_at | string | The datetime when deposit was created. | No |
| completed_at | string | The datetime when deposit was completed.. | No |
| tid | string | The shared transaction ID | No |

### Account

Get list of user accounts

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| currency | string | Currency code. | No |
| balance | double | Account balance. | No |
| locked | double | Account locked funds. | No |

### Transactions

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| address | string | Recipient address of transaction. | No |
| currency | string | Transaction currency id. | No |
| amount | double | Transaction amount. | No |
| fee | double | Transaction fee. | No |
| txid | string | Transaction id. | No |
| state | string | Transaction state. | No |
| note | string | Withdraw note. | No |
| confirmations | integer | Number of confirmations. | No |
| updated_at | string | Transaction updated time in iso8601 format. | No |
| type | string | Type of transaction | No |

### Member

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| uid | string | Member UID. | No |
| email | string | Member email. | No |
| accounts | [ [Account](#account) ] | Member accounts. | No |
