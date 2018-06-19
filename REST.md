# REST API

* https://webapi.coinfloorex.com:8090/

For clients who do not wish to take advantage of Coinfloor's native [WebSocket API][], Coinfloor offers a RESTful API that implements much of the same functionality.

## Authentication

Authenticated requests to the REST API use [HTTP Basic Authentication][], wherein the *user-id* is the concatenation of user's numeric ID, a slash, and the user's Base64-encoded API key, and the *password* is the user's password or the Base64 encoding of the user's 28-byte private key that is derived from the user's ID and password.

The public market data methods do not require authentication.

---

## Common Record Types

### `<balance>`

	{
		"id": <integer>,
		"available": <integer>,
		"reserved": <integer>
	}

* **`id`:** *(integer)* The numeric asset code of an asset in which the user's balances are reported.
* **`available`:** *(integer)* The [scaled][] amount of the user's available balance in the identified asset.
* **`reserved`:** *(integer)* The [scaled][] amount of the user's reserved balance in the identified asset.

### `<order>`

	{
		"id": <integer>,
		"base": <integer>,
		"counter": <integer>,
		"quantity": <integer>,
		"price": <integer>,
		"time": <integer>
	}

* **`id`:** *(integer)* The numeric identifier of an order.
* **`base`:** *(integer)* The numeric identifier of the base asset of the order.
* **`counter`:** *(integer)* The numeric identifier of the counter asset of the order.
* **`quantity`:** *(integer)* The [scaled][] amount of the base asset that the order offers to trade. It is negative for a sell order and positive for a buy order.
* **`price`:** *(integer)* The [scaled][] price at which the order offers to trade.
* **`time`:** *(integer)* The micro-timestamp at which the order was opened.

### `<ticker>`

	{
		"base": <integer>,
		"counter": <integer>,
		"last": <integer>|null,
		"bid": <integer>|null,
		"ask": <integer>|null,
		"low": <integer>|null,
		"high": <integer>|null,
		"volume": <integer>
	}

* **`base`:** *(integer)* The numeric identifier of the base asset of the ticker.
* **`counter`:** *(integer)* The numeric identifier of the counter asset of the ticker.
* **`last`:** *(integer or `null`)* The [scaled][] price of the last trade in the identified market, or `null` if no trade has occurred in the identified market.
* **`bid`:** *(integer or `null`)* The [scaled][] price of the highest-priced open bid order in the identified market, or `null` if no bid orders are open in the identified market.
* **`ask`:** *(integer or `null`)* The [scaled][] price of the lowest-priced open ask order in the identified market, or `null` if no ask orders are open in the identified market.
* **`low`:** *(integer or `null`)* The [scaled][] price of the lowest-priced trade in the identified market in the preceding 24-hour period, or `null` if no trade has occurred in the identified market in the preceding 24-hour period.
* **`high`:** *(integer or `null`)* The [scaled][] price of the highest-priced trade in the identified market in the preceding 24-hour period, or `null` if no trade has occurred in the identified market in the preceding 24-hour period.
* **`volume`:** *(integer)* The [scaled][] quantity of the base asset that has traded in the identified market in the preceding 24-hour period.

### `<trade>`

	{
		"time": <integer>,
		"base": <integer>,
		"counter": <integer>,
		"quantity": <integer>,
		"price": <integer>,
		"total": <integer>,
		"base_fee": <integer>,
		"counter_fee": <integer>,
		"order_id": <integer>|null
	}

* **`time`:** *(integer)* The micro-timestamp at which the trade occurred.
* **`base`:** *(integer)* The numeric identifier of the base asset that was traded.
* **`counter`:** *(integer)* The numeric identifier of the counter asset that was traded.
* **`quantity`:** *(integer)* The [scaled][] amount of the base asset that was traded.
* **`price`:** *(integer)* The [scaled][] price at which the trade occurred.
* **`total`:** *(integer)* The [scaled][] amount of the counter asset that was traded.
* **`base_fee`:** *(integer)* The [scaled][] amount of the base asset that was levied as a trading fee.
* **`counter_fee`:** *(integer)* The [scaled][] amount of the counter asset that was levied as a trading fee.
* **`order_id`:** *(integer or `null`)* The numeric identifier of the user's limit order that participated in the trade, or `null` if the user caused the trade by a market order.

---

## `GET /tickers/`

**Authentication not required.**
Returns the tickers of all available markets.

### Request

	GET /tickers/ HTTP/1.1

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	[<ticker>, …]

---

## `GET /tickers/<base>:<counter>`

**Authentication not required.**
Returns the ticker of the specified market.

### Request

	GET /tickers/<base>:<counter> HTTP/1.1

* **`base`:** *(integer)* The numeric identifier of the base asset of the market whose ticker is to be returned.
* **`counter`:** *(integer)* The numeric identifier of the counter asset of the market whose ticker is to be returned.

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	<ticker>

### Errors

* If the specified market was not found, then this method returns:

		HTTP/1.1 404 Not Found

---

## `GET /depth/<base>:<counter>`

**Authentication not required.**
Returns the amount of open interest at each of the top 20 price levels on each side of the specified order book.

A facility to retrieve full order book listings is intentionally omitted from this API, as such facilities have a tendency to be abused through rapid polling. If you require order book listings, please use the [WebSocket API][], which delivers snapshots followed by incremental updates.

### Request

	GET /depth/<base>:<counter>

* **`base`:** *(integer)* The numeric identifier of the base asset of the market whose depth information is to be returned.
* **`counter`:** *(integer)* The numeric identifier of the counter asset of the market whose depth information is to be returned.

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	{
		"bids": [[<price>, <quantity>], …],
		"asks": [[<price>, <quantity>], …]
	}

