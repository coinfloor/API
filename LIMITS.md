# Request Limits

Coinfloor's application programming interface (API) allows our clients to access and control their accounts or view our market data using custom-written software. To protect the performance of the system, we impose certain limits:

| Metric                  |                            Limit |
|:------------------------|---------------------------------:|
| Open orders             |                   1,000 per user |
| Authentication attempts |          1,000 per hour per user |
| Order placements*       | 1,000 per 1,000 seconds per user |
| Information requests†   |    10 per 10 seconds per session |
| Records requests‡       |         10 per 1 minute per user |

\* "Order placements" also include order cancellations and market order estimates.

† "Information requests" include balance inquiries, open order listings, and trade volume inquiries.

‡ "Records requests" pertain to requests for historical records, such as past trades.
