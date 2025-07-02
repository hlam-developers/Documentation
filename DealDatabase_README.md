  
# DealDatabase

This application is used to record and store details of M&A deals as well as the details for the traded positions for each deal.

## Installation

The application can be accessed [here](https://dealdatabase.hlamtools.trade).

## User Guide

### Dashboard

#### Functionalities

##### Search Feature

The user can input a ticker, which would open a dropdown of existing tickers within the database. The user can then select the ticker to view its trade deals.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-dashboard-search.png)

##### Deal Selector

After inputting a ticker, the user can specifically select a deal that is stored within the database (the latest deal is automatically selected). Information regarding the ticker's market data (for the day) is also displayed.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-dashboard-dropdown-data.png)

#### Quick Selector

For ease of use, the latest deals are displayed here. This allows the user to be able to quickly click and access the deals data. This is sorted by the deals `Date Announced` field.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-dashboard-quickselector.png)

#### Deal Information

Only the `Deal Information`, `Deal Metrics` and the `Risk Weightings` of the TradeDeal is displayed. For `Risk Weighting`, the latest risk weights are displayed for the TradeDeal according to its `Date` property.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-dashboard-tradeid-information.png)

### Current Dataset

This tab briefly outlines the details of each deal. This tab only contains deals with their `Status` value being `Pending`. It is sorted automatically by the `Date Announced` column on load.

#### Functionalities

##### Search

The user can search apply multiple filters by selecting the property and by inputting the value into the search bar. The user can also clear all view filtering by pressing the `Reset View` button.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-currentdataset-search.png)

##### Filter

The user can filter through deals by selecting the property and by selecting/inputting the filter value into the filter bar. The user can also remove a specific filter by pressing the `X` on the applied filter, the user can also clear all view filtering by pressing the `Reset View` button.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-currentdataset-filter.png)

##### Features

The user can highlight a deal by clicking onto a deal, this deal will have its background highlighted in purple. There are multiple features such as `Edit` and `Delete` for each deal. A popup will be displayed for confirmation before deleting a deal.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-currentdataset-features.png)

#### Creating a New Deal

The user can add a new deal into the database by pressing the orange `Add New Deal` button.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-currentdataset-add-deal.png)

##### Template

The user can prefill fields by searching through existing deals.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-currentdataset-add-deal-template.png)

### Risk Weightings

This tab briefly outlines the details of the trade position & risk weighting for each deal for their respective portfolio. On load it is sorted automatically by the `Date Announced` column.

#### Search

The user can search through risk-weightings via the Trade ID or by the Instrument.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-riskweightings-search.png)

#### Add Risk Weights

The user can add a new risk weighting into the database by pressing the orange `Add New Risk Weighting` button.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-riskweightings-addriskweights.png)

### Historical Dataset

Similarly to __Current Dataset__, the __Historical Dataset__ tab briefly outlines the details of each each deal but it contains the rest of the deals with their `Status` that are not `Pending`.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-historicaldataset.png)

#### Date Filter

Due to the large volume of deals within the database, deals are automatically filtered by their `Date Announced` value. The user can then choose to display which deals are displayed by selecting the year for the deals `Date Announced` value.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-historicaldataset-datefilter.png)
  
## Documentation

### DealDatabase.Blazor

The blazor application uses standard *[TradingToolCommon.Blazor](https://github.com/hlam-developers/TradingToolCommon)* components.

The __Current Dataset__ tab mimics the functionality of the *Dataset* tab in the *Harvest Lane Deal Database.xlsm* spreadsheet. This tab contains deals with the `Date Announced` date being within the last two years for load speed purposes. These deals are received from the *{env}-dealdatabase* table.

The __Historical Dataset__ tab mimics the functionality of the *Dataset* tab in the *Harvest Lane Deal Database.xlsm* spreadsheet. This tab contains deals with the `Date Announced` date beyond the last two years. These deals are received from the *{env}-dealdatabase* table.

The __Risk Weightings__, tab mimics the functionality of the *Risk Weightings* tab in the *Harvest Lane Deal Database.xlsm* spreadsheet. These deals are received from the *{env}-risk-weightings* table.