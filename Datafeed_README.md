# Datafeed

This readme groups the projects contained within this repository into three categories.

* Instrument Data Sources
* Datafeed Data Sources
* Datafeed Supporting Projects

# Instrument Data Sources

Data source for instruments and associated metadata.

## Datafeed.LiveAlerts.StartOfDay

This project generates daily lists of instruments that the _Data Sources_ should subscribe to for the day.

This project identifies suitable instruments based on pre-defined criteria, and persists these to the {env}-instruments table. When the _Data Sources_ start up, they query this list and subscribe to changes for these instruments. 

The logic used to identify suitable instruments is complex, ever changing, and thus difficult to document in this readme. The best guidance is to refer to the code itself.

This project takes approximately half an hour to run. It starts up, obtains the instruments, persists them and exits.

The records in the instruments table serve numerous other purposes, however these are out of scope for this readme. 

## Datafeed.FpMarkets

FpMarkets provides borrowable shares for shorting. 

This project scrapes the FpMarkets website and updates the "Borrowable" field for instruments which are shortable via FpMarkets.

This project runs once daily (an hour after Datafeed.LiveAlerts.StartOfDay starts).

# Datafeed Data Sources

Extracts data from Iress and publish changes via ZMQ.

## Datafeed.Excel - _No longer in use. Deprecated and replaced by Datafeed.Iress._

Extracts live instrument data from Iress via Excel and publishes changes via ZMQ. Also able to persist changes directly to DynamoDB.

## Datafeed.Iress

Extracts live instrument data from Iress via Desktop Web Services (SOAP) and publishes changes via ZMQ. Also able to persist changes directly to DynamoDB.

# Datafeed Supporting Projects

The following projects are considered supporting projects. They do not directly obtain live instrument data but instead support the other projects that do. These projects are no longer in use.

## Datafeed.Consolidation - _Not currently in use._

Subscribes to one or more existing ZMQ datafeeds and consolidates the messages into a single ZMQ datafeed.

## Datafeed.DynamoDB - _Not currently in use._

Subscribes to an existing ZMQ datafeed and persists receives records to DynamoDB.