#How to Scale values when using the Trade Engine API

All values entered into or received from the trade engine need to have the quantity, price or total information scaled.  In this document we provide instructions and examples for scaling values when using the trade engine.

##Basic scaling

- All values relating to XBT are representing in units of 0.0001 XBT

- All values relating to GBP are representing in units of 0.01 GBP

###For the following functions any price provided should be scaled by 10000

```
- PlaceOrder
```

###For the following functions any prices returned are scaled by 10000

```
- GetOrders
- CancelOrder
- WatchOrders
- WatchTicker
```


###Examples:
```
1) PlaceOrder for quantity: 2000000, 

price:1234560000 base:XBT, counter:GBP

= Place a buy limit order of quantity 200 Bitcoin at a unit price of £1234.56

2) PlaceOrder for total:1234567890, base:XBT, counter:GBP
= Place a buy market order with an order total of £12345678.90

3) CancelOrder returns quantity: 1000000, price:1234560000 base:XBT, counter:GBP
= a returned order of quantity 100 Bitcoin at a unit price of £1234.56
```

