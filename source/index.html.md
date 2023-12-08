---
title: Cape Crypto API Documentation

# language_tabs: # must be one of https://git.io/vQNgJ
#   - shell

toc_footers:
  - <a href="https://trade.capecrypto.com" target="_blank">Cape Crypto PTY LTD</a>

includes:
  - errors

search: true

code_clipboard: true
---

# Introduction

Welcome to the Cape Crypto API Documentation. You can use our full API suite to integrate with us.

Create and cancel orders, view your account balance, stream orderbook and account updates over websocket, and perform all other actions you can perform from the apps.

🚀 Looking for trading features that we haven't added yet? We'll add them for you! <a href="https://support.capecrypto.com/hc/en-us/requests/new" target="_blank">Reach out to us</a>

<img src="https://raw.githubusercontent.com/capecrypto/kungfu/main/hummingbot_icon.jpg" alt="Hummingbot icon" title="Hummingbot icon" width="15" height="15"/> Looking for a Hummingbot connector for Cape Crypto? We have one! <a href="https://support.capecrypto.com/hc/en-us/requests/new" target="_blank">Reach out to us</a>

# Getting started

The API suite consists of public and private endpoints. Private endpoints require your requests to be signed for authentication. In order to sign your requests, you will need to create and API key and secret, and have 2FA enabled:

1. Sign into your account <a href="https://trade.capecrypto.com" target="_blank">Cape Crypto</a>
2. Head to the <strong>Profile</strong> page by clicking the profile icon at the top right
3. Enable 2FA
4. Create a new API key
5. Save your new API key and Secret and do not share it with anyone

Use your API key and secret to sign your requests for all private endpoints. (Public endpoints do not require authentication)

- Base url REST: `https://trade.capecrypto.com/api/v2/trade`
- Base url WSS: `wss://trade.capecrypto.com/api/v2/stream`

# Authentication

> Authentication:

```javascript
// generateHeaders.js

const crypto = require("crypto");
const apiKey = "123456...";
const secret = "ABCDEF...";

module.exports = async () => {
  const timeStamp = new Date().getTime();
  const queryString = `${timeStamp}${apikey}`;
  const signature = crypto
    .createHmac("sha256", secret)
    .update(queryString)
    .digest("hex");

  return {
    "Content-Type": "application/json;charset=utf-8",
    "X-Auth-Apikey": apikey,
    "X-Auth-Nonce": timeStamp,
    "X-Auth-Signature": signature,
  };
};
```

<aside class="notice">
Ensure you have <strong>Enabled 2FA</strong> and <strong>Created your API key</strong>.
</aside>

To securely access the private endpoints, each request requires authentication headers.

Generate a new signature for each new request, and add these headers to the request.

1. <strong>X-Auth-Nonce</strong>: Current timestamp in milliseconds UTC time.
2. <strong>X-Auth-Apikey</strong>: Your API key
3. <strong>X-Auth-Signature</strong>: HMAC-SHA256 encrypted string of the combined timestamp, apikey and secret (see JS example)

# Example using Authentication

## <span class="request-type__post">POST</span> Create order

```javascript
// generateHeaders.js

const crypto = require("crypto");
const apiKey = "123456...";
const secret = "ABCDEF...";

module.exports = async () => {
  const timeStamp = new Date().getTime();
  const queryString = `${timeStamp}${apikey}`;
  const signature = crypto
    .createHmac("sha256", secret)
    .update(queryString)
    .digest("hex");

  return {
    "Content-Type": "application/json;charset=utf-8",
    "X-Auth-Apikey": apikey,
    "X-Auth-Nonce": timeStamp,
    "X-Auth-Signature": signature,
  };
};
```

> Send the authenticated request:

```javascript
// createOrder.js
const axios = require("axios");
const generateHeaders = require("./generateHeaders");

createOrder = async () => {
  const baseUrl = "https://trade.capecrypto.com";
  const method = "POST";
  const path = `/api/v2/trade/market/orders`;
  const url = `${baseUrl}${path}`;
  const headers = await generateHeaders();

  const orderData = {
    market: "btczar",
    side: "sell",
    volume: "0.00001",
    ord_type: "limit",
    price: "800000",
    post_only: true,
    custom_order_id: "302b0494-1a06-4089-846c-0c6b13f1b661",
  };

  const axiosConfig = {
    method: method,
    url: url,
    headers: headers,
    data: orderData,
  };

  try {
    const results = await axios(axiosConfig);
    console.log(JSON.stringify(results.data, undefined, 2));
  } catch (err) {
    console.log(err);
  }
};
```

> Create order response:

```json
{
  "id": 173757,
  "uuid": "7e86a4fa-fcae-4953-b9bc-d6f328beea48",
  "side": "sell",
  "ord_type": "limit",
  "price": "800000.0",
  "avg_price": "0.0",
  "state": "pending",
  "market": "btczar",
  "created_at": "2023-12-08T08:46:59+01:00",
  "updated_at": "2023-12-08T08:46:59+01:00",
  "origin_volume": "0.00001",
  "remaining_volume": "0.00001",
  "executed_volume": "0.0",
  "trades_count": 0,
  "custom_order_id": "302b0494-1a06-4089-846c-0c6b13f1b661"
}
```

`https://trade.capecrypto.com/api/v2/trade/market/orders`

Create a new order on your account by signing your request and submitting the request

# Public request example

## <span class="request-type__get">GET</span> Get available markets

```javascript
const axios = require("axios");

getMarkets = async () => {
  const baseUrl = "https://trade.capecrypto.com";
  const method = "GET";
  const path = `/api/v2/trade/public/markets`;
  const url = `${baseUrl}${path}`;

  const axiosConfig = {
    method: method,
    url: url,
  };

  try {
    const results = await axios(axiosConfig);
    console.log(JSON.stringify(results.data, undefined, 2));
  } catch (err) {
    console.log(err);
  }
};

getMarkets();
```

> Markets response:

