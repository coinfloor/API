Please Note: Coinfloor BIST API Version 1 [can be found here](BIST.md)

# COINFLOOR BIST API (VERSION 2)

- [COINFLOOR BIST API (VERSION 2)](#coinfloor-bist-api-version-2)
  - [WHAT IS THE BIST API?](#what-is-the-bist-api)
  - [REQUEST LIMITS](#request-limits)
  - [PUBLIC DATA FUNCTIONS](#public-data-functions)
      - [TICKER](#ticker)
      - [ORDER BOOK](#order-book)
      - [TRANSACTIONS](#transactions)
      - [ESTIMATE BUY MARKET ORDER](#estimate-buy-market-order)
      - [ESTIMATE SELL MARKET ORDER](#estimate-sell-market-order)
  - [API AUTHENTICATION](#api-authentication)
      - [USER ID](#user-id)
      - [API KEY](#api-key)
      - [PASSPHRASE](#passphrase)
  - [PRIVATE FUNCTIONS](#private-functions)
      - [ACCOUNT BALANCE](#account-balance)
      - [USER TRANSACTIONS](#user-transactions)
      - [OPEN ORDERS](#open-orders)
      - [CANCEL ORDER](#cancel-order)
      - [BUY LIMIT ORDER](#buy-limit-order)
      - [SELL LIMIT ORDER](#sell-limit-order)
      - [BUY MARKET ORDER](#buy-market-order)
      - [SELL MARKET ORDER](#sell-market-order)

## WHAT IS THE BIST API?

Coinfloor offers an API gateway called <u>BIST</u> that approximately emulates [Bitstamp's v1 HTTP API](https://www.bitstamp.net/api/). This following document is provided as a quick reference to this API. For complete details, please consult the documentation of Bitstamp's v1 HTTP API and our document detailing [the differences between Bitstamp's v1 HTTP API and BIST](https://github.com/coinfloor/API/blob/master/BIST-Migration.md).

Bitstamp was not a multi-currency exchange at the time this emulation layer was implemented. Because Coinfloor is a multi-currency exchange, Coinfloor emulates the entire Bitstamp v1 API at multiple separate endpoints, one for each market that Coinfloor operates.

The version 2 of Bitstamp-like API can be accessed at the following API endpoints:

* `https://webapi.coinfloor.co.uk/v2/bist/<base>/<counter>/`

`<base>` and `<counter>` are placeholders for asset codes listed in the "Asset Type" column of [SCALE.md](SCALE.md).

These endpoint URIs replace the `https://www.bitstamp.net/api/` portion of Bitstamp's URIs.

**N.B: All examples below use the XBT/GBP asset pair on the Coinfloor UK platform, but this can be changed to the asset pair and platform of your choice.**


## REQUEST LIMITS

Coinfloor's application programming interface (API) allows our clients to access and control their accounts or view our market data using custom-written software. To protect the performance of the system, we impose certain limits. See [LIMITS.md](LIMITS.md) for details.


## PUBLIC DATA FUNCTIONS

#### TICKER
GET https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/ticker/

Returns JSON dictionary:

* last - last BTC price
* high - last 24 hours price high
* low - last 24 hours price low
* volume - last 24 hours volume
* bid - highest buy order
* ask - lowest sell order

**N.B:**

1. Please refer to the Date HTTP response header for any date or time information you may require.

2. Coinfloor returns null for any ticker fields that are not populated, as may happen if no trade has occurred in the past 24 hours.

#### ORDER BOOK
GET https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/order_book/

Returns JSON dictionary with "bids" and "asks". Each is a list of open orders and each order is represented as a list of price and amount.

**N.B:**


1. Please refer to the Date HTTP response header for any date or time information you may require.

#### TRANSACTIONS
GET https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/transactions/

This resource has been removed. Please refer to http://bitcoincharts.com/about/markets-api/ for an alternative.

#### ESTIMATE BUY MARKET ORDER

POST https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/estimate_buy_market/

Params:

* quantity - an amount of the base asset that would be bought OR
* total - an amount of the counter asset with which the base asset would be bought

Returns JSON dictionary representing estimate:

* quantity - amount of the base asset that would have been traded
* total - amount of the counter asset that would have been traded


#### ESTIMATE SELL MARKET ORDER

POST https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/estimate_sell_market/

Params:

* quantity - an amount of the base asset that would be sold OR
* total - an amount of the counter asset for which the base asset would be sold

Returns JSON dictionary representing estimate:

* quantity - amount of the base asset that would have been traded
* total - amount of the counter asset that would have been traded

## API AUTHENTICATION
All private API calls require authentication. You need to provide 3 parameters to authenticate a request:

* User ID
* API key
* Passphrase

#### USER ID
To get your User ID (sometimes referred to as a Core ID), go to the Dashboard

#### API KEY
To get your API key (referred to as a "Cookie" in the WebSocket API documentation), go to the Dashboard

#### PASSPHRASE
Is the password you use to log into Coinfloor.

**N.B:**

1. Coinfloor authenticates requests using standard HTTP Basic authentication. The username portion of the Basic credentials is constructed by concatenating the numeric user ID, a slash, and the user's API authentication cookie. The password portion is simply the user's login passphrase.

2. Many off-the-shelf HTTP clients (including cURL and all web browsers) can generate the HTTP Basic Authorization request header.

This is an example of how to make a balance request with curl:

> ``curl -k -u '[User ID]/[API key]:[Passphrase]' https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/balance/``

* ``User ID`` and ``API key`` are provided on your Coinfloor logged in Dashboard page.
* ``Passphrase`` is your Coinfloor passphrase.

## PRIVATE FUNCTIONS

#### ACCOUNT BALANCE
POST https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/balance/

Params:

* User ID - user ID
* API key - api key
* Passphrase - passphrase


Returns JSON dictionary:

* gbp_balance - GBP balance
* usd_balance - USD balance
* eur_balance - EUR balance
* pln_balance - PLN balance
* xbt_balance - XBT balance
* gbp_reserved - GBP reserved in open orders
* usd_reserved - USD reserved in open orders
* eur_reserved - EUR reserved in open orders
* pln_reserved - PLN reserved in open orders
* xbt_reserved - XBT reserved in open orders
* gbp_available- GBP available for trading
* usd_available- USD available for trading
* eur_available- EUR available for trading
* pln_available- PLN available for trading
* xbt_available - XBT available for trading

**N.B:**

1. Bitstamp returns fields whose names are constructed by appending _balance, _reserved, or _available to a currency code, which is either btc or usd. Coinfloor constructs these field names using currency codes xbt, eur,gbp, usd, and/or pln, depending on the specific API endpoint being accessed.

2. Coinfloor does not include the fee field in the response.


#### USER TRANSACTIONS
POST https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/user_transactions/

Params:

* User ID - user ID
* API key - api key
* Passphrase - passphrase
* offset - skip that many transactions before beginning to return results. Default: 0.
* limit - limit result to that many transactions. Default: 100. Maximum: 1000.
* sort - sorting by date and time (asc - ascending; desc - descending). Default: desc.

Returns descending JSON list of transactions. Every transaction (dictionary) contains:

* datetime - date and time
* id - transaction id
* type - transaction type (0 - deposit; 1 - withdrawal; 2 - market trade)
* gbp - GBP amount
* xbt - XBT amount
* xbt_gbp - GBP/XBT price
* fee - transaction fee
* order_id - executed order id

**N.B:**

1. Bitstamp returns the quantity and total of each trade in the btc and usd fields, respectively, and returns the trade price in the (undocumented) btc_usd field. Coinfloor names these fields using currency codes xbt, eur, gbp, usd, and/or pln, depending on the specific API endpoint being accessed.

#### OPEN ORDERS
POST https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/open_orders/

Params:

* User ID - user ID
* API key - api key
* Passphrase - passphrase

Returns JSON list of open orders. Each order is represented as dictionary:

* id - order id
* datetime - date and time
* type - buy or sell (0 - buy; 1 - sell)
* price - price
* amount - amount

#### CANCEL ORDER

POST https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/cancel_order/

Params:

* User ID - user ID
* API key - api key
* Passphrase - passphrase
* id - order ID

Returns 'true' if order has been found and canceled.

**N.B:**

1. If the specified order could not be found, Coinfloor returns **false**.

2. If the specified order was found and canceled, Coinfloor returns **true** with a Content-Type: text/plain header in this case.

#### BUY LIMIT ORDER

POST https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/buy/

Params:

* User ID - user ID
* API key - api key
* Passphrase - passphrase
* amount - amount
* price - price
* nonce - optional nonce parameter.  If given, it must be positive
* ttl - optional parameter that specifies a time-to-live for the order in seconds. If given, it must be positive and must not exceed 86400 (24 hours)

Returns JSON dictionary representing order:

* id - order id
* datetime - date and time
* type - buy or sell (0 - buy; 1 - sell)
* price - price
* amount - amount

**N.B:**

1. Coinfloor supports the use of tonces via the optional **nonce** request parameter. When used, each order placed must specify a tonce that is greater than that specified on any previous order since the nonce counter was last reset.  The nonce counter can only be reset via the WebSocket API. 

2. Coinfloor supports an optional time-to-live parameter on orders. If given, the placed order will be canceled automatically after the specified time if it has not already been closed otherwise. It must be positive and must not exceed 86400 (24 hours).

    a. Note that extenuating circumstances (such as heavy traffic or system upgrades) may force orders placed with times-to-live to be canceled sooner than specified. (This proviso does not apply to orders placed without a time-to-live.)

3. Coinfloor returns a datetime field with whole-second precision.

#### SELL LIMIT ORDER
POST https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/sell/

Params:

* User ID - user ID
* API key - api key
* Passphrase - passphrase
* amount - amount
* price - price
* nonce - optional nonce parameter.  If given, it must be positive
* ttl - optional parameter that specifies a time-to-live for the order in seconds. If given, it must be positive and must not exceed 86400 (24 hours)

Returns JSON dictionary representing order:

* id - order id
* datetime - date and time
* type - buy or sell (0 - buy; 1 - sell)
* price - price
* amount - amount

**N.B:**

1. Coinfloor supports the use of tonces via the optional **nonce** request parameter. When used, each order placed must specify a tonce that is greater than that specified on any previous order since the nonce counter was last reset.  The nonce counter can only be reset via the WebSocket API. 

2. Coinfloor supports an optional time-to-live parameter on orders. If given, the placed order will be canceled automatically after the specified time if it has not already been closed otherwise. It must be positive and must not exceed 86400 (24 hours).

    a. Note that extenuating circumstances (such as heavy traffic or system upgrades) may force orders placed with times-to-live to be canceled sooner than specified. (This proviso does not apply to orders placed without a time-to-live.)

3. Coinfloor returns a datetime field with whole-second precision.

#### BUY MARKET ORDER

POST https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/buy_market/

Params:

* quantity - an amount of the base asset to buy OR
* total - an amount of the counter asset with which to buy the base asset

Returns JSON dictionary representing order:

* remaining - how much of the requested quantity or total could not be traded


#### SELL MARKET ORDER 

POST https://webapi.coinfloor.co.uk/v2/bist/XBT/GBP/sell_market/

Params:

* quantity - an amount of the base asset to sell OR
* total - an amount of the counter asset for which to sell the base asset

Returns JSON dictionary representing order:

* remaining - how much of the requested quantity or total could not be traded