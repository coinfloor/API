# Scaling


## Amounts

Asset quantities and totals transmitted via the Coinfloor API are encoded as integers with an implicit scale factor that depends on the particular asset type.

| Asset Type | Asset Code | Scale Factor |  Atomic Unit |
|:----------:|-----------:|-------------:|-------------:|
|     XBT    |     0xF800 |        10000 |      Ƀ0.0001 |
|     BCH    |     0xF808 |        10000 |     ɃC0.0001 |
|     EUR    |     0xFA00 |          100 |        €0.01 |
|     GBP    |     0xFA20 |          100 |        £0.01 |
|     USD    |     0xFA80 |          100 |        $0.01 |
|     PLN    |     0xFDA8 |          100 |      0.01 zł |
|  XBTJAN18  |     0xC800 |        10000 | Jan18Ƀ0.0001 |
|  USDJAN18  |     0xCA80 |          100 |   Jan18$0.01 |
|  XBTFEB18  |     0xC801 |        10000 | Feb18Ƀ0.0001 |
|  USDFEB18  |     0xCA81 |          100 |   Feb18$0.01 |
|  XBTMAR18  |     0xC802 |        10000 | Mar18Ƀ0.0001 |
|  USDMAR18  |     0xCA82 |          100 |   Mar18$0.01 |
|  XBTAPR18  |     0xC803 |        10000 | Apr18Ƀ0.0001 |
|  USDAPR18  |     0xCA83 |          100 |   Apr18$0.01 |
|  XBTMAY18  |     0xC804 |        10000 | May18Ƀ0.0001 |
|  USDMAY18  |     0xCA84 |          100 |   May18$0.01 |
|  XBTJUN18  |     0xC805 |        10000 | Jun18Ƀ0.0001 |
|  USDJUN18  |     0xCA85 |          100 |   Jun18$0.01 |
|  XBTJUL18  |     0xC806 |        10000 | Jul18Ƀ0.0001 |
|  USDJUL18  |     0xCA86 |          100 |   Jul18$0.01 |
|  XBTAUG18  |     0xC807 |        10000 | Aug18Ƀ0.0001 |
|  USDAUG18  |     0xCA87 |          100 |   Aug18$0.01 |
|  XBTSEP18  |     0xC808 |        10000 | Sep18Ƀ0.0001 |
|  USDSEP18  |     0xCA88 |          100 |   Sep18$0.01 |
|  XBTOCT18  |     0xC809 |        10000 | Oct18Ƀ0.0001 |
|  USDOCT18  |     0xCA89 |          100 |   Oct18$0.01 |
|  XBTNOV18  |     0xC80A |        10000 | Nov18Ƀ0.0001 |
|  USDNOV18  |     0xCA8A |          100 |   Nov18$0.01 |
|  XBTDEC18  |     0xC80B |        10000 | Dec18Ƀ0.0001 |
|  USDDEC18  |     0xCA8B |          100 |   Dec18$0.01 |

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
| XBTJAN18:USDJAN18 |          100 |
| XBTFEB18:USDFEB18 |          100 |
| XBTMAR18:USDMAR18 |          100 |
| XBTAPR18:USDAPR18 |          100 |
| XBTMAY18:USDMAY18 |          100 |
| XBTJUN18:USDJUN18 |          100 |
| XBTJUL18:USDJUL18 |          100 |
| XBTAUG18:USDAUG18 |          100 |
| XBTSEP18:USDSEP18 |          100 |
| XBTOCT18:USDOCT18 |          100 |
| XBTNOV18:USDNOV18 |          100 |
| XBTDEC18:USDDEC18 |          100 |

### Examples

| Logical Price | Integer Encoding |
|--------------:|-----------------:|
|   £543.21/XBT |            54321 |
|     £1000/XBT |           100000 |
