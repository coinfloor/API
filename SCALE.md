# Scaling


## Amounts

Asset quantities and totals transmitted via the Coinfloor API are encoded as integers with an implicit scale factor that depends on the particular asset type.

| Asset Type | ID (Hex) | ID (Dec) | Scale Factor   |  Atomic Unit |
|:----------:|---------:|---------:|---------------:|-------------:|
|     XBT    |   0xF800 |    63488 | **[[ API V1:** 10000 **]]** <br/>  **[[ API V2:** 100000000 **]]** | **[[ API V1:** Ƀ0.0001 **]]** <br/> **[[ API V2:**  Ƀ0.00000001 **]]**|
|     EUR    |   0xFA00 |    64000 |          100   |        €0.01 |
|     GBP    |   0xFA20 |    64032 |          100   |        £0.01 |

### Examples

| Logical Amount | Integer Encoding |
|---------------:|-----------------:|
|  Ƀ12.3456      | **[[ API V1:**  123456 **]]** <br/> **[[ API V2:**  1234560000 **]]**|
|       £9876.54 |           987654 |
|           Ƀ1.5 |            15000 |
|            £20 |             2000 |


## Prices

Prices transmitted via the Coinfloor API are encoded as integers with an implicit scale factor that depends on the particular asset pair.

|    Asset Pair     | Scale Factor |
|:-----------------:|-------------:|
|      XBT:EUR      |          100 |
|      XBT:GBP      |          100 |


### Examples

| Logical Price | Integer Encoding |
|--------------:|-----------------:|
|   £543.21/XBT |            54321 |
|     €1000/XBT |           100000 |
