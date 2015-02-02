# COINFLOOR BIST API

![Coinlfoor Icon](https://coinfloor.co.uk/img/thumb_appicon.png =200x80)

## WHAT IS THE BIST API?

Coinfloor offers an API gateway called <u>BIST</u> that approximately emulates [Bitstamp HTTP API](https://www.bitstamp.net/api/). This document details the functionality, the most notable of which are related to Coinfloor's use of different currency codes.  A separate document focusses solelyonly on what is required to [migrate code written for Bitstamp’s HTTP API to BIST](https://github.com/coinfloor/API/blob/master/BIST-Migration.md).

Because Coinfloor is a multi-currency exchange and Bitstamp is not, Coinfloor must emulate the entire Bitstamp API at multiple separate endpoints, one for each market that Coinfloor operates.

The Bitstamp-like API can be accessed at the following API endpoints:

* https://webapi.coinfloor.co.uk:8090/bist/XBT/EUR/
* https://webapi.coinfloor.co.uk:8090/bist/XBT/GBP/
* https://webapi.coinfloor.co.uk:8090/bist/XBT/USD/
* https://webapi.coinfloor.co.uk:8090/bist/XBT/PLN/

These endpoint URIs replace the https://www.bitstamp.net/api/ portion of Bitstamp's URIs.

<span style="color:red;">**N.B:  All examples below use the XBT/GBP asset pair but this can be changed to the asset pair of choice.
**</span>


### WHAT IS THE BITSTAMP API?

Bitstamp application programming interface (API) allows our clients to access and control their BitStamp accounts, using custom written software.  It can located [here](https://www.bitstamp.net/api/). 



##REQUEST LIMITS

Coinfloor's application programming interface (API) allows our clients to access and control their accounts or view our market data using custom-written software. To protect the performance of the system, we impose certain limits:


| Metric                  | Limit                            |
|-------------------------|----------------------------------|
| Open Orders             | 1,000 per user                   |
| Authentication attempts | 1,000 per hour per user          |
| Order placements*       | 1,000 per 1,000 seconds per user |
| Information requests†   | 10 per 10 seconds per session    


*"Order placements" also include order cancellations.

† "Information requests" include balance inquiries, open order listings, market order estimates, and trade volume inquiries.

## PUBLIC DATA FUNCTIONS

#### TICKER
GET https://webapi.coinfloor.co.uk:8090/bist/XBT/GBP/ticker/

Returns JSON dictionary:

* last - last BTC price
* high - last 24 hours price high
* low - last 24 hours price low
* vwap - last 24 hours volume weighted average price: [vwap](http://en.wikipedia.org/wiki/Volume-weighted_average_price)
* volume - last 24 hours volume
* bid - highest buy order
* ask - lowest sell order

**N.B: **

1. Bitstamp includes an undocumented timestamp field in the returned JSON object. Coinfloor does not include this field. Please refer to the Date HTTP response header.

2. returns null for any ticker fields that are not populated, as may happen if no trade has occurred in the past 24 hours.

#### ORDER BOOK
GET https://webapi.coinfloor.co.uk:8090/bist/XBT/GBP/order_book/

Returns JSON dictionary with "bids" and "asks". Each is a list of open orders and each order is represented as a list of price and amount.

**N.B: **


1. Bitstamp includes an undocumented timestamp field in the returned JSON object. Coinfloor does not include this field. Please refer to the Date HTTP response header.


#### TRANSACTIONS
GET https://webapi.coinfloor.co.uk:8090/bist/XBT/GBP/transactions/

Params:

* time - time frame for transaction export ("minute" - 1 minute, "hour" - 1 hour). Default: hour.

Returns descending JSON list of transactions. Every transaction (dictionary) contains:

* date - unix timestamp date and time
* tid - transaction id
* price - BTC price
* amount - BTC amount

**N.B:**

1. Bitstamp encodes the date field as a string. Coinfloor encodes the datefield as an integer.


##API AUTHENTICATION
All private API calls require authentication. You need to provide 3 parameters to authenticate a request:

* API key
* Tonce
* Signature



#### API KEY
To get an API key, go to the Dashboard

####CORE ID
To get an Core ID, go to the Dashboard

####TONCE
Tonce is a regular integer number. It must be increasing with every request you make. Example: if you set tonce to 1 in your first request, you must set it to at least 2 in your second request. You are not required to start with 1. A common practice is to use [unix time](http://en.wikipedia.org/wiki/Unix_time) for that parameter.

**N.B:**

1. Bitstamp authenticates requests using an API key and an HMAC (which Bitstamp calls a "signature"). The HMAC is computed using a tonce (which Bitstamp calls a "nonce," even though it isn't one), a client ID, an API key, and a secret key as inputs. The API key, tonce, and HMAC are then passed as POST parameters.

2. Coinfloor authenticates requests using standard HTTP Basic authentication. The username portion of the Basic credentials is constructed by concatenating the numeric user ID, a slash, and the user's API authentication cookie. The password portion is simply the user's login passphrase.

3. Bitstamp passes authentication credentials in POST parameters. Custom software is needed to generate the required HMAC.

4. Coinfloor passes authentication credentials in the HTTP standard Authorization request header. Many off-the-shelf HTTP clients (including cURL and all web browsers) can generate the necessary request header.

##PRIVATE FUNCTIONS

####ACCOUNT BALANCE
POST https://webapi.coinfloor.co.uk:8090/bist/XBT/GBP/balance/

Params:

* key - API key
* core id - core id
* tonce - tonce

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


####USER TRANSACTIONS
POST https://webapi.coinfloor.co.uk:8090/bist/XBT/GBP/user_transactions/

Params:

* key - API key
* core id - core id
* tonce - tonce
* offset - skip that many transactions before beginning to return results. Default: 0.
* limit - limit result to that many transactions. Default: 100. Maximum: 1000.
* sort - sorting by date and time (asc - ascending; desc - descending). Default: desc.

Returns descending JSON list of transactions. Every transaction (dictionary) contains:

* datetime - date and time
* id - transaction id
* type - transaction type (2 - market trade)
* usd - USD amount
* btc - BTC amount
* fee - transaction fee
* order_id - executed order id

**N.B:**

1. Coinfloor currently returns only trade records. It does not return records of deposits or withdrawals.

2. Bitstamp returns the quantity and total of each trade in the btc and usd fields, respectively, and returns the trade price in the (undocumented) btc_usd field. Coinfloor names these fields using currency codes xbt, eur, gbp,usd, and/or pln, depending on the specific API endpoint being accessed.

####OPEN ORDERS
POST https://webapi.coinfloor.co.uk:8090/bist/XBT/GBP/open_orders/

Params:

* key - API key
* core id - core id
* tonce - tonce

Returns JSON list of open orders. Each order is represented as dictionary:

* id - order id
* datetime - date and time
* type - buy or sell (0 - buy; 1 - sell)
* price - price
* amount - amount

####CANCEL ORDER

POST https://webapi.coinfloor.co.uk:8090/bist/XBT/GBP/cancel_order/

Params:

* key - API key
* core id - core id
* tonce - tonce
* id - order ID

Returns 'true' if order has been found and canceled.

**N.B:**

1. If the specified order could not be found, Bitstamp returns {"error": "Order not found"}. Coinfloor returns false in this case.

2. If the specified order was found and canceled, Bitstamp returns true with a Content-Type: application/json header. Coinfloor returns true with a Content-Type: text/plainheader in this case.

####BUY LIMIT ORDER

POST https://webapi.coinfloor.co.uk:8090/bist/XBT/GBP/buy/

Params:

* key - API key
* core id - core id
* tonce - tonce
* amount - amount
* price - price
* ttl - optional parameter that specifies a time-to-live for the order in seconds. If given, it must be positive and must not exceed 86400 (24 hours)

Returns JSON dictionary representing order:

* id - order id
* datetime - date and time
* type - buy or sell (0 - buy; 1 - sell)
* price - price
* amount - amount

**N.B:**

1. Coinfloor supports the use of tonces (“time once”) on orders via the optional nonce (“number once”) request parameter (so named to emulate Bitstamp). When tonces are used, each order placed must specify a tonce that is greater than that specified on any previous order since the tonce counter was last reset. 

2. Coinfloor supports an optional time-to-live parameter on orders. If given, the placed order will be canceled automatically after the specified time if it has not already been closed otherwise. It must be positive and must not exceed 86400 (24 hours).
Note that extenuating circumstances (such as heavy traffic or system upgrades) may force orders placed with times-to-live to be canceled sooner than specified. (This proviso does not apply to orders placed without a time-to-live.)

3. Bitstamp returns a datetime field with microsecond precision. Coinfloor returns a datetime field with whole-second precision.

####SELL LIMIT ORDER
POST https://webapi.coinfloor.co.uk:8090/bist/XBT/GBP/sell/

Params:

* key - API key
* core id - core id
* tonce - tonce
* amount - amount
* price - price
* ttl - optional parameter that specifies a time-to-live for the order in seconds. If given, it must be positive and must not exceed 86400 (24 hours)

Returns JSON dictionary representing order:

* id - order id
* datetime - date and time
* type - buy or sell (0 - buy; 1 - sell)
* price - price
* amount - amount

**N.B:**

1. Coinfloor supports the use of tonces (“time once”) on orders via the optional nonce (“number once”) request parameter (so named to emulate Bitstamp). When tonces are used, each order placed must specify a tonce that is greater than that specified on any previous order since the tonce counter was last reset. 

2. Coinfloor supports an optional time-to-live parameter on orders. If given, the placed order will be canceled automatically after the specified time if it has not already been closed otherwise. It must be positive and must not exceed 86400 (24 hours).

    a. Note that extenuating circumstances (such as heavy traffic or system upgrades) may force orders placed with times-to-live to be canceled sooner than specified. (This proviso does not apply to orders placed without a time-to-live.)

3. Bitstamp returns a datetime field with microsecond precision. Coinfloor returns a datetime field with whole-second precision.

####BUY MARKET ORDER

POST https://webapi.coinfloor.co.uk:8090/bist/XBT/GBP/buy_market/

Params:

* quantity - an amount of the base asset to buy OR
* total – an amount of the counter asset to buyl

Returns JSON dictionary representing order:

* remaining - how much of the requested quantity or total could not be traded

**N.B:**

1. Bitstamp does not implement this method.

#### SELL MARKET ORDER 

POST https://webapi.coinfloor.co.uk:8090/bist/XBT/GBP/sell_market/

Params:

* quantity - an amount of the base asset to sell OR
* total – an amount of the counter asset to sell

Returns JSON dictionary representing order:

* remaining - how much of the requested quantity or total could not be traded

**N.B:**

1. Bitstamp does not implement this method.
