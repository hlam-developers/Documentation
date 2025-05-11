# CxaTmArb
## Installation

The application can be accessed [here](https://cxatmarb.hlamtools.trade/).

## Algorithm Description

An algorithm that arbitrage opportunities between ASX and CXA.

Arbitrage opportunities are found throughout the day, as well as pre-open. Note that pre-open phases occur before and after market open.

### Intra-day Arbitrage Opportunities
* Forward: BUY from **CXA** at _ask_, SELL to **ASX** at _bid_
* Reverse: BUY from **ASX** at _ask_, SELL to **CXA** at _bid_

### Pre-Open Arbitrage Opportunities
* Forward: BUY from **CXA** at _ask_, SELL to **ASX** (at _match_ before open, at _last_ after close)
* Reverse: BUY from **ASX** (at _match_ before open, at _last_ after close), SELL to **CXA** at _bid_

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
