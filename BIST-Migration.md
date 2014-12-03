# Migrating from Bitstamp's API to Coinfloor's Bitstamp-like API

Coinfloor offers an API gateway that approximately emulates [Bitstamp's HTTP API](https://www.bitstamp.net/api/). This document details the differences, the most notable of which are related to Coinfloor's use of different currency codes.

Because Coinfloor is a multi-currency exchange and Bitstamp is not, Coinfloor must emulate the entire Bitstamp API at multiple separate endpoints, one for each market that Coinfloor operates.

The Bitstamp-like API can be accessed at the following API endpoints:

* `https://webapi.coinfloor.co.uk:8090/bist/XBT/EUR/`
* `https://webapi.coinfloor.co.uk:8090/bist/XBT/GBP/`
* `https://webapi.coinfloor.co.uk:8090/bist/XBT/USD/`
* `https://webapi.coinfloor.co.uk:8090/bist/XBT/PLN/`

These endpoint URIs replace the `https://www.bitstamp.net/api/` portion of Bitstamp's URIs.


## Public Data Functions


### Ticker

#### Functional differences

None.

#### Structural differences

* Bitstamp includes an undocumented `timestamp` field in the returned JSON object. Coinfloor does not include this field. Please refer to the `Date` HTTP response header.
* Coinfloor returns **null** for any ticker fields that are not populated, as may happen if no trade has occurred in the past 24 hours.


### Order Book

#### Functional differences

None.

#### Structural differences

* Bitstamp includes an undocumented `timestamp` field in the returned JSON object. Coinfloor does not include this field. Please refer to the `Date` HTTP response header.


### Transactions

#### Functional differences

None.

#### Structural differences

* Bitstamp encodes the `date` field as a string, even though its value is a Unix timestamp. Coinfloor encodes the `date` field as an integer.


### EUR/USD Conversion Rate

Coinfloor does not implement this method.


## API Authentication

### Functional differences

* Bitstamp authenticates requests using an API key and an HMAC (which Bitstamp calls a "signature"). The HMAC is computed using a tonce (which Bitstamp calls a "nonce," even though it isn't one), a client ID, an API key, and a secret key as inputs. The API key, tonce, and HMAC are then passed as POST parameters.
* Coinfloor authenticates requests using standard HTTP Basic authentication. The username portion of the Basic credentials is constructed by concatenating the numeric user ID, a slash, and the user's API authentication cookie. The password portion is simply the user's login passphrase.

### Structural differences

* Bitstamp passes authentication credentials in POST parameters. Custom software is needed to generate the required HMAC.
* Coinfloor passes authentication credentials in the HTTP standard `Authorization` request header. Many off-the-shelf HTTP clients (including cURL and all web browsers) can generate the necessary request header.


## Private Functions


### Account Balance

#### Functional differences

* Coinfloor does not return information about trading fees.

#### Structural differences

* Bitstamp returns fields whose names are constructed by appending `_balance`, `_reserved`, or `_available` to a currency code, which is either `btc` or `usd`. Coinfloor constructs these field names using currency codes `xbt`, `eur`, `gbp`, `usd`, and/or `pln`, depending on the specific API endpoint being accessed.
* Coinfloor does not include the `fee` field in the response.


### User Transactions

#### Functional differences

* Coinfloor returns only trade records. It does not return records of deposits or withdrawals.

#### Structural differences

* Bitstamp returns the quantity and total of each trade in the `btc` and `usd` fields, respectively, and returns the trade price in the (undocumented) `btc_usd` field. Coinfloor names these fields using currency codes `xbt`, `eur`, `gbp`, `usd`, and/or `pln`, depending on the specific API endpoint being accessed.


### Open Orders

#### Functional differences

None.

#### Structural differences

None.


### Cancel Order

#### Functional differences

None.

#### Structural differences

* If the specified order could not be found, Bitstamp returns `{"error": "Order not found"}`. Coinfloor returns `false` in this case.
* If the specified order was found and canceled, Bitstamp returns `true` with a `Content-Type: application/json` header, even though `true` is not a valid JSON encoding. Coinfloor returns `true` with a `Content-Type: text/plain` header in this case.


### Buy/Sell Limit Order

#### Functional differences

* Coinfloor supports the use of tonces on orders via the optional `nonce` request parameter (so named to emulate Bitstamp). When tonces are used, each order placed must specify a tonce that is greater than that specified on any previous order since the tonce counter was last reset. Placing an order without a tonce resets the tonce counter.
* Coinfloor supports an optional time-to-live parameter on orders. If given, the placed order will be canceled automatically after the specified time if it has not already been closed otherwise.
    * Note that extenuating circumstances (such as heavy traffic or system upgrades) may force orders placed with times-to-live to be canceled sooner than specified. (This proviso does not apply to orders placed without a time-to-live.)

#### Structural differences

* Bitstamp returns a `datetime` field with microsecond precision. Coinfloor returns a `datetime` field with whole-second precision.
* Coinfloor accepts an optional `ttl` request parameter that specifies a time-to-live for the order in seconds. If given, it must be positive and must not exceed 86400 (24 hours).


### Buy/Sell Market Order

Bitstamp does not implement this method.

#### Structure

```
POST <endpoint>/buy_market/
POST <endpoint>/sell_market/
```

##### Request parameters:

* Exactly one of:
  * `quantity` – an amount of the base asset to buy or sell
  * `total` – an amount of the counter asset to buy or sell

##### Response:

Returns a JSON object containing a single field `remaining`, which communicates how much of the requested quantity or total could not be traded.


### Withdrawal Requests

Coinfloor does not implement this method.


### Bitcoin Withdrawal

Coinfloor does not implement this method.


### Bitcoin Deposit Address

Coinfloor does not implement this method.


### Unconfirmed Bitcoin Deposits

Coinfloor does not implement this method.


### Ripple Withdrawal

Coinfloor does not implement this method.


### Ripple Deposit Address

Coinfloor does not implement this method.
