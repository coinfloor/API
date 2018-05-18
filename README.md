# Coinfloor Trade Engine APIs

Coinfloor's application programming interfaces (APIs) provide our clients programmatic access to control aspects of their accounts and to place orders on Coinfloor's trading platforms.

Coinfloor provides two interfaces to our API: a [Bitstamp Emulation API][BIST] and our native [WebSocket API]. Using these interfaces, it is possible to make both authenticated and unauthenticated API calls.

Access keys are available on the Coinfloor logged-in dashboard page for verified users which, in conjunction with your account password, allow use of either API.

---
## General notes

To protect the performance of the system, Coinfloor imposes certain limits on the rates at which you may issue commands to the API. Please see [LIMITS.md](LIMITS.md).

All quantities and prices are transmitted and received as integers with implicit scale factors. For scale information, please see [SCALE.md](SCALE.md).

Coinfloor has published [client libraries] for several popular languages to aid you in implementing your client application.

---
## Getting started with the Bitstamp Emulation (“BIST”) API

The BIST API is accessible via HTTPS connection to the following URLs:

```text
https://webapi.coinfloor.co.uk:8090/bist/
https://webapi.coinfloorex.com:8090/bist/
```

[Click here for more details on how to use the BIST API][BIST]

---
## Getting started with the WebSocket API

The WebSocket API is accessible via WebSocket connection to the following URLs:

```text
ws://api.coinfloor.co.uk/ (unencrypted)
wss://api.coinfloor.co.uk/ (encrypted)

ws://api.coinfloorex.com/ (unencrypted)
wss://api.coinfloorex.com/ (encrypted)
```

Commands, replies, and notifications traverse the WebSocket in text frames with JSON-formatted payloads.

***We strongly recommend that you use TLS transport for all connections.***

WebSocket connections to the API will timeout after 60 seconds of no traffic passing in either direction. To prevent timeouts, send a Ping frame approximately every 45 seconds while the connection is otherwise idle. You do not need to send Ping frames if you are otherwise sending or receiving data frames on the socket.

[Click here for more details on how to use the WebSocket API][WebSocket API]



[BIST]: BIST.md
[WebSocket API]: WEBSOCKET-README.md
[client libraries]: https://github.com/coinfloor/
