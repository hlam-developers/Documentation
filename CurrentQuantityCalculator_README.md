  
# Current Quantity Calculator

This application is primarily used to calculate current risk weightings, as well as calculate quantities to each new target risk values.

## Installation

The application can be accessed [here](https://cqc.hlamtools.trade/).

## User Guide

The page will refresh all relevant information pertaining to the queried ticker automatically. 

### Code

Enter a position ticker to search the relevant metrics for a position.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/cqc-code.png)

### New Position Calculator

Enter a theoretical risk value to calculate how many shares to buy/sell to reach the calculated position. This detail is shown in the "Additional Information" section.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/cqc-new-pos.png)

#### Current Risk Amounts

<sub><sup> Risk Exposure Differs for Each Fund </sup></sub>

Current risk exposure of the position in terms of average position entry prices, as well as the ticker's last sale price. This is displayed in both percentage and dollar terms.

#### Additional Information

Target from Last Price - Amount of shares required to reach new risk value.

Quantity from Last Price - Amount of shares required to actioned, to reach new risk value.

Dollars Required - Amount of cash required to be actioned, to reach target risk value.

Quantity if No Cash - Total amount of shares that can be actioned with current cash.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/cqc-current-risk-additional-info.png)

#### Add Investments Entry

Adds quantities generated from the new theoretical position calculation to the investments database. This can then be viewed in the Investments tab in PositionMonitor.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/cqc-add-investments.png)

#### Target Weightings

The current target weightings for the ticker.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/cqc-target-weightings.png)

### Current Quantities

The current quantities we hold for the ticker, sectioned by entity.

### Current Cash

The current cash quantities we hold for each entity.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/cqc-current-cash-quantities.png)

### Deal Snapshot

<sup> You can view more details about the deal with the Event Calendar tool. </sup>

If there is an entry in Deal Database for the position, this section will populate with details of the most current/relevant deal.

#### Event Calendar

If there are Event Calendar entires for this position, this section will populate with the three latest events for the position.
