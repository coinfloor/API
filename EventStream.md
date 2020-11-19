# Event Stream

Version 1
* https://webapi.coinfloor.co.uk/event-stream
  
Version 2
* https://webapi.coinfloor.co.uk/v2/event-stream

Coinfloor offers an Event Stream resource that complies with the [Server-Sent Events] specification by the W3C. This resource delivers a continuous, consistent stream of events related to market and account activity on the Coinfloor platform. It is offered as a simpler alternative to Coinfloor's [WebSocket API] for clients that do not require bidirectional communication with the platform.

The Event Stream resource supports the standard `Last-Event-ID` request header to enable transiently disconnected clients to reconnect and resume the event stream without missing any events. See the [Server-Sent Events] specification for details.

## Authentication

**[[ Version 2:** The Event Stream resource may be requested without authentication, in which case only public events (order book and ticker changes) are returned. **]]**

Requests for the Event Stream resource **[[ Version 1:** must **]]** **[[ Version 2:** may **]]** authenticate using [HTTP Basic Authentication], wherein the *user-id* is the concatenation of user's numeric ID, a slash, and the user's Base64-encoded API key, and the *password* is the user's password or the Base64 encoding of the user's 28-byte private key that is derived from the user's ID and password. Authenticated requests will receive account-private events (balance changes and more details on the user's own order events) in addition to the public events.

---

## BalanceChanged
**[[ Version 1:**
```
event: BalanceChanged
data: {
data:   "asset": <integer>,
data:   "balance": <integer>
data: }
```
**]]**

**[[ Version 2:**
```
event: BalanceChanged
data: {
data:   "asset": <integer>,
data:   "available": <integer>,
data:   "reserved": <integer>
data: }
```
**]]**

* **`asset`:** *(integer)* The numeric identifier of the asset whose available balance in the user's account changed.
* **`[[ Version 1: balance ]] [[ Version 2: available ]]`:** *(integer)* The [scaled] available amount of the specified asset in the user's account.
* **`[[ Version 2: reserved`:** : (integer) The scaled reserved amount of the specified asset in the user's account. ]]
---

## OrderOpened

```
event: OrderOpened
data: {
data:   "base": <integer>,
data:   "counter": <integer>,
data:   "id": <integer>,
data:   "tonce": <integer>,
data:   "quantity": <integer>,
data:   "price": <integer>,
data:   "time": <integer>
data: }
```

* **`base`:** *(integer)* The numeric identifier of the base asset of the order.
* **`counter`:** *(integer)* The numeric identifier of the counter asset of the order.
* **`id`:** *(integer)* The numeric identifier of the order.
* **`tonce`:** *(integer or `null`, conditional)* The tonce given in the `PlaceOrder` command that opened the order, or `null` if that command did not specify a tonce. Present only if the authenticated user is the owner of the order.
* **`quantity`:** *(integer)* The [scaled] amount of the base asset that is to be traded. It is negative for a sell order and positive for a buy order.
* **`price`:** *(integer)* The [scaled] price at which the order offers to trade.
* **`time`:** *(integer)* The microsecond Unix timestamp at which the order was opened.

---

## OrdersMatched

```
event: OrdersMatched
data: {
data:   "base": <integer>,
data:   "counter": <integer>,
data:   "bid": <integer>,
data:   "bid_tonce": <integer>,
data:   "ask": <integer>,
data:   "ask_tonce": <integer>,
data:   "quantity": <integer>,
data:   "price": <integer>,
data:   "total": <integer>,
data:   "bid_rem": <integer>,
data:   "ask_rem": <integer>,
data:   "time": <integer>,
data:   "bid_base_fee": <integer>,
data:   "bid_counter_fee": <integer>,
data:   "ask_base_fee": <integer>,
data:   "ask_counter_fee": <integer>
data: }
```

