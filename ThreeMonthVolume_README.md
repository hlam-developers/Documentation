# ThreeMonthVolume

This application monitors the market for any large-volume trades that are equal to or greater than 1% of the stock's shares on issue.

## Installation

The application can be accessed [here](https://threemonthvolume.hlamtools.trade).

## User Guide

Alerts are generated when a security trades 1% of the stock's shares on issue within a certain time period.

### Row Colours

An alert may have one of the below row colours. The colours below are in order of priority;
if an alert is eligible for more than one row colour it will use the first.

Red line - Stock health indication. Last price is below yearly low (yearly low as of the previous trading day;
not updated intra-day to reflect breached yearly low).

Green line - Stock health indication. Last price is greater than monthly low by a specific margin:

If last Price &lt; \$0.20 then Green if Last Price >= 130% of Monthly Low.
If last Price &lt; \$1.00: then Green if Last Price >= 120% of Monthly Low.
If last Price &gt;= \$1.00: then Green if Last Price >= 115% of Monthly Low.

### Text Colours

An alert will have <span style="color: red">Red Text</span> if there are already two or more alerts for the same code
in the last 60 seconds.

* Note that Blue Line alerts will never display red text, and are excluded from the count
for other alerts.

### Sounds

Sounds are played in the below priority list. Only one sound is played per alert.

### Column Headings
![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/tmv-column-headings.png)

Time - Exchange time for the alert

Pos - Initials of instrument owner (only one person). Determined in order of priority: User who has bought the most. User who has sold the most. User with open orders. In the case of multiple users with open orders ?? is used. Will be suffixed by <strong>a</strong> and/or <strong>b</strong> depending on the order types.

Watchlist - Indicates which watchlist the stock is saved under.

Code - Security code for the alert.

Volume - The amount of shares bought/sold at the instance.

Last - Last price at time of alert.

Block Trade Price - The position/average trade price of the trade.

% of Shares On Issue - How large the trade is in comparison to the shares on Issue.

Premium Discount - Is there a premium/discount of the trade (calculated via block trade price vs last price)

MktCap - Market cap of the stock.

Checked - User who checked the alert.

## Documentation

### ThreeMonthVolume.Blazor

The blazor application uses standard _[TradingToolCommon.Blazor](https://github.com/hlam-developers/TradingToolCommon)_ components. 

There are inconsistencies with the _DatafeedTimestamp_ of alerts and the time they were actually received as there is a delay between the _Exchange Update_ and _Algorithm Heartbeat_ variables (see below). It is not necessary to correct these inconsistencies as they make no impact to the functionality of the tool. The timestamp used for the front-end is _LastUpdateDatetime_ as a result.

### ThreeMonthVolume.Algorithm

The algorithm inherits the _TradingTool.Common.Algorithm.AlgorithmBase_ class. The algorithm works a little different to the algorithm in the other tools as it assesses every instrument fed through the relevant _Zmq_ publishers, and hence currently isn't efficient enough to send regular algorithm heartbeats before the exchange alarms trigger.

This algorithm used to rely on querying the _{env}-datafeed_ table but the data received is too slow and unreliable. Instead, the algorithm initialises a _TradeClient_ object and queries Iress directly.