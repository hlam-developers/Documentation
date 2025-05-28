  
# DealDatabase

This application is used to record and store details of M&A deals as well as the details for the traded positions for each deal.

## Installation

The application can be accessed [here](https://dealdatabase.hlamtools.trade).

## User Guide

### Current Dataset

This tab briefly outlines the details of each deal. This tab only contains deals with the `Date Announced` date being within the last two years. It is sorted automatically by the `Date Announced` column on load.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-current-dataset.png)

#### Functionalities

##### Double Click

The user can double click a deal to automatically filter by the deals target ticker.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-current-dataset-filter.png)

##### Search & Clear

The user can search for the deals target ticker by inputting the target ticker into the searchbar and by pressing *Enter* on their keyboard. The user can also clear the filtering by pressing the `Clear` Button next to the searchbar.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-search-clear.png)

### Historical Dataset

Similarly to __Current Dataset__, the __Historical Dataset__ tab briefly outlines the details of each each deal but it contains deals with their `Date Announced` date that are beyond the last two years.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-historical-dataset.png)

### Risk Weightings

This tab briefly outlines the details of the trade position & risk weighting for each deal for their respective portfolio. On load it is sorted automatically by the `Date Announced` column.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/dealdatabase-risk-weightings.png)
  
## Documentation

### DealDatabase.Blazor

The blazor application uses standard *[TradingToolCommon.Blazor](https://github.com/hlam-developers/TradingToolCommon)* components.

The __Current Dataset__ tab mimics the functionality of the *Dataset* tab in the *Harvest Lane Deal Database.xlsm* spreadsheet. This tab contains deals with the `Date Announced` date being within the last two years for load speed purposes. These deals are received from the *{env}-dealdatabase* table.

The __Historical Dataset__ tab mimics the functionality of the *Dataset* tab in the *Harvest Lane Deal Database.xlsm* spreadsheet. This tab contains deals with the `Date Announced` date beyond the last two years. These deals are received from the *{env}-dealdatabase* table.

The __Risk Weightings__, tab mimics the functionality of the *Risk Weightings* tab in the *Harvest Lane Deal Database.xlsm* spreadsheet. These deals are received from the *{env}-risk-weightings* table.