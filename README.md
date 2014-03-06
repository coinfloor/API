API
===

All quantities and prices transmitted via the API must have implicit scale factors applied.
Before using the API, please read [SCALE.md](SCALE.md).

---

# Authenticate

Authenticates as a user by signing a challenge.

**Authorization:** None required.

### Request

	#!json
	{
		"tag": <integer>,
		"method": "Authenticate",
		"user_id": <integer>,
		"cookie": <string>,
		"nonce": <string>,
		"signature": [
			<string>,
			<string>
		]
	}

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

`user_id` is the numeric identifier of the user who is attempting to authenticate.

`cookie` is the base64-encoded login cookie which is unique to the specified user.

`nonce` is a base64-encoded string of 16 bytes that have been randomly selected by the client.

`signature` is an array containing two base64-encoded strings, which encode the *r* and *s* components of the signature.

### Success Reply

	#!json
	{
		"tag": <integer>,
		"error_code": 0
	}

`tag` is present iff `tag` was given and non-zero in the request.

### Error Reply

	#!json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
1            | "There is no such user."
6            | "You are making authentication attempts too rapidly."
7            | "You sent an incorrect login cookie."
7            | "You sent an incorrect signature. This probably means you used a wrong passphrase."
8            | *(varies)*

---

# GetBalances

Retrieves the available balances of the authenticated user.

**Authorization:** Any authenticated user may invoke this command.

### Request

	#!json
	{
		"tag": <integer>,
		"method": "GetBalances"
	}

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

### Success Reply

	#!json
	{
		"tag": <integer>,
		"error_code": 0,
		"balances": [
			{
				"asset": <integer>,
				"balance": <integer>
			},
			⋮
		]
	}

`tag` is present iff `tag` was given and non-zero in the request.

`asset` is an asset code for which a balance is given.

`balance` is the user's available balance in the specified asset.

### Error Reply

	#!json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
6            | "You are making information requests too rapidly."
7            | "You are not authenticated."

---

# GetOrders

Retrieves the open orders of the authenticated user.

**Authorization:** Any authenticated user may invoke this command.

### Request

	#!json
	{
		"tag": <integer>,
		"method": "GetOrders"
	}

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

### Success Reply

	#!json
	{
		"tag": <integer>,
		"error_code": 0,
		"orders": [
			{
				"id": <integer>,
				"base": <integer>,
				"counter": <integer>,
				"quantity": <integer>,
				"price": <integer>,
				"time": <integer>
			},
			⋮
		]
	}

`tag` is present iff `tag` was given and non-zero in the request.

`id` is the numeric identifier of an order.

`base` and `counter` are the asset codes of the base and counter assets of the order.

`quantity` is the amount of the base asset that is to be traded. It is negative for a sell order and positive for a buy order.

`price` is the price at which the order offers to trade, scaled by a factor of 10000.

`time` is the micro-timestamp at which the order was opened.

### Error Reply

	#!json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
6            | "You are making information requests too rapidly."
7            | "You are not authenticated."

---

# EstimateMarketOrder

Simulates the execution of a market order and returns the quantity and total that would have been traded. This estimation does not take into account trading fees.

**Authorization:** None required.

### Request

	#!json
	{
		"tag": <integer>,
		"method": "EstimateMarketOrder",
		"base": <integer>,
		"counter": <integer>,
		"quantity": <integer>,
		"total": <integer>
	}

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

`base` and `counter` are the asset codes of the base and counter assets of the order.

`quantity` is the amount of base asset that is to be traded. It is negative for a sell order and positive for a buy order. This field must be omitted if `total` is supplied.

`total` is the amount of the counter asset that is to be traded. It is negative for a sell order and positive for a buy order. This field must be supplied if `quantity` is omitted.

