# Futures API Specification

**API endpoint URL: `https://webapi.coinfloorex.com:8090/`**

[scaled]: SCALE.md

## Authentication

All method calls require [HTTP Basic authentication][]. The username portion of the Basic credentials is constructed by concatenating the numeric user ID, a slash, and the user's API authentication cookie. The password portion is simply the user's login passphrase.

[HTTP Basic authentication]: https://tools.ietf.org/html/rfc2617#section-2

---

## Common Record Types

### `<collateral>`

	{
		"asset_id": <integer>,
		"available": <integer>
	}

* **`asset_id`:** *(integer)* The numeric asset code of an asset in which the user's available collateral is reported.
* **`available`:** *(integer)* The [scaled][] amount of the user's available collateral in the identified asset.

### `<offer>`

	{
		"id": <integer>,
		"created": <integer>,
		"rescinded": <integer>,
		"asset_id": <integer>,
		"amount": <integer>,
		"min_amount": <integer>,
		"max_amount": <integer>,
		"term": <integer>,
		"initial_margin": <integer>,
		"maintenance_margin": <integer>,
		"apr": <integer>,
		"qualifiers": ["<string>", …]
	}

* **`id`:** *(integer)* The numeric identifier of a loan offer.
* **`created`:** *(integer)* The micro-timestamp at which this offer was created.
* **`rescinded`:** *(integer, conditional)* The micro-timestamp at which this offer was rescinded. Present only if this offer is rescinded.
* **`asset_id`:** *(integer)* The numeric asset code of the asset in which a loan is offered.
* **`amount`:** *(integer)* The [scaled][] amount of the identified asset that is available for borrowing against this offer.
* **`min_amount`:** *(integer, conditional)* The [scaled][] minimum amount that may be borrowed against this offer in any one loan. If absent, there is no minimum.
* **`max_amount`:** *(integer, conditional)* The [scaled][] maximum amount that may be borrowed across all of the user's loans against this offer. If absent, there is no maximum.
* **`term`:** *(integer)* The maximum term of the offered loan, in seconds.
* **`initial_margin`:** *(integer)* The initial margin ratio of the offered loan, expressed with an implicit scale factor of 10000. This fraction of the loan amount is the minimum available collateral needed to initiate a loan against this offer.
* **`maintenance_margin`:** *(integer)* The maintenance margin ratio of the offered loan, expressed with an implicit scale factor of 10000. Must not exceed `initial_margin`.
* **`apr`:** *(integer)* The annualized interest rate of the offered loan, expressed with an implicit scale factor of 10000. For offers of futures contracts, this is always zero.
* **`qualifiers`:** *(array of strings, conditional)* The qualifiers that apply to this offer, if any.
	* **`promo`:** This is a promotional offer.
	* **`facility`:** This offer represents a loan facility that has been granted to the user.
	* **`corporate`:** This offer is available to corporate customers only.

### `<loan>`

	{
		"id": <integer>,
		"offer_id": <integer>,
		"initiated": <integer>,
		"asset_id": <integer>,
		"principal": <float>,
		"term": <integer>,
		"initial_margin": <integer>,
		"maintenance_margin": <integer>,
		"apr": <integer>,
		"qualifiers": ["<string>", …]
	}

* **`id`:** *(integer)* The numeric identifier of an outstanding loan.
* **`offer_id`:** *(integer)* The numeric identifier of the offer against which this loan was initiated.
* **`initiated`:** *(integer)* The micro-timestamp at which this loan was initiated.
* **`asset_id`:** *(integer)* The numeric asset code of the asset in which this loan is denominated.
* **`principal`:** *(float)* The [scaled][] amount of the identified asset borrowed as principal in this loan. The present accrued amount is calculated as `ceil(principal * exp(apr/1e4 * (now() - initiated) / (365.2425 * 24 * 60 * 60 * 1000000)))`.
* **`term`:** *(integer)* The maximum term of this loan, in seconds. Repayment of this loan is due at micro-timestamp `initiated + term * 1000000`.
* **`initial_margin`:** *(integer)* The initial margin ratio of this loan, expressed with an implicit scale factor of 10000.
* **`maintenance_margin`:** *(integer)* The maintenance margin ratio of this loan, expressed with an implicit scale factor of 10000.
* **`apr`:** *(integer)* The annualized interest rate of this loan, expressed with an implicit scale factor of 10000. For loans of futures contracts, this is always zero.
* **`qualifiers`:** *(array of strings, conditional)* The qualifiers that apply to this loan, if any.
	* **`promo`:** This loan was initiated against a promotional offer.
	* **`facility`:** This loan represents a draw-down from a loan facility that has been granted to the user.
	* **`corporate`:** This loan was initiated against an offer that was available to corporate customers only.

---

## `GET /borrower/events`

Returns a stream of events pertaining to the user's borrowing activity.
The response body is an indefinitely long document in [`text/event-stream`][EventSource] format.

Optionally, the Base64-encoded [HTTP Basic authentication][] string may be passed to this resource in an `auth` query string parameter rather than in the standard `Authorization` request header. This option is provided as a workaround for a deficiency in some [EventSource][] client implementations.

