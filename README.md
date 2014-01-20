API
===

## Licence
```
Copyright 2014 Coinfloor LTD.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
---
## Notes

All C\+\+ structures are tightly packed (i.e., no implicit padding).

All multi-byte integers in C\+\+ structures are serialized in native byte order (e.g., little-endian if the engine is running on x86_64).

All values entered into or received from the trade engine need to have the quantity, price or total information scaled. Before placing trades or using the API, please read the [Scale.md File](Scale.md)

---

# Authenticate

Authenticates as a user by signing a challenge.

**Authorization:** None required.

### Request

```json
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
```

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

`user_id` is the numeric identifier of the user who is attempting to authenticate.

`cookie` is the base64-encoded login cookie which is unique to the specified user.

`nonce` is a base64-encoded string of 16 bytes that have been randomly selected by the client.

`signature` is an array containing two base64-encoded strings, which encode the *r* and *s* components of the signature.

### Success Reply

```json
	{
		"tag": <integer>,
		"error_code": 0
	}
```
`tag` is present iff `tag` was given and non-zero in the request.

### Error Reply

```json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}
```

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

```json
	{
		"tag": <integer>,
		"method": "GetBalances"
	}

```

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

### Success Reply

```json
	{
		"tag": <integer>,
		"error_code": 0,
		"balances": [
			{
				"asset": <integer>,
				"balance": <integer>
			},
		
		]
	}
```

`tag` is present iff `tag` was given and non-zero in the request.

`asset` is an asset code for which a balance is given.

`balance` is the user's available balance in the specified asset.

### Error Reply

```json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}

```

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

```json
	{
		"tag": <integer>,
		"method": "GetOrders"
	}
```

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

### Success Reply

```json
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
		]
	}
```

`tag` is present iff `tag` was given and non-zero in the request.

`id` is the numeric identifier of an order.

`base` and `counter` are the asset codes of the base and counter assets of the order.

`quantity` is the amount of the base asset that is to be traded. It is negative for a sell order and positive for a buy order.

`price` is the price at which the order offers to trade, scaled by a factor of 10000.

`time` is the micro-timestamp at which the order was opened.

### Error Reply

```json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}

```

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
6            | "You are making information requests too rapidly."
7            | "You are not authenticated."

---

# PlaceOrder

Places an order to execute a specified trade. This command is used to place both limit orders and market orders.

**Authorization:** Any authenticated user may invoke this command.

### Request

```json
	{
		"tag": <integer>,
		"method": "PlaceOrder",
		"base": <integer>,
		"counter": <integer>,
		"quantity": <integer>,
		"price": <integer>,
		"total": <integer>
	}
```

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

```json
	{
		"tag": <integer>,
		"error_code": 0,
		"id": <integer>,
		"time": <integer>,
		"remaining": <integer>
	}
```

`tag` is present iff `tag` was given and non-zero in the request.

`id` is the numeric identifier assigned to the opened order. It is present only if `price` was supplied in the request.

`time` is the micro-timestamp at which which the order was opened. It is present only if `price` was supplied in the request.

`remaining` is the amount of the base asset (if `quantity` was supplied in the request), or the amount of the counter asset (if `total` was supplied in the request), that could not be traded, either because insufficient liquidity existed on the order book or because the user's balance was exhausted. It is present only if `price` was omitted from the request.

### Error Reply

```json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}
```

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
1            | "You specified an invalid asset pair."
4            | "You have insufficient funds."
5            | "You have too many outstanding orders."
6            | "You are sending orders too rapidly."
7            | "You are not authenticated."
8            | "Quantity must not be zero."
8            | "You must specify either quantity or total for a market order."
8            | *(varies)*

---

# CancelOrder

Cancels an open order belonging to the authenticated user.

**Authorization:** Any authenticated user may invoke this command.

### Request

```json
	{
		"tag": <integer>,
		"method": "CancelOrder",
		"id": <integer>
	}
```

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

`id` is the numeric identifier of the order to be canceled.

### Success Reply

```json
	{
		"tag": <integer>,
		"error_code": 0,
		"base": <integer>,
		"counter": <integer>,
		"quantity": <integer>,
		"price": <integer>
	}
```

`tag` is present iff `tag` was given and non-zero in the request.

`base` and `counter` are the asset codes of the base and counter assets of the canceled order.

`quantity` is the amount of the base asset that was remaining to be traded at the time the order was canceled. It is negative for sell orders and positive for buy orders.

`price` is the price at which the canceled order had offered to trade, scaled by a factor of 10000.

### Error Reply

```json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}
```

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
1            | "The specified order was not found."
6            | "You are sending orders too rapidly."
7            | "You are not authenticated."
8            | *(varies)*

---

# WatchOrders

Subscribes/unsubscribes the requesting client to/from the orders feed of a specified order book.

### Request

```json
	{
		"tag": <integer>,
		"method": "WatchOrders",
		"base": <integer>,
		"counter": <integer>,
		"watch": <boolean>
	}
```

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

`base` and `counter` and the asset codes of the base and counter assets of the order book whose orders feed the client is to be subscribed to or unsubscribed from.

`watch` is a flag indicating whether to subscribe (**true**) or unsubscribe (**false**).

### Success Reply

```json
	{
		"tag": <integer>,
		"error_code": 0
	}
```

`tag` is present iff `tag` was given and non-zero in the request.

### Error Reply

```json

	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}
```

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
1            | "You specified an invalid asset pair."
1            | "You are not watching the order book for the specified asset pair."
2            | "You are already watching the order book for the specified asset pair."

---

# WatchTicker

Subscribes/unsubscribes the requesting client to/from the ticker feed of a specified order book.

### Request

```json

	{
		"tag": <integer>,
		"method": "WatchTicker",
		"base": <integer>,
		"counter": <integer>,
		"watch": <boolean>
	}
```

`tag` is optional. Iff given and non-zero, it will be echoed in the reply.

`base` and `counter` and the asset codes of the base and counter assets of the order book whose ticker feed the client is to be subscribed to or unsubscribed from.

`watch` is a flag indicating whether to subscribe (**true**) or unsubscribe (**false**).

### Success Reply

```json

	{
		"tag": <integer>,
		"error_code": 0
	}
```

`tag` is present iff `tag` was given and non-zero in the request.

### Error Reply

```json
	{
		"tag": <integer>,
		"error_code": <integer>,
		"error_msg": <string>
	}
```

`tag` is present iff `tag` was given and non-zero in the request.

`error_code` | `error_msg`
-------------|------------------------------------------------------------------
1            | "You specified an invalid asset pair."
1            | "You are not watching the ticker for the specified asset pair."
2            | "You are already watching the ticker for the specified asset pair."