```json
[
  {
    "id": "btczar",
    "name": "BTC/ZAR",
    "base_unit": "btc",
    "quote_unit": "zar",
    "min_price": "200000.0",
    "max_price": "1200000.0",
    "min_amount": "0.00001",
    "amount_precision": 8,
    "price_precision": 2,
    "state": "enabled"
  },
  {
    "id": "ethzar",
    "name": "ETH/ZAR",
    "base_unit": "eth",
    "quote_unit": "zar",
    "min_price": "10000.0",
    "max_price": "100000.0",
    "min_amount": "0.0001",
    "amount_precision": 8,
    "price_precision": 2,
    "state": "enabled"
  },
  {
    "id": "xrpzar",
    "name": "XRP/ZAR",
    "base_unit": "xrp",
    "quote_unit": "zar",
    "min_price": "1.0",
    "max_price": "100.0",
    "min_amount": "1.0",
    "amount_precision": 6,
    "price_precision": 2,
    "state": "enabled"
  },
  {
    "id": "usdczar",
    "name": "USDC/ZAR",
    "base_unit": "usdc",
    "quote_unit": "zar",
    "min_price": "14.0",
    "max_price": "29.0",
    "min_amount": "0.5",
    "amount_precision": 2,
    "price_precision": 2,
    "state": "enabled"
  }
]
```

`GET https://trade.capecrypto.com/api/v2/trade/public/markets`

Returns a list of all available markets

# Public

## <span class="request-type__get">GET</span> Alive

`/public/health/alive`

### Description

Check that the trade engine is running. Occasionally the trade engine will be temporarily taken offline for udpates and general maintenance. Maintenence will usually not take longer than a few minutes.

### Responses

| Code | Description                                   |
| ---- | --------------------------------------------- |
| 200  | Trade engine is running, trading is available |

## <span class="request-type__get">GET</span> Ticker

`/public/markets/{market}/tickers`

### Description

Get ticker of specific market.

### Parameters

| Name   | Located in | Description | Required | Schema |
| ------ | ---------- | ----------- | -------- | ------ |
| market | path       |             | Yes      | string |

### Responses

