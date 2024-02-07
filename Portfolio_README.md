  

# Portfolio

This application is used to track the daily gain/loss movements of positions across the portfolios.

  

## Installation

The application can be accessed [here](https://portfolio.hlamtools.trade).

  

## User Guide

### Key Information

This tab is view only and refreshes automatically. The viewport is flexible for those on mobile. The top five gainer and loser positions per entity are shown, sorted by their values. Positions whose tickers exhibit greater than 5.0% (gainers) or less than -5.0% (losers) movement change are formatted in bold.

The tab also currently shows ARF NAV, Cash and Margin values. More rows can be added in the future to display other key metrics of the portfolios.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/key-information-tab.png)
  

### Gainers/Losers

This tab is also view only and refreshes automatically. This tab allows for users to get a closer look at all gains and movement metrics of all positions at any given time of day. Positions with a _Current Change_ of _$0.00_ will not be included in these columns.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/gainers-losers.png)

  

### ARF Positions, USYD Positions & MTF Positions
These tabs are fairly self explanatory - they list all current open positions held for each entity.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/positions.png)


### All Entities
This tab provides an over-arching view of all positions held by all entities, including aggregate quantities.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/all-entities.png)


### Configuration

This tab is used to replace certain instrument tickers that are retrieved from the fund reporting to tickers that are recognised by Iress (and thus allowing for alerts to be received for that instrument/position). Additionally, you can now make many different adjustments to each position and specify whether you would like these configurations to be _Temporary_ (they last only for the day that the configuration was put in), or _Permanent_.
  
Fill your configuration specifications in the input boxes, then press _Add_ when you're ready. You can edit the current configurations at any time by pressing their corresponding cells, and you can delete current configurations whenever you wish by pressing the red bin button.



![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/configuration.png)

### Settings

The tool currently only has one custom setting - the ability to update information from Excel from the _Settings_ menu.
This program currently runs automatically at 9:30am to account for the spreadsheets being good to go for the day, however you can choose to run it at any time to account for any changes. This updates information for all tools that rely on our spreadsheet/position data.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/settings.png)
  

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