[EventSource]: https://www.w3.org/TR/eventsource/

### Request

	GET /borrower/events HTTP/1.1

### Response

	HTTP/1.1 200 OK
	Content-Type: text/event-stream; charset=UTF-8
	
	event: Collateral
	data: [<collateral>, …]
	
	event: Offers
	data: [<offer>, …]
	
	event: OfferOpened
	data: <offer>
	
	event: OfferUpdated
	data: <offer>
	
	event: OfferClosed
	data: {"id": <integer>}
	
	event: Loans
	data: [<loan>, …]
	
	event: LoanInitiated
	data: <loan>
	
	event: LoanUpdated
	data: <loan>
	
	event: LoanTerminated
	data: {"id": <integer>}

---

## `GET /borrower/collateral/`

Returns the collateral available to the user in various assets.

### Request

	GET /borrower/collateral/ HTTP/1.1

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	[<collateral>, …]

---

## `GET /borrower/collateral/<id>`

Returns the collateral available to the user in the specified asset.

### Request

	GET /borrower/collateral/<id> HTTP/1.1

* **`id`:** *(integer)* The numeric asset code of the asset in which to report available collateral.

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	<collateral>

### Errors

* If the specified asset was not found, then this method returns:

		HTTP/1.1 404 Not Found
		Content-Length: 0

---

## `GET /borrower/offers/`

Returns the loan offers available to the user.

### Request

	GET /borrower/offers/ HTTP/1.1

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	[<offer>, …]

---

## `GET /borrower/offers/<id>`

Returns the specified loan offer.

### Request

	GET /borrower/offers/<id> HTTP/1.1

* **`id`:** *(integer)* The numeric identifier of the loan offer to return.

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	<offer>

### Errors

* If the specified loan offer was not found, then this method returns:

		HTTP/1.1 404 Not Found
		Content-Length: 0

* If the specified loan offer is not visible to the user, then this method returns:

		HTTP/1.1 403 Forbidden
		Content-Length: 0

---

## `GET /borrower/loans/`

Returns the borrower's outstanding loans.

### Request

	GET /borrower/loans/ HTTP/1.1

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	[<loan>, …]

---

## `GET /borrower/loans/<id>`

Returns the borrower's specified outstanding loan.

### Request

	GET /borrower/loans/<id> HTTP/1.1

* **`id`:** *(integer)* The numeric identifier of the loan to return.

### Response

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=US-ASCII
	
	<loan>

### Errors

* If the specified loan was not found, then this method returns:

		HTTP/1.1 404 Not Found
		Content-Length: 0

* If the specified loan is not visible to the user, then this method returns:

		HTTP/1.1 403 Forbidden
		Content-Length: 0

---

## `POST /borrower/loans/`

Initiates a loan.

### Request

	POST /borrower/loans/ HTTP/1.1
	Content-Type: application/x-www-form-urlencoded
	
	offer_id=<integer>&amount=<integer>

* **`offer_id`:** *(integer)* The numeric identifier of an available loan offer.
* **`amount`:** *(integer)* The [scaled][] amount of the offered asset to borrow.

### Response

	HTTP/1.1 201 Created
	Location: <id>
	Content-Type: application/json; charset=US-ASCII
	
	<loan>

The `Location` response header contains the numeric identifier of the newly initiated loan.

### Errors

* If the specified loan offer was not found, then this method returns:

		HTTP/1.1 404 Not Found
		Content-Length: 0

* If the requested loan was not allowed, then this method returns:

		HTTP/1.1 403 Forbidden
		Content-Type: text/plain; charset=US-ASCII
		
		<explanation>

---

## `POST /borrower/loans/<id>`

Partially repays an outstanding loan.

### Request

	POST /borrower/loans/<id> HTTP/1.1
	Content-Type: application/x-www-form-urlencoded
	
	amount=<integer>

* **`id`:** *(integer)* The numeric identifier of the outstanding loan to repay.
* **`amount`:** *(integer)* The [scaled][] amount to repay.

### Response

	HTTP/1.1 204 No Content

### Errors

* If the specified loan was not found, then this method returns:

		HTTP/1.1 404 Not Found
		Content-Length: 0

* If the requested repayment was not allowed, then this method returns:

		HTTP/1.1 403 Forbidden
		Content-Type: text/plain; charset=US-ASCII
		
		<explanation>

---

## `DELETE /borrower/loans/<id>`

Fully repays an outstanding loan.

### Request

	DELETE /borrower/loans/<id> HTTP/1.1

* **`id`:** *(integer)* The numeric identifier of the outstanding loan to repay.

### Response

	HTTP/1.1 204 No Content

### Errors

* If the specified loan was not found, then this method returns:

		HTTP/1.1 404 Not Found
		Content-Length: 0

* If the requested repayment was not allowed, then this method returns:

		HTTP/1.1 403 Forbidden
		Content-Type: text/plain; charset=US-ASCII
		
		<explanation>