| Code                  | Description       | Schema |
| --------------------- | ----------------- | ------ |
| 200 Returns a ticker. | [Ticker](#ticker) |

## <span class="request-type__get">GET</span> All tickers

`/public/markets/tickers`

### Description

Get ticker of all markets

### Responses

| Code | Description                  | Schema            |
| ---- | ---------------------------- | ----------------- |
| 200  | Returns an array of tickers. | [Ticker](#ticker) |

## <span class="request-type__get">GET</span> K Line

`/public/markets/{market}/k-line`

### Description

Get K-Line (OHLC) of specific market.

### Parameters

| Name      | Located in | Description                                                                                                         | Required | Schema  |
| --------- | ---------- | ------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| market    | path       |                                                                                                                     | Yes      | string  |
| period    | query      | Time period of K line, default to 1. You can choose between 1, 5, 15, 30, 60, 120, 240, 360, 720, 1440, 4320, 10080 | No       | integer |
| time_from | query      | Timestamp in ms. If set, only k-line data after that time will be returned.                                         | No       | integer |
| time_to   | query      | Timestamp in ms. If set, only k-line data till that time will be returned.                                          | No       | integer |
| limit     | query      | Limit the number of returned data points default to 30. Ignored if time_from and time_to are given.                 | No       | integer |

### Responses

| Code | Description                      |
| ---- | -------------------------------- |
| 200  | Returns K-Line (OHLC) of market. |

## <span class="request-type__get">GET</span> Orderbook

`/public/markets/{market}/depth`

### Description

Get the orderbook for the specified market eg. 'btczar'. Asks and bids are sorted away from the spread, ie asks are sorted low to high, and bids are sorted high to low. Results are returned in tupples of [price, amount] in the base currency eg. 'btc'.

### Parameters

| Name   | Located in | Description                                             | Required | Schema  |
| ------ | ---------- | ------------------------------------------------------- | -------- | ------- |
| market | path       |                                                         | Yes      | string  |
| limit  | query      | Limit the number of returned price levels. Default 100. | No       | integer |

### Responses

| Code | Description                       |
| ---- | --------------------------------- |
| 200  | Get orderbook of specified market |

## <span class="request-type__get">GET</span> Market trades

`/public/markets/{market}/trades`

### Description

Get recent trades for the specified market, results are paginated

### Parameters

| Name      | Located in | Description                                                   | Required | Schema  |
| --------- | ---------- | ------------------------------------------------------------- | -------- | ------- |
| market    | path       |                                                               | Yes      | string  |
| limit     | query      | Limit the number of returned trades. Default to 100.          | No       | integer |
| time_from | query      | Timestamp in ms. Return trades executed after the timestamp.  | No       | integer |
| time_to   | query      | Timestamp in ms. Return trades executed before the timestamp. | No       | integer |
| page      | query      | Paginated page number.                                        | No       | integer |
| order_by  | query      | Default is desc (newest to oldest). Options are [asc desc]    | No       | string  |

### Responses

| Code | Description                  | Schema              |
| ---- | ---------------------------- | ------------------- |
| 200  | Get recent trades on market. | [ [Trade](#trade) ] |

## <span class="request-type__get">GET</span> Markets

`/public/markets`

### Description

Get all available markets.

### Parameters

| Name       | Located in | Description                                                             | Required | Schema  |
| ---------- | ---------- | ----------------------------------------------------------------------- | -------- | ------- |
| limit      | query      | Limit the number of returned paginations. Defaults to 100.              | No       | integer |
| page       | query      | Paginated page number.                                                  | No       | integer |
| ordering   | query      | Order of results by field name (see order_by below), defaults to 'asc'. | No       | string  |
| order_by   | query      | Name of the field to order by. [id position]                            | No       | string  |
| base_unit  | query      | Return only markets that have this currency as the base unit eg. 'btc'  | No       | string  |
| quote_unit | query      | Return only markets that have this currency as the quote unit eg. 'zar' | No       | string  |

### Responses

| Code | Description                | Schema                |
| ---- | -------------------------- | --------------------- |
| 200  | Get all available markets. | [ [Market](#market) ] |

## <span class="request-type__get">GET</span> All Currencies

`/public/currencies`

### Description

Get list of currencies

### Parameters

| Name  | Located in | Description                                                | Required | Schema  |
| ----- | ---------- | ---------------------------------------------------------- | -------- | ------- |
| limit | query      | Limit the number of returned paginations. Defaults to 100. | No       | integer |
| page  | query      | Paginated page number.                                     | No       | integer |
| type  | query      | Currency type                                              | No       | string  |

### Responses

| Code | Description            | Schema                    |
| ---- | ---------------------- | ------------------------- |
| 200  | Get list of currencies | [ [Currency](#currency) ] |

## <span class="request-type__get">GET</span> Single Currency

`/public/currencies/{id}`

### Description

Get a single currency

### Parameters

| Name | Located in | Description    | Required | Schema |
| ---- | ---------- | -------------- | -------- | ------ |
| id   | path       | Currency code. | Yes      | string |

### Responses

| Code | Description    | Schema                |
| ---- | -------------- | --------------------- |
| 200  | Get a currency | [Currency](#currency) |

# Account

#

## <span class="request-type__post">POST</span> Create new beneficiary

`/account/beneficiaries`

### Description

Create a new beneficiary

### Parameters

| Name        | Located in | Description                                      | Required | Schema |
| ----------- | ---------- | ------------------------------------------------ | -------- | ------ |
| currency    | formData   | Beneficiary currency code.                       | Yes      | string |
| name        | formData   | Human rememberable name which refer beneficiary. | Yes      | string |
| description | formData   | Human rememberable name which refer beneficiary. | No       | string |
| data        | formData   | Beneficiary data in JSON format                  | Yes      | json   |

### Responses

| Code | Description            | Schema                      |
| ---- | ---------------------- | --------------------------- |
| 201  | Create new beneficiary | [Beneficiary](#beneficiary) |

## <span class="request-type__patch">PATCH</span> Activate new neneficiary

`/account/beneficiaries/{id}/activate`

### Description

Activates a new beneficiary with pin emailed during beneficiary creation

### Parameters

| Name | Located in | Description                         | Required | Schema  |
| ---- | ---------- | ----------------------------------- | -------- | ------- |
| id   | path       | Beneficiary Identifier              | Yes      | integer |
| pin  | formData   | Pin code for beneficiary activation | Yes      | integer |

### Responses

| Code | Description                    | Schema                      |
| ---- | ------------------------------ | --------------------------- |
| 200  | Activates beneficiary with pin | [Beneficiary](#beneficiary) |

## <span class="request-type__delete">DELETE</span> Delete beneficiary

` /account/beneficiaries/{id}`

### Description

Delete beneficiary

### Parameters

| Name | Located in | Description            | Required | Schema  |
| ---- | ---------- | ---------------------- | -------- | ------- |
| id   | path       | Beneficiary Identifier | Yes      | integer |

### Responses

| Code | Description        |
| ---- | ------------------ |
| 204  | Delete beneficiary |

## <span class="request-type__get">GET</span> Beneficiary by id

`/account/beneficiaries/{id}`

### Description

Get beneficiary by id

### Parameters

| Name | Located in | Description            | Required | Schema  |
| ---- | ---------- | ---------------------- | -------- | ------- |
| id   | path       | Beneficiary Identifier | Yes      | integer |

### Responses

| Code | Description           | Schema                      |
| ---- | --------------------- | --------------------------- |
| 200  | Get beneficiary by id | [Beneficiary](#beneficiary) |

## <span class="request-type__get">GET</span> List Beneficiaries

`/account/beneficiaries`

### Description

Get list of user beneficiaries

### Parameters

| Name     | Located in | Description                                                                                                                 | Required | Schema |
| -------- | ---------- | --------------------------------------------------------------------------------------------------------------------------- | -------- | ------ |
| currency | query      | Beneficiary currency code.                                                                                                  | No       | string |
| state    | query      | Defines either beneficiary active - user can use it to withdraw moneyor pending - requires beneficiary activation with pin. | No       | string |

### Responses

| Code | Description                    | Schema                          |
| ---- | ------------------------------ | ------------------------------- |
| 200  | Get list of user beneficiaries | [ [Beneficiary](#beneficiary) ] |

## <span class="request-type__post">POST</span> New withdrawal

`/account/withdraws`

### Description

Creates new withdrawal to active beneficiary.

### Parameters

| Name           | Located in | Description                                                                                                                                                   | Required | Schema  |
| -------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| otp            | formData   | OTP to perform action                                                                                                                                         | Yes      | integer |
| beneficiary_id | formData   | ID of active Beneficiary.                                                                                                                                     | Yes      | integer |
| currency       | formData   | The currency code matching the beneficiary.                                                                                                                   | Yes      | string  |
| amount         | formData   | The amount to withdraw.                                                                                                                                       | Yes      | double  |
| note           | formData   | ZAR withdrawals only. Use 'same_day_rtc' for [Faster withdraws](https://support.capecrypto.com/hc/en-za/articles/360015121638-Fee-schedule#zar-withdraw-fees) | No       | string  |

### Responses

| Code | Description             |
| ---- | ----------------------- |
| 201  | Creates new withdrawal. |

## <span class="request-type__post">GET</span> Withdrawal history

`/account/withdraws`

### Description

Get your withdraw history, paginated.

### Parameters

| Name     | Located in | Description                                                     | Required | Schema  |
| -------- | ---------- | --------------------------------------------------------------- | -------- | ------- |
| currency | query      | Currency code.                                                  | No       | string  |
| limit    | query      | Number of withdraws per page (defaults to 100, maximum is 100). | No       | integer |
| state    | query      | Filter by withdraw state. [ [Withdraw states](#states) ]        | No       | string  |
| rid      | query      | Destination address (blockchain withdraws only).                | No       | string  |
| page     | query      | Page number (defaults to 1).                                    | No       | integer |

### Responses

| Code | Description          | Schema                    |
| ---- | -------------------- | ------------------------- |
| 200  | List your withdraws. | [ [Withdraw](#withdraw) ] |

## <span class="request-type__post">GET</span> Instant deposit methods

`/payments/methods`

### Description

Gets your available instant FIAT deposit methods, including voucher providers whose vouchers you can use to redeem (deposit). Use the id of the provider when creating the deposit request for the specific method.

### Responses

| Code | Description                            | Schema                                            |
| ---- | -------------------------------------- | ------------------------------------------------- |
| 201  | Array of instant FIAT deposit methods. | [Instant Deposit Method](#instant-deposit-method) |

## <span class="request-type__post">POST</span> Redeem voucher

`/payments/vouchers/new`

### Description

Redeem a voucher code.

### Parameters

| Name              | Located in | Description                                                      | Required | Schema  |
| ----------------- | ---------- | ---------------------------------------------------------------- | -------- | ------- |
| payment_method_id | query      | The id of this deposit method (see Instant FIAT deposit methods) | Yes      | integer |
| amount            | query      | The amount to redeem, eg 10 redeems R10                          | Yes      | double  |
| voucher_pin       | query      | The voucher redemption code.                                     | Yes      | string  |

### Responses

| Code | Description         | Schema                                    |
| ---- | ------------------- | ----------------------------------------- |
| 201  | Voucher redemption. | [Voucher redemption](#voucher-redemption) |

## <span class="request-type__get">GET</span> Deposit address

`/account/deposit_address/{currency}`

### Description

Fetch the deposit address for the specified currency.

Note: If your currency deposit address has not been created yet, the result may return empty while the address is being created. If it is empty, please try again in a few seconds.

### Parameters

| Name     | Located in | Description              | Required | Schema |
| -------- | ---------- | ------------------------ | -------- | ------ |
| currency | path       | The currency to receive. | Yes      | string |

### Responses

| Code | Description                                                                        | Schema              |
| ---- | ---------------------------------------------------------------------------------- | ------------------- |
| 200  | Deposit address. (May be empty on first request for new accounts, see note above). | [Deposit](#deposit) |

## <span class="request-type__get">GET</span> Deposit history by txid

`/account/deposits/{txid}`

### Description

Get details of a specific blockchain deposit by the txid.

### Parameters

| Name | Located in | Description            | Required | Schema |
| ---- | ---------- | ---------------------- | -------- | ------ |
| txid | path       | Deposit transaction id | Yes      | string |

### Responses

| Code | Description                      | Schema              |
| ---- | -------------------------------- | ------------------- |
| 200  | Get details of specific deposit. | [Deposit](#deposit) |

## <span class="request-type__get">GET</span> Deposit history

`/account/deposits`

### Description

Get your deposit history

### Parameters

| Name     | Located in | Description                                                    | Required | Schema  |
| -------- | ---------- | -------------------------------------------------------------- | -------- | ------- |
| currency | query      | Currency code                                                  | No       | string  |
| state    | query      | Filter deposits by deposit state [States](#states)             | No       | string  |
| txid     | query      | Deposit transaction id.                                        | No       | string  |
| limit    | query      | Number of deposits per page (defaults to 100, maximum is 100). | No       | integer |
| page     | query      | Page number (defaults to 1).                                   | No       | integer |

### Responses

| Code | Description               | Schema                  |
| ---- | ------------------------- | ----------------------- |
| 200  | Get your deposit history. | [ [Deposit](#deposit) ] |

## <span class="request-type__get">GET</span> Balance

`/account/balances/{currency}`

### Description

Get single account balance by currency code eg. 'btc'

### Parameters

| Name     | Located in | Description        | Required | Schema |
| -------- | ---------- | ------------------ | -------- | ------ |
| currency | path       | The currency code. | Yes      | string |

### Responses

| Code | Description                     | Schema              |
| ---- | ------------------------------- | ------------------- |
| 200  | Get account balance by currency | [Account](#account) |

## <span class="request-type__get">GET</span> All balances

`/account/balances`

### Description

Get account balances for all currencies

### Parameters

| Name    | Located in | Description                                                | Required | Schema  |
| ------- | ---------- | ---------------------------------------------------------- | -------- | ------- |
| limit   | query      | Limit the number of returned paginations. Defaults to 100. | No       | integer |
| page    | query      | Paginated page number.                                     | No       | integer |
| nonzero | query      | Filter non zero balances.                                  | No       | Boolean |

### Responses

| Code | Description       | Schema                  |
| ---- | ----------------- | ----------------------- |
| 200  | Array of balances | [ [Account](#account) ] |

## <span class="request-type__post">POST</span> New lightning payment

`/account/lightning/pays`

### Description

Submits a payment request to pay a BTC Lightning BOLT11 invoice (withdraws this balance from your BTC account).

As the payment is requested and validated on the lightning network, please query your withdrawal history to confirm that the payment succeeded.

### Parameters

| Name               | Located in | Description                                                                                 | Required | Schema |
| ------------------ | ---------- | ------------------------------------------------------------------------------------------- | -------- | ------ |
| bolt               | query      | The bolt11 invoice to pay.                                                                  | Yes      | string |
| amount             | query      | The amount to of the invoice in SATs. Sanity check is performed against the supplied bolt11 | Yes      | double |
| description        | query      | The description of bolt description                                                         | No       | string |
| member_description | query      | Personal description of this payment for your records                                       | No       | string |

### Responses

| Code | Description        | Schema                                      |
| ---- | ------------------ | ------------------------------------------- |
| 201  | Lightning payment. | [ [Lightning payment](#lightning-payment) ] |

## <span class="request-type__post">POST</span> Create a Lightning invoice

`/account/lightning/invoices`

### Description

Create a new lightning invoice to be paid (receive) over the BTC Lightning network.

You can optionally specify a conversion_percent (1 = 100%, 0.1 = 10%) to automatically convert once the invoice is paid. The conversion percent should be such that the conversion amount is greater than or equal to the minimum order amount for the market eg. 'btczar'. For example if receiving BTC 0.00001, and the minimum order amount on 'btczar' is 0.00001, your conversion percent would have to be 1 (100%). Anything less would cause the invoice creation to fail, as the conversion amount would be below the minimum market order amount.

The invoice expires in 1 hour.

### Parameters

| Name               | Located in | Description                                                                                 | Required | Schema |
| ------------------ | ---------- | ------------------------------------------------------------------------------------------- | -------- | ------ |
| amount             | query      | The amount to of the invoice in SATs. Sanity check is performed against the supplied bolt11 | Yes      | double |
| conversion_percent | query      | Percent of the received amount to be automatically converted to FIAT                        | No       | double |
| description        | query      | The description for the invoice (visible on the bolt11 invoice)                             | No       | string |
| member_description | query      | Personal description of this invoice for your records                                       | No       | string |

### Responses

| Code | Description        | Schema                                      |
| ---- | ------------------ | ------------------------------------------- |
| 201  | Lightning invoice. | [ [Lightning invoice](#lightning-invoice) ] |

# Trading

## <span class="request-type__post">POST</span> Create Order

`/market/orders`

### Description

Submits a Buy/Sell order to the order book. Orders should be confirmed that they have been created either by querying the order ID that is returned in the response, or subscribing to the websockets for order updates.

You can supply your own custom_order_id for tracking purposes for you own system.

For limit orders, you can specify post_only to ensure that orders wont complete if they would have matched. The order request will fail if post_only is true, and the order would have matched.

### Parameters

| Name            | Description                       | Required | Schema  |
| --------------- | --------------------------------- | -------- | ------- |
| market          | The market name eg 'btczar'       | Yes      | string  |
| side            | The direction of the order eg buy | Yes      | string  |
| volume          | The order size in base currency   | Yes      | double  |
| ord_type        | Limit / Market                    | No       | string  |
| price           | The limit order price             | No       | double  |
| post_only       | Fail limit order if matched       | No       | boolean |
| custom_order_id | Your custom order id              | No       | string  |

### Responses

| Code | Description     | Schema          |
| ---- | --------------- | --------------- |
| 201  | Order submitted | [Order](#order) |

## <span class="request-type__get">GET</span> Order by id

`/market/orders/{id}`

### Description

Get single order details.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ------ |
| id   | path       |             | Yes      | string |

### Responses

| Code | Description    | Schema          |
| ---- | -------------- | --------------- |
| 200  | Order details. | [Order](#order) |

## <span class="request-type__post">GET</span> All orders

`/market/orders`

### Description

Get orders, result is paginated.

### Parameters

| Name       | Located in | Description                                                                   | Required | Schema  |
| ---------- | ---------- | ----------------------------------------------------------------------------- | -------- | ------- |
| market     | query      |                                                                               | No       | string  |
| base_unit  | query      |                                                                               | No       | string  |
| quote_unit | query      |                                                                               | No       | string  |
| state      | query      | Filter order by state.                                                        | No       | string  |
| limit      | query      | Limit the number of returned orders, default to 100.                          | No       | integer |
| page       | query      | Paginated page number.                                                        | No       | integer |
| order_by   | query      | If set, returned orders will be sorted in specific order, default to "desc".  | No       | string  |
| ord_type   | query      | Filter order by ord_type.                                                     | No       | string  |
| type       | query      | Filter order by type.                                                         | No       | string  |
| time_from  | query      | Timestamp in ms.If set, only orders created after the time will be returned.  | No       | integer |
| time_to    | query      | Timestamp in ms.If set, only orders created before the time will be returned. | No       | integer |

### Responses

| Code | Description                           | Schema              |
| ---- | ------------------------------------- | ------------------- |
| 200  | Get your orders, result is paginated. | [ [Order](#order) ] |

## <span class="request-type__get">GET</span> Trades

`/market/trades`

### Description

Fetch your executed trades.

### Parameters

| Name      | Located in | Description                                                  | Required | Schema  |
| --------- | ---------- | ------------------------------------------------------------ | -------- | ------- |
| market    | query      |                                                              | No       | string  |
| limit     | query      | Limit the number of returned trades. Default to 100.         | No       | integer |
| page      | query      | Paginated page number.                                       | No       | integer |
| time_from | query      | Timestamp in ms. Return trades executed after the timestamp. | No       | integer |
| time_to   | query      | Timestamp in ms. Return trades executed after the timestamp. | No       | integer |
| order_by  | query      | Order by created_at, defaults to 'desc'. [asc desc]          | No       | string  |

### Responses

| Code | Description                | Schema              |
| ---- | -------------------------- | ------------------- |
| 200  | Get your executed trades.. | [ [Trade](#trade) ] |

## <span class="request-type__post">POST</span> Cancel Order

`/market/orders/{id}/cancel`

### Description

Cancel a specific order by Order id.

### Parameters

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ------ |
| id   | path       |             | Yes      | string |

### Responses

| Code | Description      |
| ---- | ---------------- |
| 201  | Cancel an order. |

## <span class="request-type__post">POST</span> Cancel All Orders

`/market/orders/cancel`

### Description

Cancel all of your open orders.

### Parameters

| Name   | Located in | Description                                | Required | Schema |
| ------ | ---------- | ------------------------------------------ | -------- | ------ |
| market | formData   |                                            | No       | string |
| side   | formData   | Only cancel orders on this side [buy sell] | No       | string |

### Responses

| Code | Description           | Schema          |
| ---- | --------------------- | --------------- |
| 201  | Cancel all my orders. | [Order](#order) |

# Websocket

```javascript
// generateHeaders.js

const crypto = require("crypto");
const apiKey = "123456...";
const secret = "ABCDEF...";

module.exports = async () => {
  const timeStamp = new Date().getTime();
  const queryString = `${timeStamp}${apikey}`;
  const signature = crypto
    .createHmac("sha256", secret)
    .update(queryString)
    .digest("hex");

  return {
    "Content-Type": "application/json;charset=utf-8",
    "X-Auth-Apikey": apikey,
    "X-Auth-Nonce": timeStamp,
    "X-Auth-Signature": signature,
  };
};
```

The high performance websocket provides near realtime updates on the orderbook, your orders and balances.

The orderbook endpoint does not require authenticaiton, while the private endpoints (orders, trades, and balances) do require authentication.

To connect to the private endpoints on the websocket, send the authentication headers with the connection request, similar to the REST authentication, and subscribe to the streams you are interested in.
You can combine multiple subscriptions in one request, and also provide the subscriptions at the same time as connecting - ie. you only need 1x request to get fully connected.

You can also combine the public and private streams into one so that you only need to maintain 1x connection for example `?stream=btczar.ob-inc&stream=order&stream=trade&stream=balance`

## <span class="request-type__get">SUBSCRIBE</span> Orderbook

```javascript
const WebSocket = require("ws");

async function connectWssPublic() {
  try {
    const baseUrl = `wss://trade.capecrypto.com/api/v2`;
    const streams = "?stream=btczar.ob-inc&stream=ethzar.ob-inc";
    const path = `/stream/public${streams}`;

    ws = new WebSocket(`${baseUrl}${path}`);
  } catch (err) {
    console.log(err);
  }

  ws.on("open", () => {
    console.log("Connected to websocket");
  });

  ws.on("message", function message(data) {
    data = JSON.parse(data);
    console.log(JSON.stringify(data, undefined, 2));

    if (["ob-snap", "ob-inc"].includes(data)) {
      // handle all subscribed markets
      // updateOrderBook(data);
    }
  });
}

connectWssPublic();
```

`?stream={market}.ob-inc`

### Description

Subscribe to orderbook updates for the specified market. You can subscribe to multiple markets at the same time by adding each market as a param, eg

`?stream=btczar.ob-inc&ethzar.ob-inc`

Note: if the amount is empty "", the order has been removed from the orderbook

### Parameters

| Name            | Located in | Description                        | Required | Schema |
| --------------- | ---------- | ---------------------------------- | -------- | ------ |
| {market}-ob.inc | query      | Name of the market to subscribe to | yes      | string |

### Responses

| Description | Schema                                          |
| ----------- | ----------------------------------------------- |
| snapshot    | [ [Snapshot](#websocket-orderbook-snapshot) ]   |
| increment   | [ [Increment](#websocket-orderbook-increment) ] |

## <span class="request-type__get">SUBSCRIBE</span> K-Line

```javascript
const WebSocket = require("ws");

async function connectWssPublic() {
  try {
    const baseUrl = `wss://trade.capecrypto.com/api/v2`;
    const streams = "?stream=btczar.kline-15m";
    const path = `/stream/public${streams}`;

    ws = new WebSocket(`${baseUrl}${path}`);
  } catch (err) {
    console.log(err);
  }

  ws.on("open", () => {
    console.log("Connected to websocket");
  });

  ws.on("message", function message(data) {
    data = JSON.parse(data);
    console.log(JSON.stringify(data, undefined, 2));

    if (["kline-"].includes(data)) {
      // handle all subscribed markets
      // updateOrderBook(data);
    }
  });
}

connectWssPublic();
```

`?stream={market}.kline-{period}`

### Description

Subscribe to the K-LINE (OHLC) for the specified market to get updates on the OHLC for each period. Similary to the otherendpoints, you can combine streams into a single subscription request.

### Parameters

| Name     | Located in | Description                                                                                            | Required | Schema |
| -------- | ---------- | ------------------------------------------------------------------------------------------------------ | -------- | ------ |
| {market} | query      | Name of the market to subscribe to                                                                     | yes      | string |
| {period} | query      | The period to get updates at "1m", "5m", "15m", "30m", "1h", "2h", "4h", "6h", "12h", "1d", "3d", "1w" | yes      | string |

### Responses

| Name                    | Type     | Description                                        |
| ----------------------- | -------- | -------------------------------------------------- |
| {market}.kline-{period} | object   | Key name of the OHLC for the market and period     |
| OHLC for that period    | [number] | Array of Timestamp, Open, High, Low, Close, Volume |

## <span class="request-type__get">SUBSCRIBE</span> Private

```javascript
async function connectWssPrivate() {
  try {
    const baseUrl = `wss://trade.capecrypto.com/api/v2`;
    const headers = await generateHeaders();
    const streams =
      "?stream=btczar.ob-inc&stream=ethzar.ob-inc&stream=order&stream=trade&stream=balance";
    const path = `/stream/private?${streams}`;

    ws = new WebSocket(`${baseUrl}${path}`, { headers });
  } catch (err) {
    console.log(err);
  }

  ws.on("open", () => {
    console.log("Connected to websocket");
  });

  ws.on("message", (data) => {
    data = JSON.parse(data);
    console.log(JSON.stringify(data, undefined, 2));

    if (["ob-snap", "ob-inc"].includes(data)) {
      // handle all subscribed markets
      // updateOrderBook(data);
    }
    if (data["trade"]) {
      // handleOwnTradeUpdate(data);
    }
    if (data["order"]) {
      // handleOwnOrderUpdate(data);
    }

    if (data["balance"]) {
      // handleBalanceUpdate(data);
    }
  });
}

connectWssPrivate();
```

`?stream={subscription}`

Combined into 1x:
`?stream={order}&stream={trade}&stream={balance}`

### Description

Subscribe to your account, and receive updates on your placed orders, trades executed, and balance updates.

### Parameters

| Name    | Located in | Description                                   | Required | Schema |
| ------- | ---------- | --------------------------------------------- | -------- | ------ |
| order   | query      | Subscribe to order updates for your orders    | No       | string |
| trade   | query      | Subscribe to trade executions for your orders | No       | string |
| balance | query      | Subscribe to balance updates on your account  | No       | string |

### Responses

| Name      | Located in | Description                                                  | Required | Schema  |
| --------- | ---------- | ------------------------------------------------------------ | -------- | ------- |
| market    | query      |                                                              | No       | string  |
| limit     | query      | Limit the number of returned trades. Default to 100.         | No       | integer |
| page      | query      | Paginated page number.                                       | No       | integer |
| time_from | query      | Timestamp in ms. Return trades executed after the timestamp. | No       | integer |
| time_to   | query      | Timestamp in ms. Return trades executed after the timestamp. | No       | integer |
| order_by  | query      | Order by created_at, defaults to 'desc'. [asc desc]          | No       | string  |

# Models

### Ticker

| Name   | Type                        | Description                     |
| ------ | --------------------------- | ------------------------------- |
| at     | integer                     | Timestamp of ticker             |
| ticker | [TickerEntry](#tickerentry) | Ticker entry for specified time |

### TickerEntry

| Name                 | Type    | Description                                                                                   |
| -------------------- | ------- | --------------------------------------------------------------------------------------------- |
| low                  | double  | The lowest trade price during last 24 hours (0.0 if no trades executed during last 24 hours)  |
| high                 | double  | The highest trade price during last 24 hours (0.0 if no trades executed during last 24 hours) |
| open                 | double  | Price of the first trade executed 24 hours ago or less                                        |
| last                 | double  | The last executed trade price                                                                 |
| volume               | double  | Total volume of trades executed during last 24 hours                                          |
| amount               | double  | Total amount of trades executed during last 24 hours                                          |
| vol                  | double  | Alias to volume                                                                               |
| avg_price            | double  | Average price more precisely VWAP (volume weighted average price)                             |
| price_change_percent | string  | Price change over the last 24 hours                                                           |
| at                   | integer | Timestamp of ticker                                                                           |

### Orderbook entry

| Name   | Type   | Description                                                                                                           |
| ------ | ------ | --------------------------------------------------------------------------------------------------------------------- |
| price  | number | The price of the order                                                                                                |
| amount | number | The amount of the order at that price. Note: if the amount is empty "", the order has been removed from the orderbook |

### Trade

| Name            | Type    | Description                                             |
| --------------- | ------- | ------------------------------------------------------- |
| id              | string  | Trade ID.                                               |
| price           | double  | Trade price.                                            |
| amount          | double  | Trade amount.                                           |
| total           | double  | Trade total (Amount \* Price).                          |
| fee_currency    | double  | Currency user's fees were charged in.                   |
| fee             | double  | Percentage of fee user was charged for performed trade. |
| fee_amount      | double  | Amount of fee user was charged for performed trade.     |
| market          | string  | Trade market id.                                        |
| created_at      | string  | Trade create time in iso8601 format.                    |
| taker_type      | string  | Trade taker order type (sell or buy).                   |
| side            | string  | Trade side.                                             |
| order_id        | integer | Order id.                                               |
| custom_order_id | integer | Optional custom Order id if supplied.                   |

### Order

| Name             | Type              | Description                                             |
| ---------------- | ----------------- | ------------------------------------------------------- |
| id               | integer           | Unique order id.                                        |
| uuid             | string            | Unique order UUID.                                      |
| custom_order_id  | string            | Custom order id if supplied during order creation.      |
| side             | string            | Either 'sell' or 'buy'.                                 |
| ord_type         | string            | Type of order, either 'limit' or 'market'.              |
| post_only        | boolean           | If the Limit order was submitted as post_only .         |
| price            | double            | Price for 1x base unit.                                 |
| avg_price        | double            | Average execution price over all executed trades.       |
| state            | string            | Current state of the order [Order states](#states)      |
| market           | string            | The market in which the order is placed, e.g. 'btczar'. |
| created_at       | string            | Order create time in iso8601 format.                    |
| updated_at       | string            | Order updated time in iso8601 format.                   |
| origin_volume    | double            | The original amount of the order.                       |
| remaining_volume | double            | The remaining amount of the original order volume.      |
| executed_volume  | double            | The executed volume of the original order volume.       |
| trades_count     | integer           | Number of trades executed against this limit order.     |
| trades           | [[Trade](#trade)] | Array of trades executed against this order.            |

### Market

| Name             | Type   | Description                                  |
| ---------------- | ------ | -------------------------------------------- |
| id               | string | Unique market id eg 'btczar'                 |
| name             | string | Market name.                                 |
| base_unit        | string | Market Base unit.                            |
| quote_unit       | string | Market Quote unit.                           |
| min_price        | double | Minimum order price.                         |
| max_price        | double | Maximum order price.                         |
| min_amount       | double | Minimum order amount.                        |
| amount_precision | double | Precision for order amount.                  |
| price_precision  | double | Precision for order price.                   |
| state            | string | If the market is enabled for trading or not. |

### Currency

| Name                 | Type   | Description                                                           |
| -------------------- | ------ | --------------------------------------------------------------------- |
| id                   | string | Currency code.                                                        |
| name                 | string | Currency name                                                         |
| symbol               | string | Currency symbol                                                       |
| explorer_transaction | string | Currency transaction exprorer url template                            |
| explorer_address     | string | Currency address exprorer url template                                |
| type                 | string | Currency type                                                         |
| deposit_enabled      | string | Currency deposit possibility status (true/false).                     |
| withdrawal_enabled   | string | Currency withdrawal possibility status (true/false).                  |
| deposit_fee          | string | Currency deposit fee                                                  |
| min_deposit_amount   | string | Minimal deposit amount                                                |
| withdraw_fee         | string | Currency withdraw fee                                                 |
| min_withdraw_amount  | string | Minimal withdraw amount                                               |
| withdraw_limit_24h   | string | Currency 24h withdraw limit                                           |
| withdraw_limit_72h   | string | Currency 72h withdraw limit                                           |
| base_factor          | string | Currency base factor                                                  |
| precision            | string | Currency precision                                                    |
| position             | string | Position used for defining currencies order                           |
| icon_url             | string | Currency icon                                                         |
| min_confirmations    | string | Number of confirmations required for confirming deposit or withdrawal |

### Withdraw

| Name            | Type    | Description                                             |
| --------------- | ------- | ------------------------------------------------------- |
| id              | integer | The withdrawal id.                                      |
| currency        | string  | The currency code.                                      |
| type            | string  | The withdrawal type                                     |
| amount          | string  | The withdrawal amount                                   |
| fee             | double  | The exchange fee.                                       |
| blockchain_txid | string  | The withdrawal transaction id.                          |
| rid             | string  | The beneficiary ID or wallet address on the Blockchain. |
| state           | string  | The withdrawal state.                                   |
| confirmations   | integer | Number of confirmations.                                |
| note            | string  | Optional withdraw note.                                 |
| created_at      | string  | The datetime for the withdrawal.                        |
| updated_at      | string  | The datetime for the withdrawal.                        |
| done_at         | string  | The datetime when withdraw was completed                |

### Beneficiary

| Name        | Type    | Description                                                                                   |
| ----------- | ------- | --------------------------------------------------------------------------------------------- |
| uuid        | integer | Beneficiary Identifier                                                                        |
| currency    | string  | Beneficiary currency code.                                                                    |
| name        | string  | Name of the beneficiary.                                                                      |
| description | string  | A personal description of the beneficiary for your records.                                   |
| data        | json    | Bank Account details for FIAT Beneficiary in JSON format. For crypto it's blockchain address. |
| state       | string  | Pending, Active, Archived (0,1,2)                                                             |

### Deposit

| Name          | Type    | Description                               |
| ------------- | ------- | ----------------------------------------- |
| id            | integer | Unique deposit id.                        |
| currency      | string  | Deposit currency id.                      |
| amount        | double  | Deposit amount.                           |
| fee           | double  | Deposit fee.                              |
| txid          | string  | Deposit transaction id.                   |
| confirmations | integer | Number of deposit confirmations.          |
| state         | string  | Deposit state.                            |
| created_at    | string  | The datetime when deposit was created.    |
| completed_at  | string  | The datetime when deposit was completed.. |
| tid           | string  | The shared transaction ID                 |

### Account

| Name     | Type   | Description           |
| -------- | ------ | --------------------- |
| currency | string | Currency code.        |
| balance  | double | Account balance.      |
| locked   | double | Account locked funds. |

### Instant Deposit Method

| Name        | Type   | Description                            |
| ----------- | ------ | -------------------------------------- |
| id          | string | Method id, used when redeeming voucher |
| provider_id | string | ID of the provider                     |
| name        | string | Name of the provider method            |
| type        | string | Type of method [voucher instanteft]    |
| description | string | Description of this provider           |
| min_amount  | number | Minimum deposit / redemption amount    |
| max_amount  | number | Maximum deposit / redemption amount    |

### Voucher Redemption

| Name           | Type                              | Description                                                              |
| -------------- | --------------------------------- | ------------------------------------------------------------------------ |
| amount         | number                            | The amount deposited after fees.                                         |
| origin_amount  | number                            | The original amount of the voucher.                                      |
| change_voucher | [Change voucher](#change-voucher) | Optional if provider offers this, a new voucher for the remaining amount |

### Change voucher

| Name          | Type   | Description                                                       |
| ------------- | ------ | ----------------------------------------------------------------- |
| change_amount | number | Amount of change from the redeemed voucher, available to be spent |
| change_expiry | string | Expiry date of the new voucher.                                   |
| change_pin    | number | The new voucher_pin to be used for redemption.                    |

### Lightning payment

| Name               | Type                       | Description                                                     |
| ------------------ | -------------------------- | --------------------------------------------------------------- |
| bolt               | String                     | The invoice bolt11.                                             |
| address            | String                     | The address of the invoice if not bolt11.                       |
| amount             | number                     | Invoice amount paid in SAT.                                     |
| description        | String                     | Description for this invoice in the bolt11.                     |
| member_description | String                     | Private member description for this invoice provided by member. |
| state              | String                     | Payment state.                                                  |
| created_at         | String (ISO8601 formatted) | The datetime when payment was created.                          |

### Lightning invoice

| Name               | Type                       | Description                                  |
| ------------------ | -------------------------- | -------------------------------------------- |
| bolt               | String                     | The invoice in bolt11.                       |
| amount             | BigDecimal                 | Invoice amount to be paid in SAT.            |
| conversion_percent | BigDecimal                 | The amount to automatically convert to FIAT. |
| amount_msat        | BigDecimal                 | Invoice amount to be paid in Mili SAT.       |
| description        | String                     | Description for this invoice.                |
| member_description | String                     | Private member Description for this invoice. |
| state              | String                     | Payment state.                               |
| created_at         | String (ISO8601 formatted) | The datetime when invoice was created.       |
| expires_at         | String (ISO8601 formatted) | The datetime when invoice expires.           |
| paid_at            | String (ISO8601 formatted) | The datetime when invoice was paid.          |

### Websocket orderbook snapshot

| Name             | Type                                  | Description                                                                  |
| ---------------- | ------------------------------------- | ---------------------------------------------------------------------------- |
| {market}.ob-snap | Object                                | The object key for the subscription                                          |
| asks             | [[Orderbook entry](#orderbook-entry)] | Array of ask / sell orderbook entries.                                       |
| bids             | [[Orderbook entry](#orderbook-entry)] | Array of bid / buy orderbook entries.                                        |
| sequence         | integer                               | The current sequence of the orderbook, used to ensure updates are sequential |

### Websocket orderbook increment

| Name            | Type                                  | Description                                                                  |
| --------------- | ------------------------------------- | ---------------------------------------------------------------------------- |
| {market}.ob-inc | Object                                | The object key for the subscription                                          |
| asks            | [[Orderbook entry](#orderbook-entry)] | Array of ask / sell orderbook entries.                                       |
| bids            | [[Orderbook entry](#orderbook-entry)] | Array of bid / buy orderbook entries.                                        |
| sequence        | integer                               | The current sequence of the orderbook, used to ensure updates are sequential |

```json
{
  "btczar.ob-snap": {
    "asks": [
      ["841039.73", "0.156431"],
      ["845402.1", "0.004389"],
      ["900000.0", "0.00001"]
    ],
    "bids": [
      ["835956.8", "0.047756"],
      ["831012.48", "0.03749"],
      ["829990.51", "0.019462"],
      ["400000.0", "0.0001"]
    ],
    "sequence": 9712
  }
}
```

### States

| Order states | Description                                                                                      |
| ------------ | ------------------------------------------------------------------------------------------------ |
| pending      | Initial state - order has been submitted (but is not yet placed / confirmed)                     |
| wait         | Order has been placed and is available on the orderbook                                          |
| done         | Order has been completely filled                                                                 |
| cancel       | The order has been cancelled (A cancelled limit order may still have executed trades against it) |
| reject       | The order submission was rejected                                                                |

| Deposit states |
| -------------- |
| submitted      |
| canceled       |
| rejected       |
| accepted       |
| collected      |
| skipped        |

| Withdraw states |
| --------------- |
| prepared        |
| submitted       |
| rejected        |
| accepted        |
| skipped         |
| processing      |
| succeed         |
| canceled        |
| failed          |
| errored         |
| confirming      |
