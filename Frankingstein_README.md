# Frankingstein

This application is a modernised web application version of the Frankingstein spreadsheet.

## Installation

The application can be accessed [here](https://frankingstein.hlamtools.trade).

## User Guide

### Overview

The Overview tab displays all current takeover configurations and their corresponding metrics. All columns can be sorted and hidden by right clicking on the column headers. This configuration is saved for your username, so feel free to customise it to your liking.

Before market open, after market close and during any Pre-NR received period, if there is an auction match price for a takeover, the auction match price will be displayed in the "Match Price" column. This price will be used in replacement for the current Bid/Offer prices used in calculation of all metrics. Accordingly, the takeover row will be highlighted to indicate that the auction match price is being used.

![alt text](https://hlam-public.s3.ap-southeast-2.amazonaws.com/auction-match-formatting.png)

The same way that you would in the spreadsheet, you can set alerts for any takeover. When a notification is received, it will be displayed in the Notifications box towards the bottom of the page. This will not trigger again until the metric falls back below the alert threshold. Alerts that are set are highlighted in the column for easy identification.

### Metrics

The Metrics tab is a view-only tab that displays all the metrics calculated for each takeover. The metrics are calculated using the same formulas as in the spreadsheet, and are updated in real-time.

![alt text](https://hlam-public.s3.ap-southeast-2.amazonaws.com/metrics-tab.png)

### Configuration

The Configuation tab is used to enter, edit and delete takeover configurations. Ticking the "LIC?" and "Estimate" checkboxes will format the relevant cells in the Overview tab to match the spreadsheet formatting. The value for the Payment Date is automatically calculated as 46 days before the Close Date, but you can override this if you so wish by entering a date into the Payment Date field.

![alt text](https://hlam-public.s3.ap-southeast-2.amazonaws.com/configuration-tab.png)

### Annualised Calculator

**Please make sure you are adding the relevant exchange to the end of your ticker input, e.g. "SPR.ASX".**

The Annualised Calculator tab is used to calculate gross and annualised returns for a given takeover, customisable by parameters. You can add/remove as many calculators as you like, and you can also open them in new tabs.

If querying for a Cash takeover, the calculator will display a price step table with relevant gross and annualised returns.

![alt text](https://hlam-public.s3.ap-southeast-2.amazonaws.com/calculator-cash.png)

If querying for a Scrip takeover, the calculator will display a price step table with relevant gross and annualised returns, as well as an additional table comparing gross and annualised returns between the current Bid and Offer prices for both the Target and Acquirer. You can also input custom prices for the Target and Acquirer to see how the returns change.

![alt text](https://hlam-public.s3.ap-southeast-2.amazonaws.com/calculator-scrip.png)

### Spread Matrix

**Please make sure you are adding the relevant exchange to the end of your ticker input, e.g. "SPR.ASX".**

The Spread Matrix tab is used to calculate spread returns for a given scrip takeover, displayed in a matrix table and organised by price steps. This is also customisable by parameters.

![alt text](https://hlam-public.s3.ap-southeast-2.amazonaws.com/spread-matrix.png)
