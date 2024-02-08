# ThreeMonthVolume

This application monitors the market for any large-volume trades that are equal to or greater than 1% of the stock's shares on issue.

## Installation

The application can be accessed [here](https://threemonthvolume.hlamtools.trade).

## User Guide

### Alerts

This tab displays all the current alerts for the day, including metrics like _Block Trade Price_ and _Premium/Discount_. More metrics can be added if needed. These alerts can be filtered to exclude Company Options.

## Documentation

### ThreeMonthVolume.Blazor

The blazor application uses standard _[TradingToolCommon.Blazor](https://github.com/hlam-developers/TradingToolCommon)_ components. 

There are inconsistencies with the _DatafeedTimestamp_ of alerts and the time they were actually received as there is a delay between the _Exchange Update_ and _Algorithm Heartbeat_ variables (see below). It is not necessary to correct these inconsistencies as they make no impact to the functionality of the tool. The timestamp used for the front-end is _LastUpdateDatetime_ as a result.

### ThreeMonthVolume.Algorithm

The algorithm inherits the _TradingTool.Common.Algorithm.AlgorithmBase_ class. The algorithm works a little different to the algorithm in the other tools as it assesses every instrument fed through the relevant _Zmq_ publishers, and hence currently isn't efficient enough to send regular algorithm heartbeats before the exchange alarms trigger.

This algorithm used to rely on querying the _{env}-datafeed_ table but the data received is too slow and unreliable. Instead, the algorithm initialises a _TradeClient_ object and queries Iress directly.
