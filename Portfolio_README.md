
# Portfolio
This application is used to track the daily gain/loss movements of positions across the portfolios.

## Installation
The application can be accessed [here](https://portfolio.hlamtools.trade).

## User Guide
### Key Information
This tab is view only and refreshes automatically. The viewport is flexible for those on mobile. The top five gainer and loser positions per entity are shown, sorted by their values. Positions whose tickers exhibit greater than 5.0% (gainers) or less than -5.0% (losers) movement change are formatted in bold.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/key-information-tab.png)

### Alerts
This tab is also view only and refreshes automatically. This tab is more for debugging, but proves useful if you are wanting to see exact gainer/loser alerts. Each entry provides which entity the alert relates to, what instrument it represents and the current change and movement. The same formatting applies to these entries as they do in the _Key Information_ tab.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/alerts-tab.png)

### Configuration
This tab is used to replace certain instrument tickers that are retrieved from the fund reporting to tickers that are recognised by Iress (and thus allowing for alerts to be received for that instrument/position).

Enter the current ticker and the ticker it should be replaced with in the top input row, then press Add.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/configuration-tab.png)

Current configurations can be removed by pressing the Bin button in the corresponding row.

## Documentation
### Portfolio.Blazor
The blazor application uses standard _[TradingToolCommon.Blazor](https://github.com/hlam-developers/TradingToolCommon)_ components.

The _Key Information_ tab mimicks the functionality of the _Key Information_ tab in the _Position Monitor Master.xlsm_ spreadsheet.

The _Alerts_ tab shows all subscribed Gainers/Losers alerts received by the application. This is only stored in-memory and is re-calculated upon refresh.

### Portfolio.Algorithm
The algorithm subscribes to the _{env-long-positions}_ table and stores all long positions to track for the day. This table is updated every morning by _[DataFeed.Excel](https://github.com/hlam-developers/DataFeed)_.

The algorithm loops through the _UpdateGainsLosses()_ and _UpdateOverviews()_ methods from initialisation to close. The methods push alerts to the _{env-overviews}_ table and _{env-gainers-losers}_ table. The alerts are calculated using Iress' API.
