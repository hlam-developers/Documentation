# NewsGatheringTool

A tool that searches for news for a given instrument.

Currently, the tool searches through these sources for articles:
- The Australian
- AFR
- Bloomberg

## User Guide
1. Begin by entering a code into the Code input box, then press Enter. This will bring up both the correlated search terms for the particular instrument and any previous search results for the instrument.
2. Choose whether you would like to personalise your search by selecting your search parameters and/or your search terms.
3. Once ready, press Search. The search is complete once the Search button returns from *Searching...* to *Search*. 

If multiple people are using the tool at once, the requests are processed on a FIFO basis (i.e. a queue). This is currently the fastest way to implement the algorithm without weighing down our servers.

## Documentation

## NewsGatheringTool.Blazor
The blazor application uses standard _[TradingToolCommon.Blazor](https://github.com/hlam-developers/TradingToolCommon)_ components.

## NewsGatheringTool.Algorithm
The algorithm runs constantly, looking for jobs. It runs similarly to a Notifications project. Once a job is found (i.e. the subscriber receives a new message from the _{env}-ngt-jobs_ table), the job is processed immediately, then deleted from the table once completed.

The HtmlHelpers for each source are tailored to each website's quirks/weird HTML. 