* **`price`:** *(integer)* The [scaled][] price level at which market depth is reported.
* **`quantity`:** *(integer)* The [scaled][] amount of the base asset representing the total open interest at the specified price level.

### Errors

* If the specified market was not found, then this method returns:

		HTTP/1.1 404 Not Found

---

## `GET /balances/`

**Authentication required.**
Returns the user's available and reserved balances in all assets.

### Request

	GET /balances/ HTTP/1.1

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	[<balance>, …]

---

## `GET /balances/<id>`

**Authentication required.**
Returns the user's available and reserved balances in the specified asset.

### Request

	GET /balances/<id> HTTP/1.1

* **`id`:** *(integer)* The numeric asset code of the asset in which to report the user's balances.

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	<balance>

### Errors

* If the specified asset was not found, then this method returns:

		HTTP/1.1 404 Not Found
		Content-Length: 0

---

## `GET /orders/`

**Authentication required.**
Returns the user's open limit orders.

### Request

	GET /orders/ HTTP/1.1

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	[<order>, …]

---

## `GET /orders/<id>`

**Authentication required.**
Returns the specified limit order of the user.

### Request

	GET /order/<id> HTTP/1.1

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	<order>

### Errors

* If the specified order was not found, then this method returns:

		HTTP/1.1 404 Not Found
		Content-Length: 0

---

## `POST /orders/`

**Authentication required.**
Places a limit order or executes a market order.

### Request

	POST /orders/ HTTP/1.1
	Content-Type: application/x-www-form-urlencoded
	
	base=<integer>&counter=<integer>[&price=<integer>][&quantity=<integer>|&total=<integer>]

* **`base`:** *(integer)* The numeric identifier of the base asset of the order.
* **`counter`:** *(integer)* The numeric identifier of the counter asset of the order.
* **`price`:** *(integer, optional)* The [scaled][] price at which to offer to trade. If omitted, the submitted order will be executed immediately as a market order.
* **`quantity`:** *(integer, conditional)* The [scaled][] amount of the base asset to be traded. Negative for a sell order or positive for a buy order. Required if `price` is supplied or `total` is omitted.
* **`total`:** *(integer, conditional)* The [scaled][] amount of the counter asset to be traded. Negative for a sell order or positive for a buy order. Required if `quantity` is omitted; forbidden if `quantity` is supplied.

`quantity` | `price`  | `total`
-----------|----------|---------
supplied   | supplied | omitted
supplied   | omitted  | omitted
omitted    | omitted  | supplied

### Response

The response for a limit order (`price` supplied) is:

	HTTP/1.1 201 Created
	Location: <id>
	Content-Location: <id>
	Content-Type: application/json; charset=US-ASCII
	
	<order>

* **`id`:** *(integer)* The numeric identifier of the newly placed limit order.

The response for a market order (`price` omitted) is:

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	{"remaining":<integer>}

* **`remaining`:** *(integer)* The [scaled][] amount of the base asset (if `quantity` was supplied in the request), or the [scaled][] amount of the counter asset (if `total` was supplied in the request), that could not be traded, either because insufficient liquidity existed on the order book or because the user's balance was exhausted.

---

## `DELETE /orders/`

**Authentication required.**
Cancels all of the user's open limit orders.

### Request

	DELETE /orders/ HTTP/1.1

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	[<order>, …]

The response contains a snapshot of all of the orders that were canceled.

---

## `DELETE /orders/<id>`

**Authentication required.**
Cancels the specified limit order of the user.

### Request

	DELETE /orders/<id> HTTP/1.1

* **`id`:** *(integer)* The numeric identifier of the order to be canceled.

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	<order>

The response contains a snapshot of the order that was canceled.

### Errors

* If the specified order was not found, then this method returns:

		HTTP/1.1 404 Not Found
		Content-Length: 0

---

## `GET /trades/`

**Authentication required.**
Returns historical records of the user's past trades.

### Request

	GET /trades/[?since=<integer>][&until=<integer>][&sort=asc|desc][&limit=<integer>] HTTP/1.1

* **`since`:** *(integer, optional)* A micro-timestamp by which to constrain the set of returned trade records. If supplied, only records of trades that occurred *strictly after* this time will be returned.
* **`until`:** *(integer, optional)* A micro-timestamp by which to constrain the set of returned trade records. If supplied, only records of trades that occurred *strictly before* this time will be returned.
* **`sort`:** *(string, optional)* The order in which to sort records, either `asc` for ascending order or `desc` for descending order. In addition to causing the returned records to be sorted as specified, this also affects which records are selected if not all records in the specified time range are returned. If omitted, the default sort order is ascending if `since` is supplied, or else descending.
* **`limit`:** *(integer, optional)* The maximum number of trade records to return. Note, however, that the number of records returned may be less than this limit even if more records exist in the specified time range, but at least one record is guaranteed to be returned if any records exist in the specified time range. If omitted, there is no particular limit imposed by the request.

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	[<trade>, …]

---

## `GET /trades/<time>`

**Authentication required.**
Returns the historical record of a particular past trade of the user.

### Request

	GET /trades/<time> HTTP/1.1

* **`time`:** *(integer)* The micro-timestamp of the trade to return.

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	<trade>

### Errors

* If the specified trade was not found, then this method returns:

		HTTP/1.1 404 Not Found
		Content-Length: 0


[HTTP Basic Authentication]: https://tools.ietf.org/html/rfc7617
    (The 'Basic' HTTP Authentication Scheme)
[WebSocket API]: WEBSOCKET-README.md
[scaled]: SCALE.md
