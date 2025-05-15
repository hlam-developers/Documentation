  
# Portfolio

This application is used to track the daily gain/loss movements of positions across the portfolios.

## Installation

The application can be accessed [here](https://portfolio.hlamtools.trade).

## User Guide

All tabs are view only and refreshes automatically.

### Key Information

This tab briefly outlines the details of each portfolio for the day and compares them to their respective counterpart XJO/XKO. Details of each section is outlined below:

| Portfolio Details - Section One| Image |
| --- | --- |
| <ul><li>Increase Today - percentage change for the day</li><li>Return This Month - percentage change for the month</li><li>NAV - net asset value for the portfolio</li> <li>Available Margin - current available margin for the portfolio </li></ul>  | ![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-section1.png) |

| Portfolio Details - Section Two| Image |
| --- | -- |
| <ul><li>Daily Change - figure change for the day </li><li>Current Gain/Loss - figure change of the portfolio since conception </li><li>Unit Price - price of each unit for the fund </li><li>NAB Cash - available cash in the NAB account</li><li>Excess Cash - available cash in the Barrenjoey account</li></ul> |![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-section2.png) |

| Portfolio Details - Section Three | Image |
| --- | --- |
| Positions whose tickers exhibit movement change is more than 5% are formatted in bold. <ul><li>Top Gainers/Losers - top five gainers/losers for the day</li><li>Current Gain/Loss - figure change for each gainer/loser</li></ul>| ![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-section3.png) |

| Portfolio Details - Section Four| Image |
| --- | --- |
| <ul><li>Exposure - top five entities that the portfolio holds value wise</li><li>% of Current NTA - the percentage value of the entity within the portfolio</li><li>Max Loss - maximum draw down (last price - stop price)</li></ul> | ![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-section4.png)|

  
### Gainers/Losers

This tab allows users to get a closer look at gains and movement metrics for all positions on the current day. Positions with a _Current Change_ of _$0.00_ will not be displayed.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-gainers-losers.png)

### Substantial Holders

This tab shows all positions held that is substantial or near substantial shareholder of a company. It then displays if a substantial shareholder notice needs to be lodged.
More details are listed, such as how many more shares the company would need to be buy/sell for each position before the next notice.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-susbtantial-holders.png)

### PTH

This tab outlines all paid-to-hold (PTH) positions that the company has borrowed and its respective breakdown.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-pth.png)

### Trades

This tab outlines all trades made on the current day (B/S - Buy/Sell).

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-trades.png)

### Margin Details

This tab outlines the market value and the margin requirement of the portfolio. It also shows the margin information for each position.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-margin-details.png)

### Settlement Information

This tab outlines each trade and its details as well as its settlement date.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-settlement-information.png)

### ARF Positions, USYD Positions & MTF Positions

These tab lists all current open positions held for each entity.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-positions.png)

### All Entities

This tab provides an over-arching view of all positions held by all entities, including aggregate quantities.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-all-entities.png)

### Configuration

This tab is used to replace certain instrument tickers that are retrieved from the fund reporting to tickers that are recognised by Iress (and thus allowing for alerts to be received for that instrument/position). Additionally, you can now make many different adjustments to each position and specify whether you would like these configurations to be _Temporary_ (they last only for the day that the configuration was put in), or _Permanent_.
  
Fill your configuration specifications in the input boxes, then press _Add_ when you're ready. You can edit the current configurations at any time by pressing their corresponding cells, and you can delete current configurations whenever you wish by pressing the red bin button.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-configuration.png)

### Settings

The tool currently only has one custom setting - the ability to update information from Excel from the _Settings_ menu.
This program currently runs automatically at 9:30am to account for the spreadsheets being good to go for the day, however you can choose to run it at any time to account for any changes. This updates information for all tools that rely on our spreadsheet/position data.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/portfolio-settings.png)
  
## Documentation

### Portfolio.Blazor

The blazor application uses standard _[TradingToolCommon.Blazor](https://github.com/hlam-developers/TradingToolCommon)_ components.

The _Key Information_ tab mimicks the functionality of the _Key Information_ tab in the _Position Monitor Master.xlsm_ spreadsheet. These alerts are received from the _{env}-gainers-losers_,   _{env}-portfolio-alerts_, and _{env}-overviews_ tables.

The _Gainers/Losers_ tab shows all subscribed Gainers/Losers alerts received by the application. These alerts are received from the _{env}-gainers-losers_ table, and unlike the _Key Information_ tab, are only filtered for null values.

The _ARF Positions_, _USYD Positions_, _MTF Positions_ and _All Entities_ tabs rely on alerts received from the _{env}-portfolio-alerts_ table.

The _Configurations_ table persists and subscribes to the _{env}-portfolio-configuration_ table.

The _Excel Update_ function in the _Settings_ tab calls the _/datafeed-excel_ API endpoint from restful, which calls the _{env}-datafeed-excel_ AWS lambda function. This function uses SSM to run a Powershell script to the _windows-tools-large_ instance to trigger the _[DataFeed.Excel](https://github.com/hlam-developers/DataFeed)_ scheduled task.

### Portfolio.Algorithm

The algorithm subscribes to the _{env-long-positions}_ table and stores all long positions to track for the day. This table is updated every morning by _[DataFeed.Excel](https://github.com/hlam-developers/DataFeed)_.

The algorithm loops through the _UpdateGainsLosses()_ and _UpdateOverviews()_ methods from initialisation to close. The methods push alerts to the _{env-overviews}_ table and _{env-gainers-losers}_ table. The algorithm subscribes to the _{env}-datafeed_ table for pricing updates.
