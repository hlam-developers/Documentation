# NewsAlerts

A solution which presents News Articles from Iress as alerts, whenever the news
articles match user-defined filters.

## Projects

A description of each project.

### NewsAlerts (GUI)

Desktop application deployed to director's PC. This desktop
application interacts with a generic WebSockets API (WebSocketAlertFoundations).

### Column Headings
![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/news-alert-headers.png)

Time - When the alert was generated.

Pos - Indicated if a current position is held of the code indicated.

Code - The instrument relevant to the alert.

Headline - The headline of the article.

User - Who generated the filter that searches for the article.

Checked - Most recent person who checked/clicked on the article.

MktCap - Market Cap of the Code in question.

Date - The date when the alert was generated.

Keywords - The keyword that the filter uses to generate an alert from the article.

AndNots - The keyword that the filter uses to ignore an alerts for an article.

Keyword - Category of the Keyword filtering.

AndNot - Category of the AndNots filtering.

### Date Filter

#### Searching Through Existing Filters

The user can search through all existing filters by adjusting the following values:

* Keyword Type - The keyword type that is used to generate an alert from.

* Disabled Filters - Show alerts that aren't enabled.

* Show Filters For - Show alerts that a certain user has created.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/news-alert-show-filters.png)

#### Adding a Filter

The user can add their own filter to generate alert from an article.

* Keywords - The keyword that is used to create an alert from for each article.

* AndNots - If an keyword is found in the article, exclude it from generating an alert if has an `AndNots`

The below settings indicate where the keyword filtering would occur.

* Code - The instrument being referenced by the article.

* Headline - The headline of the article.

* Headline & Body - The headline and body of the article.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/news-alert-add-filters.png)

#### Existing Filters

* Keywords - The keyword that is used to create an alert from for each article.

* AndNots - If an keyword is found in the article, exclude it from generating an alert if has an `AndNots`

* Keyword Search Type - The settings of where the `Keyword` filtering would occur.

* AndNot Search Type - The settings of where the `AndNot` filtering would occur.

* User - The user that generated the filter for the alert.

* Force - News matching this filter should always generate an alert, bypassing any Excluded Keywords.

* Enabled - Controls whether the status should be on/off.

* 🔔 - Also send to the Notifications Tool.

* ACtions - Option to delete the filter from the filter list.

![alt text](https://hlam-static-public.s3.ap-southeast-2.amazonaws.com/news-alert-filters-delete.png)

### NewsAlerts.Datafeed

Responsible for extracting news from Iress and persisting this to the DynamoDB
table {env}-news-library.

Please refer to the `cloudformation` folder within this project, as this folder contains
additional serverless projects and readmes.

### NewsLibrary.Tokeniser

Note that this serverless project is not contained within this repository, however it's
kept here for reference as it's an important piece of the puzzle.

The purpose of this project is to tokenise each article as it comes through,
preparing it for later correlation by the NewsLibrary.Processor project 
defined below.

In hindsight this project name is now misleading - as there's two projects called
news library "processor":

https://bitbucket.org/tradingtools_core/newslibraryprocessor/src/master/

### NewsLibrary.Processor

Responsible for processing tokenised articles (refer to NewsLibrary.Tokeniser above) 
and drawing correlations between companies.

This data is currently only displayed in the News Gathering Tool, but may serve another
purpose in the future.

This runs on an EC2 instance and is a nightly process.