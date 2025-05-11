# QuickMovers

## Algorithm Description

An algorithm that finds instruments that have moved quickly.

When an instrument's Last price changes, the algorithms checks to see if it should generate an alert. The Current Last Price is stored.

The Maximum and Minimum Last prices over the last 60 seconds are calculated.

The percentage movements from the Current Last Price, to these Maximum Last and the Minimum Last prices are calculated. The greater absolute % of these movements becomes the variable known as Perc Period.

The Perc Period must be greater than a certain amount to generate an alert. This amount differs based on the last price of the instrument:

* < $0.20  = 11%
* < $1.00  = 4%
* < $10.00 = 2%
* \>= $10.00 = 1.5%

Other notable features:

* If an alert is generated, another alert will not be generated for the same instrument in the next 5 minutes unless the last price moves at least 1% in either direction.
* Gargoonger triggers if an alert has been triggered for the same code 3 times (or more) in the last 60 seconds.

## Recovery

### Intro

QuickMovers has a recovery mechanism built in. This mechanism ensures that QuickMovers can start up at any time
of the day, accurately rebuild any alerts that would have been generated earlier in the day, and continue to accurately
rebuild alerts post-recovery.

The recovery mechanism allows QuickMovers to recover successfully if it has been stopped for a minute, an hour, or perhaps not even started for the entire day.

Not only is the recovery mechanism useful for failure recovery, it allows blue-green deployments to work without having duplicate or 
erroneous alerts generated. 

The simplest recovery mechanism would be to replay 100% of the day's data from DynamoDB. This would however take an incredibly long time, and thus we've built on top of this concept. The implemented recovery mechamism is not trivial. It is designed to recover as fast as possible, and thus pulls/collates data from multiple sources.

### Recovery Data

You might find it useful to refer to this table when reading through the recovery process below.

| Data | Description |
| --- | --- |
| Previous QuickMover alerts | All previously persisted QuickMover alerts for the day. |
| Last Alert Timestamp | The timestamp of the most recent alert persisted to DynamoDB (refer to Last Uptime below). |
| Last Tool Health Timestamp | The timestamp of the most recent tool health message persisted to DynamoDB (refer to Last Uptime below). |
| Last Uptime | This is the last known time that QuickMovers was running. Derived from the greater of: the last alert persisted to DynamoDB, or the last tool health record persisted to DynamoDB. In the case of a blue/green deployment, this timestamp would be very close to the current time (and that's perfectly acceptable). By using the greater of these two timestamps we can guarantee that QuickMovers was running at this given time, and thus don't need to attempt to rebuild prior alerts. |
| The state of each instrument, 60 seconds prior to the Last Uptime. | QuickMovers needs to know the state of every instrument at 60 seconds prior to the Last Uptime. QuickMovers must ignore live data when the Last and TradedVol have not changed. If we do not have the "last known state" for each instrument, we have no way to determine if the Last and TradedVol have changed as new data comes through. Not taking this step will lead to incorrect alerts being generated during recovery. |
| Datafeed from Last Uptime (minus 60 seconds) until now | QuickMovers generates alerts on the last 60 seconds of data, thus it is important to rebuild this 60 second window before we start generating alerts. This data comes from the datafeed DynamoDB table |
| New Data! | Once the recovery solution has completed and the algorithm's main loop starts, the algorithm will automatically get the last 5 minutes of data from the Datafeed's Zmq publisher. Any data prior to the latest data the algorithm has already seen is discarded. |

### Recovery Process

Please refer to the table above for descriptions of each of the data being referred to.

#### 1. Gather Data

1. Retrieve Previous QuickMover alerts.
2. Determine Last Alert Timestamp. 
3. Determine Last Tool Health Timestamp.
4. Determine the Last Uptime.
5. Retrieve the state of each instrument, 60 seconds prior to the Last Uptime.
6. Retrieve the datafeed from Last Uptime (minus 60 seconds) until now.

#### 2. Configure Algorithm

1. An instance of the Algorithm class is constructed.
2. The _state of each instrument, 60 seconds prior to the Last Uptime_ is fed into the algorithm's CalcAlerts function. This will not
generate any alerts (impossible when only one record for each instrument is fed into the algorithm); it is done as a step in setting up the algorithm's internal state to the way that it was prior to recovery.
3. The _Datafeed from Last Uptime (minus 60 seconds) until now_ is fed into the algorithm's CalcAlerts function. Once the _Last Update_ time is reached, we pause the playback and continue with the following steps. We will resume playback in Step 6.
4. Any alerts returned by the algorithm up to this point are discarded (as per Step 2, we are only feeding in data to the algorithm to set up it's internal state). Note that the algorithm still retains these alerts internally (e.g. to calculate gargoongers). We fix this in the next step.
5. At this point, the algorithm's state is almost back to the desired state. We now replace the Algorithm's internal alerts with those we retrieved from DynamoDB: _Previous QuickMover alerts_.
6. We now continue to feed in the data that we paused in Step 3. Any alerts from this point are _legitimate_, and we will persist these to DynamoDB.
7. The recovery has completed, and the algorithm main loop will commence. At the very start of the main loop - when messages are retrieved from Zmq - the last 5 minutes of messages will automatically be sent from the upstream datafeed. There is guaranteed to be data we've already seen, however the algorithm discards any data prior to the latest data it has already seen (thus also ignoring duplicate data).

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