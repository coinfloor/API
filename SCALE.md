# Scaling


## Amounts

Asset quantities and totals transmitted via the Coinfloor API are encoded as integers with an implicit scale factor that depends on the particular asset type.

| Asset Type | ID (Hex) | ID (Dec) | Scale Factor |  Atomic Unit |
|:----------:|---------:|---------:|-------------:|-------------:|
|     XBT    |   0xF800 |    63488 |        10000 |      Ƀ0.0001 |
|     BCH    |   0xF808 |    63496 |        10000 |     ɃC0.0001 |
|     ETH    |   0xF820 |    63520 |        10000 |      Ξ0.0001 |
|     EUR    |   0xFA00 |    64000 |          100 |        €0.01 |
|     GBP    |   0xFA20 |    64032 |          100 |        £0.01 |
|     USD    |   0xFA80 |    64128 |          100 |        $0.01 |
|     PLN    |   0xFDA8 |    64936 |          100 |      0.01 zł |

### Examples

| Logical Amount | Integer Encoding |
|---------------:|-----------------:|
|       Ƀ12.3456 |           123456 |
|       £9876.54 |           987654 |
|           Ƀ1.5 |            15000 |
|            £20 |             2000 |


## Prices

Prices transmitted via the Coinfloor API are encoded as integers with an implicit scale factor that depends on the particular asset pair.

|    Asset Pair     | Scale Factor |
|:-----------------:|-------------:|
|      XBT:EUR      |          100 |
|      XBT:GBP      |          100 |
|      XBT:USD      |          100 |
|      XBT:PLN      |          100 |
|      BCH:GBP      |          100 |
|      ETH:GBP      |          100 |

### Examples

| Logical Price | Integer Encoding |
|--------------:|-----------------:|
|   £543.21/XBT |            54321 |
|     £1000/XBT |           100000 |
