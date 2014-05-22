Web-API
=======

A simple to use API wrapper that allows users to access the Websocket API via HTTP


Coinfloor's web API allows users to make full use of the Coinfloor platform and [WebSocket API](https://github.com/coinfloor/API/blob/master/WEBSOCKET-README.md) in a standard HTTP request/response fashion, using a user ID, API key, and password for authentication.

---
# Security

Due to the security credentials being sent with each request (over SSL), use of the web API entails greater exposure than use of the public-key authentication in our [WebSocket API](https://github.com/coinfloor/API/blob/master/WEBSOCKET-README.md).


# Authentication credentials

Authentication credentials are passed in these HTTP request headers:
```text
coinfloor-user-id:  <string>
coinfloor-cookie:   <string>
coinfloor-password: <string>
```
You can get your authentication credentials from the dashboard on the Coinfloor site.

---
# Body, methods

All method calls for the web API are as described in the [WebSocket API](https://github.com/coinfloor/API/blob/master/WEBSOCKET-README.md) documentation. The HTTP request body must be a JSON array containing up to six API method calls.

A Ruby example:

``` ruby
require 'httparty'

response = HTTParty.post(
  "https://web_api.coinfloor.co.uk:8443/api/",
  
  :headers => {
    'content-type'       => 'application/json',
    'coinfloor-user-id'  => '42',
    'coinfloor-cookie'   => 'ABCDEFGHIJKLMNOPQRSTUVWXYZa=',
    'coinfloor-password' => 'opensesame'
  },
  
  :body    => "[{\"method\":\"GetBalances\",\"tag\":1}, {\"method\":\"GetOrders\",\"tag\":2}]"
)
```

# Responses

A successful response will return an HTTP status code of 200 with a JSON-encoded array in the body of the response. For each method call in the request, a corresponding reply will be present in the response array.

# Errors

If there is an error or a problem with your request, you will receive a response with an HTTP error code and no body.

* 401: Failure to authenticate, perhaps as a result of missing authentication details.
* 400: Invalid JSON in the header or body of the request.
* 413: Too many method calls in request.
* 500: Server-side error.
* 503: API is unavailable.


[WebSocket API]: https://github.com/coinfloor/API/blob/master/README.md