### Success Reply

	#!json
	{
		"tag": <integer>,
		"error_code": 0,
		"quantity": <integer>,
		"total": <integer>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`quantity` is the amount of the base asset that would have been traded if the market order really had been executed. It is always positive.

`total` is the amount of the counter asset that would have been traded if the market order really had been executed. It is always positive.

### Error Reply

	#!json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
1            | "You specified an invalid asset pair."
6            | "You are making information requests too rapidly."
8            | "Quantity must not be zero."
8            | "Total must not be zero."
8            | "You must specify either quantity or total for a market order."
8            | *(varies)*

---

# PlaceOrder

Places an order to execute a specified trade. This command is used to place both limit orders and market orders.

**Authorization:** Any authenticated user may invoke this command.

### Request

	#!json
	{
		"tag": <integer>,
		"method": "PlaceOrder",
		"base": <integer>,
		"counter": <integer>,
		"quantity": <integer>,
		"price": <integer>,
		"total": <integer>
	}

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

`base` and `counter` are the asset codes of the base and counter assets of the order.

`quantity` is the amount of base asset that is to be traded. It is negative for a sell order and positive for a buy order. This field must be supplied if `price` is supplied and must be omitted if `total` is supplied.

`price` is the price at which the order offers to trade, scaled by a factor of 10000. It is optional; if omitted, the order will be executed as a market order.

`total` is the amount of the counter asset that is to be traded. It is negative for a sell order and positive for a buy order. This field must be omitted if `price` is supplied and must be supplied if `quantity` is omitted.

`quantity` | `price`  | `total`
-----------|----------|---------
supplied   | supplied | omitted
supplied   | omitted  | omitted
omitted    | omitted  | supplied

### Success Reply

	#!json
	{
		"tag": <integer>,
		"error_code": 0,
		"id": <integer>,
		"time": <integer>,
		"remaining": <integer>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`id` is the numeric identifier assigned to the opened order. It is present only if `price` was supplied in the request.

`time` is the micro-timestamp at which the order was opened. It is present only if `price` was supplied in the request.

`remaining` is the amount of the base asset (if `quantity` was supplied in the request), or the amount of the counter asset (if `total` was supplied in the request), that could not be traded, either because insufficient liquidity existed on the order book or because the user's balance was exhausted. It is present only if `price` was omitted from the request.

### Error Reply

	#!json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
1            | "You specified an invalid asset pair."
4            | "You have insufficient funds."
5            | "You have too many outstanding orders."
6            | "You are sending orders too rapidly."
7            | "You are not authenticated."
8            | "Quantity must not be zero."
8            | "Order total would overflow."
8            | "You must specify either quantity or total for a market order."
8            | *(varies)*

---

# CancelOrder

Cancels an open order belonging to the authenticated user.

**Authorization:** Any authenticated user may invoke this command.

### Request

	#!json
	{
		"tag": <integer>,
		"method": "CancelOrder",
		"id": <integer>
	}

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

`id` is the numeric identifier of the order to be canceled.

### Success Reply

	#!json
	{
		"tag": <integer>,
		"error_code": 0,
		"base": <integer>,
		"counter": <integer>,
		"quantity": <integer>,
		"price": <integer>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`base` and `counter` are the asset codes of the base and counter assets of the canceled order.

`quantity` is the amount of the base asset that was remaining to be traded when the order was canceled. It is negative for a sell order and positive for a buy order.

`price` is the price at which the canceled order had offered to trade, scaled by a factor of 10000.

### Error Reply

	#!json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
1            | "The specified order was not found."
6            | "You are sending orders too rapidly."
7            | "You are not authenticated."
8            | *(varies)*

---

# GetTradeVolume

Retrieves the 30-day trailing trade volume for the authenticated user.

**Authorization:** Any authenticated user may invoke this command.

### Request

	#!json
	{
		"tag": <integer>,
		"method": "GetTradeVolume",
		"asset": <integer>
	}

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

`asset` is the asset code of the asset whose trade volume is to be retrieved.

### Success Reply

	#!json
	{
		"tag": <integer>,
		"error_code": 0,
		"volume": <integer>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`volume` is the user's 30-day trailing trade volume in the specifed asset.

### Error Reply

	#!json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
6            | "You are making information requests too rapidly."
7            | "You are not authenticated."
8            | *(varies)*

---

# WatchOrders

Subscribes/unsubscribes the requesting client to/from the orders feed of a specified order book.
When subscribing, up to 2000 orders from the top of the book are returned in the response.

**Authorization:** None required.

### Request

	#!json
	{
		"tag": <integer>,
		"method": "WatchOrders",
		"base": <integer>,
		"counter": <integer>,
		"watch": <boolean>
	}

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

`base` and `counter` are the asset codes of the base and counter assets of the order book whose orders feed the client is to be subscribed to or unsubscribed from.

`watch` is a flag indicating whether to subscribe (**true**) or unsubscribe (**false**).

### Success Reply

	#!json
	{
		"tag": <integer>,
		"error_code": 0,
		"orders": [
			{
				"id": <integer>,
				"quantity": <integer>,
				"price": <integer>,
				"time": <integer>
			},
			⋮
		]
	}

`tag` is present iff `tag` was given and non-zero in the request.

`orders` is present iff `watch` was **true** in the request.

`id` is the numeric identifier of an order.

`quantity` is the amount of the base asset that is to be traded. It is negative for a sell order and positive for a buy order.

`price` is the price at which the order offers to trade, scaled by a factor of 10000.

`time` is the micro-timestamp at which the order was opened.

### Error Reply

	#!json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
1            | "You specified an invalid asset pair."
1            | "You are not watching the order book for the specified asset pair."
2            | "You are already watching the order book for the specified asset pair."

---

# WatchTicker

Subscribes/unsubscribes the requesting client to/from the ticker feed of a specified order book.
When subscribing, the current ticker values of the book are returned in the response.

**Authorization:** None required.

### Request

	#!json
	{
		"tag": <integer>,
		"method": "WatchTicker",
		"base": <integer>,
		"counter": <integer>,
		"watch": <boolean>
	}

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

`base` and `counter` are the asset codes of the base and counter assets of the order book whose ticker feed the client is to be subscribed to or unsubscribed from.

`watch` is a flag indicating whether to subscribe (**true**) or unsubscribe (**false**).

### Success Reply

	#!json
	{
		"tag": <integer>,
		"error_code": 0,
		"last": <integer>,
		"bid": <integer>,
		"ask": <integer>,
		"low": <integer>,
		"high": <integer>,
		"volume": <integer>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`last`, `bid`, `ask`, `low`, `high`, and `volume` are present iff `watch` was **true** in the request.

`last` is the price at which the last trade in the specified order book executed, scaled by a factor of 10000, or **null** if no such trade has yet executed.

`bid` and `ask` are the highest bid price and lowest ask price in the specified order book, respectively, scaled by a factor of 10000, or **null** if there is no such order.

`low` and `high` are the lowest and highest prices at which any trade executed in the specified order book in the trailing 24-hour period, scaled by a factor of 10000, or **null** if no such trade executed in that period.

`volume` is the quantity of the base asset that has been traded in the specified order book in the trailing 24-hour period.

### Error Reply

	#!json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
1            | "You specified an invalid asset pair."
1            | "You are not watching the ticker for the specified asset pair."
2            | "You are already watching the ticker for the specified asset pair."

---

# BalanceChanged

One of the authenticated user's balances has changed.

**Recipients:** Authenticated users receive notice of changes to their own balances.

### Notification

	#!json
	{
		"notice": "BalanceChanged",
		"asset": <integer>,
		"balance": <integer>
	}

`asset` is the asset code of the balance that changed.

`balance` is the user's new available balance in the specified asset.

---

# OrderOpened

A new order has been opened.

**Recipients:** Authenticated users receive notice of their own orders. Users that have subscribed to the orders feed of an order book receive notice of all orders in that book.

### Notification

	#!json
	{
		"notice": "OrderOpened",
		"id": <integer>,
		"base": <integer>,
		"counter": <integer>,
		"quantity": <integer>,
		"price": <integer>,
		"time": <integer>
	}

`id` is the numeric identifier of the order.

`base` and `counter` are the asset codes of the base and counter assets of the order.

`quantity` is the amount of the base asset that is to be traded. It is negative for a sell order and positive for a buy order.

`price` is the price at which the order offers to trade, scaled by a factor of 10000.

`time` is the micro-timestamp at which the order was opened.

---

# OrdersMatched

Two orders matched, resulting in a trade.

**Recipients:** Authenticated users receive notice of their own orders. Users that have subscribed to the orders feed of an order book receive notice of all orders in that book.

### Notification

	#!json
	{
		"notice": "OrdersMatched",
		"bid": <integer>,
		"ask": <integer>,
		"base": <integer>,
		"counter": <integer>,
		"quantity": <integer>,
		"price": <integer>,
		"total": <integer>,
		"bid_rem": <integer>,
		"ask_rem": <integer>,
		"time": <integer>,
		"bid_base_fee": <integer>,
		"bid_counter_fee": <integer>,
		"ask_base_fee": <integer>,
		"ask_counter_fee": <integer>
	}

`bid` and `ask` are the numeric identifiers of the bid and ask orders, respectively, that matched. Either (but not both) may be omitted if the corresponding side of the trade was a market order.

`base` and `counter` are the asset codes of the base and counter assets of the orders.

`quantity` is the amount of the base asset that was traded. It is always positive.

`price` is the price at which the trade executed, scaled by a factor of 10000.

`total` is the amount of the counter asset that was traded. It is always positive.

`bid_rem` and `ask_rem` are the quantities remaining in the bid and ask orders, respectively, after the trade. Either (but not both) may be omitted if the corresponding side of the trade was a market order.

`time` is the micro-timestamp at which the trade executed.

`bid_base_fee`, `bid_counter_fee`, `ask_base_fee`, and `ask_counter_fee` are the fees levied on the buyer and seller, respectively, in the base and counter assets, respectively. These fields are present only if the recipient of the notification is the buyer or seller, respectively.

---

# OrderClosed

An order was removed from the order book.

**Recipients:** Authenticated users receive notice of their own orders. Users that have subscribed to the orders feed of an order book receive notice of all orders in that book.

### Notification

	#!json
	{
		"notice": "OrderClosed",
		"id": <integer>,
		"base": <integer>,
		"counter": <integer>,
		"quantity": <integer>,
		"price": <integer>,
	}

`id` is the numeric identifier of the order.

`base` and `counter` are the asset codes of the base and counter assets of the order.

`quantity` is the amount of the base asset that was remaining to be traded when the order was closed. It is negative for a sell order and positive for a buy order. It is zero if the order was completely fulfilled.

`price` is the price at which the order had offered to trade, scaled by a factor of 10000.

---

# TickerChanged

The ticker for an order book changed.

**Recipients:** Users that have subscribed to the ticker feed of an order book receive notice of all ticker changes in that book.

### Notification

	#!json
	{
		"notice": "TickerChanged",
		"base": <integer>,
		"counter": <integer>,
		"last": <integer>,
		"bid": <integer>,
		"ask": <integer>,
		"low": <integer>,
		"high": <integer>,
		"volume": <integer>
	}

`base` and `counter` are the asset codes of the base and counter assets of the order book whose ticker changed.

`last` is the price at which the last trade in the specified order book executed, scaled by a factor of 10000, or **null** if no such trade has yet executed. It is present only if it has changed since the previous notice in which it was present.

`bid` and `ask` are the highest bid price and lowest ask price in the specified order book, respectively, scaled by a factor of 10000, or **null** if there is no such order. Each is present only if it has changed since the previous notice in which it was present.

`low` and `high` are the lowest and highest prices at which any trade executed in the specified order book in the trailing 24-hour period, scaled by a factor of 10000, or **null** if no such trade executed in that period. Each is present only if it has changed since the previous notice in which it was present.

`volume` is the quantity of the base asset that has been traded in the specified order book in the trailing 24-hour period. It is present only if it has changed since the previous notice in which it was present.
