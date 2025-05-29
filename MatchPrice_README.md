# MatchPrice

## Installation

The application can be accessed [here](https://matchprice.hlamtools.trade/).

## Algorithm Description

An algorithm that finds shares that are set to open higher or lower than their previous close.

### Sounds

Sounds are played in the below priority list. Only one sound is played per alert.

### Column Headings
![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/matchprice-headings.png)

Pos - Indicates if a position is held for this instrument.

Watchlist - Indicates which watchlist the instrument is saved under.

Code - Security code/Instrument for the alert.

Last - Last price of instrument.

Match - Match Price of instrument.

Perc - Percentage difference between Last and Match.

MktCap - Market cap of the stock.

Rpt - Indicates whether if the company has made a recent report.

Info - News Source of the report.

Borrow - Indicates if there are borrowable stocks for the instrument.

CR - CapRasing.

Status - Trading Status:

 * H - Trading Halt

 * P - Pre-open

## Projects

A description of each project.

Please be advised that this section has been kept generic so that
it may be copied and pasted between solutions. Please replace *X*
with the name of the solution.

### X (GUI)

Desktop application deployed to director's PC. This desktop 
application interacts with a generic WebSockets API (WebSocketAlertFoundations).

### X.Algorithm

The back end of this project.

### Test/X.Algorithm.Tests

Any unit tests for the Algorithm project.

### Test/X.Algorithm.NarrowIntegration.Tests

This project sets up a narrow integration test with ZMQ pub/sub, in the
same way that a live version would work. The ZMQ publisher is local.
In a live version the publisher would be another machine on the network.

This project ensures that the API can subscribe, recieve, and process
messages sent from a ZMQ publisher.

### Test/X.Algorithm.DatafeedReplay.Tests

Running this project allows you to "replay a day" locally (e.g. yesterday's 
data). The project will run as fast as your computer will allow, so it can
make testing dynamic alerts difficult (there are obviously ways to tweak this
if needed, just keep it in mind).

##### Configuration

###### Credentials

You will need to add your AWS credentials to the 
X.Algorithm.DatafeedReplay.Tests\App.config file within this project.

###### Choose Day

Update the `DayOffset` variable within X.Algorithm.DatafeedReplay.Tests\Program.cs. 
Here you can use 0 for today's date, or use a negative number to go back X amount of days.