* **`base`:** *(integer)* The numeric identifier of the base asset of the orders.
* **`counter`:** *(integer)* The numeric identifier of the counter asset of the orders.
* **`bid`:** *(integer, conditional)* The numeric identifier of the bid order that matched. Omitted if the bid was a market order.
* **`bid_tonce`:** *(integer, conditional)* The tonce given in the `PlaceOrder` command that opened the bid order, or `null` if that command did not specify a tonce. Present only if the authenticated user is the owner of the bid order.
* **`ask`:** *(integer, conditional)* The numeric identifier of the ask order that matched. Omitted if the ask was a market order.
* **`ask_tonce`:** *(integer, conditional)* The tonce given in the `PlaceOrder` command that opened the ask order, or `null` if that command did not specify a tonce. Present only if the authenticated user is the owner of the ask order.
* **`quantity`:** *(integer)* The [scaled] amount of the base asset that was traded.
* **`price`:** *(integer)* The [scaled] price at which the trade executed.
* **`total`:** *(integer)* The [scaled] amount of the counter asset that was traded.
* **`bid_rem`:** *(integer, conditional)* The [scaled] quantity remaining in the bid order after the trade. It is always positive. Omitted if the bid was a market order.
* **`ask_rem`:** *(integer, conditional)* The [scaled] quantity remaining in the ask order after the trade. It is always positive. Omitted if the ask was a market order.
* **`time`:** *(integer)* The microsecond Unix timestamp at which the trade executed.
* **`bid_base_fee`:** *(integer)* The [scaled] amount of the base asset that was charged to the buyer as a trade fee.
* **`bid_counter_fee`:** *(integer)* The [scaled] amount of the counter asset that was charged to the buyer as a trade fee.
    * `bid_base_fee` and `bid_counter_fee` are present only if the authenticated user is the owner of the bid order.
* **`ask_base_fee`:** *(integer)* The [scaled] amount of the base asset that was charged to the seller as a trade fee.
* **`ask_counter_fee`:** *(integer)* The [scaled] amount of the counter asset that was charged to the seller as a trade fee.
    * `ask_base_fee` and `ask_counter_fee` are present only if the authenticated user is the owner of the ask order.

---

## OrderClosed

```
event: OrderClosed
data: {
data:   "base": <integer>,
data:   "counter": <integer>,
data:   "id": <integer>,
data:   "tonce": <integer>,
data:   "quantity": <integer>,
data:   "price": <integer>
data: }
```

* **`base`:** *(integer)* The numeric identifier of the base asset of the order.
* **`counter`:** *(integer)* The numeric identifier of the counter asset of the order.
* **`id`:** *(integer)* The numeric identifier of the order.
* **`tonce`:** *(integer or `null`, conditional)* The tonce given in the `PlaceOrder` command that opened the order, or `null` if that command did not specify a tonce. Present only if the authenticated user is the owner of the order.
* **`quantity`:** *(integer)* The [scaled] amount of the base asset remaining to be traded at the time the order was closed. It is negative for a sell order and positive for a buy order.
* **`price`:** *(integer)* The [scaled] price at which the order had offered to trade.

---

## TickerChanged

```
event: TickerChanged
data: {
data:   "base": <integer>,
data:   "counter": <integer>,
data:   "last": <integer>,
data:   "bid": <integer>,
data:   "ask": <integer>,
data:   "low": <integer>,
data:   "high": <integer>,
data:   "volume": <integer>
data: }
```

* **`base`:** *(integer)* The numeric identifier of the base asset of the book whose ticker changed.
* **`counter`:** *(integer)* The numeric identifier of the counter asset of the book whose ticker changed.
* **`last`:** *(integer or `null`)* The [scaled] price at which the last trade executed on the book, or `null` if no trade has executed on the book.
* **`bid`:** *(integer or `null`)* The [scaled] price of the highest-priced bid order on the book, or `null` if the book contains no bid orders.
* **`ask`:** *(integer or `null`)* The [scaled] price of the lowest-priced ask order on the book, or `null` if the book contains no ask orders.
* **`low`:** *(integer or `null`)* The [scaled] price at which the lowest-priced trade on the book executed within the preceding 24 hours, or `null` if no trade has executed on the book in the preceding 24 hours.
* **`high`:** *(integer or `null`)* The [scaled] price at which the highest-priced trade on the book executed within the preceding 24 hours, or `null` if no trade has executed on the book in the preceding 24 hours.
* **`volume`:** *(integer)* The [scaled] total amount of the base asset that has been traded on the book within the preceding 24 hours.

---

**[[ Version 2:**

## UserTradeVolumeChanged

```
event: UserTradeVolumeChanged
data: {
data:   "asset": <integer>,
data:   "volume": <integer>
data: }
```

* **`asset`:** *(integer)* The numeric identifier of the asset whose trailing 30-day user trade volume has changed.
* **`volume`:** *(integer)* The [scaled] amount of the user's trailing 30-day trade volume in the specified asset.
  
**]]**



[HTTP Basic Authentication]: https://tools.ietf.org/html/rfc7617
    (The 'Basic' HTTP Authentication Scheme)
[Server-Sent Events]: https://www.w3.org/TR/eventsource/
[WebSocket API]: https://github.com/coinfloor/API/blob/master/WEBSOCKET-README.md
[scaled]: SCALE.md
