# Alerts APIs

This readme covers the following projects in this repo:

* alerts-rest-api
* alerts-websockets-api
* alerts-websockets-api-streams-publisher

## Overview

There are two APIs in this project, a REST and WebSockets API. The WebSockets API is used for streaming updates to connected users. The WebSockets API does not perform any transactional operations **at all**. All updates are performed via REST, ensuring they remain transactional unlike the "send and pray" approach provided by WebSockets. Yes, you could roll your own transactional solution for WebSockets - but why?

The REST and WebSocket APIs have the concept of a "subscription". A user can subscribe to a DynamoDB table (and optionally one or more Partition Keys within that table). Once a user is subscribed, any updates to that table (restricted to Partition Keys within that table if requested) will be streamed to the user via WebSockets. This includes Creates, Updates and Deletes.

To make a subscription, the user must first connect to the WebSocket and retrieve their Connection ID (by sending a "getConnectionId" action). With this connection ID, they may then invoke the `/subscribe` endpoint (`POST`) in the REST API to subscribe.

A single connection to the WebSocket API can support a theoretically unlimited number of subscriptions.

The user may request "all" or the "latest" data from the DynamoDB table when subscribing. This data is returned in the `POST: /subscribe` HTTP response if less than 10MB. Otherwise, it is streamed to the user via their WebSocket.

They user may also request updates after a specific timestamp. This would return all new or updated records after this timestamp.

### Alerts via WebSockets - Sequence Diagram
https://lucid.app/lucidchart/c922f1ca-71bc-4764-9c36-d7a15080c248/edit?page=0_0#

## Authorization

A Json Web Token (JWT) is used to gain access to the Alerts REST and WebSocket APIs. This is an opaque token signed by AWS KMS. 

Users may obtain a JWT by sending a POST to the `/auth` endpoint with their username and IP address (implicitly). If we're aware of their username, their username and IP address will be whitelisted for 2 weeks, and a JWT will be generated and returned in the HTTP response with a 12 hour expiry

The token is comprised of a basic payload containing some details about the user (username, GUI). Relying parties (i.e. the Alerts REST or WebSocket APIs) may verify the token via KMS. This verifies the integrity of the token, ensuring that A) it has been issued by us, and B) it has not been modified.

If a user connects to the WebSockets API with a valid token, their IP address will be automatically added to the whitelist table. This ensures that if the user changes IP address, their new IP address will not require manual approval. This is super handy if the user fails over to 4G.

### Alerts API (REST/Socks Hybrid) Auth Flow Diagram

https://lucid.app/lucidchart/cff4ce1c-5cd9-4fdf-9181-2ac009e9c35d/edit?page=0_0#

## HTTP API

### Endpoints

* All POST endpoints require a JSON object in the body.
* All GET endpoints use regular query params.
* All endpoints have an associated OPTIONS endpoint to satisfy any browser CORS pre-flight requests.
* An asterisk denotes a required parameter.
* All endpoints other than `/auth` require an authorization token. This must be provided in the `Authorization` header and be prefixed by `"Bearer "`. (e.g. `Authorization: Bearer ey....`). Failure to do so will result in a 401 response.

##### POST: /auth

Public endpoint, accepts an auth payload containing pertitent details to ascertain whether or not the user is permitted to use the APIs. If they are permitted, a JWT token is generated and returned. The user may then use this token to interact with the rest of the API as well as the WebSocket API. If they are not permitted, support is notified via Slack and a 401 is returned to the user. 

A user is permitted if their IP address and username combination have already been seen and approved by support.

If approval is pending or the IP address and username combination has been denied, no further Slack notifications are raised.

Parameters:
* username [string] * - The connecting user's username.
* guiName [string] * - The application they're connecting from.
* guiVersion [string] * - The version of the application they're connecting from.

Example Request Body:

```
{
    "username": "bob",
    "guiName": "App 1",
    "guiVersion": "1.0.0.0"
}
```

Example Response 2:
(User is authorised)

```
{
    "statusCode": "200",
    // JWT token in body.
    "body": "ey..."
    "headers": {
        // Suitable CORS headers.
    }
}
```

Example Response 1:
(User is not authorised)

```
{
    "statusCode": "401",
    "headers": {
        // Suitable CORS headers.
    }
}
```

#### GET: /query

Query a DynamoDB table. 

This endpoint is intended to be very flexible to suit future requirements, however it may still need tweaking into the future.

Note: API Gateway does not support the `|` character anywhere in the URL. The C# HttpClient refuses to send it in encoded form (`%7C`). This seemed impossible to circumvent. `__PIPE__` may be sent in any of the query parameters below, and it will be converted to `|` before the Lambda starts processing the request.

Parameters:
* tableName [string] * - The table being queried.
* indexName [string] - The index to query.
* partitionKeyValue [string] - The partition key value to query. If not provided the table will be scanned.
* rangeKeyValue [string] - If this is provided, you must also provide partitionKeyValue. The range key within the partition key value to query. If not provided all records within the provided partitionKeyValue are returned.
* rangeKeyValue2 [string] - If this is provided, you must also provide rangeKeyValue. Used to provide the secondary value for a range key `between` query. This is only used when rangeKeyCondition is `between`.
* rangeKeyCondition [string] - The condition to use for the range key query. One of `eq`, `lt`, `lte`, `gt`, `gte`, `begins_with` or `between`.
* projectionExpression [string] - Comma separated list of fields to return, used to avoid sending redundant data to the client in large queries.
* scanIndexForward [bool] (default `true`) - Set to false to scan in reverse.
* limit [int] - Limit the number of records returned.


Example Request Body:

```
{
    "tableName": "env-alerts",
    "partitionKeyValue": "test-asx-20210831"
}
```

Example Response:

```
{
    "statusCode": "200",
    "body": "[{\"ToolExchangeDate\":\"test-asx-20210831\",\"Datetime\":\"2021-08-31-T11:34:00+00:00\",\"Instrument\":\"ZZA.ASX\"},...]"
    "headers": {
        // Suitable CORS headers.
    }
}
```

#### POST: /check-alerts-matching-instrument

Checks all alerts matching the given instrument across alerts from linked tools within today's date. Linked tools are the static trading tools: `qm,qmc,owl,owh,tml,tmh`.

This will publish checked alert updates to all connected websocket users.

Parameters:
* instrument [string] * - The instrument to check.
* toolExchangeDate [string] - The tool exchange date for the alert being checked, "today's date" is extracted from the date in this parameter.


Example Request Body:

```
{
    "instrument": "ZZA.ASX",
    "toolExchangeDate": "test-asx-20210831"
}
```

Example Response:

```
{
    "statusCode": "200",
    "headers": {
        // Suitable CORS headers.
    }
}
```

#### POST: /delete-item

Delete a given item.

Parameters:
* tableName [string] * - Table to delete the item from.
* item [object] - Item to delete


Example Request Body:

```
{
    "tableName": "test-alerts",
    "item": {
        "ToolExchangeDate": "test-asx-20210831",
        "Datetime": "2021-08-31-T11:34:00+00:00",
        "Instrument": "ZZA.ASX"
    }
}
```

Example Response:

```
{
    "statusCode": "200",
    "headers": {
        // Suitable CORS headers.
    }
}
```

#### POST: /update-item

Updates a given item. If the item does not exist, it is created.

The DynamoDB record is updated, not replaced. Any properties provided are used in the DynamoDB update operation, including those properties that are provided as `null`. If you do not wish to update a specific property, do not provide it in the `item`.

Please refer to the Auditing section for documentation on automated Auditing of updates.

Parameters:
* tableName [string] * - Table to update.
* item [object] - Item to update

Example Request Body:

```
{
    "tableName": "test-alerts",
    "item": {
        "ToolExchangeDate": "test-asx-20210831",
        "Datetime": "2021-08-31-T11:34:00+00:00",
        "CheckedBy": "mchan"
    }
}
```

Note: If the above record existed in DynamoDB and had a property `Instrument` with a value of `ZZA.ASX`, it would still have this property and value after the update operation.

Example Response:

```
{
    "statusCode": "200",
    "headers": {
        // Suitable CORS headers.
    }
}
```

#### POST: /send-sms

Send an SMS. 

Note that any user with a valid auth token can send any SMS to any number. This function could be abused. Environment variable ENABLE_SMS exists on the Lambda function to disable the actual sending of the SMS (to be changed if required in an emergency if something goes haywire).

Parameters:
* senderId [string] - What to display for the sender of the SMS. Truncated after 11 characters.
* mobileNumber [string] - Table to update. Note this should be in the format +614######## e.g. +61412345678. Do not contain leading zero for the "04" in mobile number.
* payload [string] - Payload to send

Example Request Body:

```
{
    "senderId": "My App",
    "mobileNumber": "+61412345678",    
    "payload": "This is a text message, how are you?",
}
```

#### POST: /subscribe

Subscribe a websocket connection or mobile number to one or more table partitions.

If the subscription already exists, this endpoint does nothing other than return a basic response (example further below).

Two subscription `type`s are supported, `SMS` or `WebSockets`. Note that `SMS` will only send alerts when they're created (not updated).

When the user is subscribing via `WebSockets` they may choose to:
* `getAll`: Load all data for the table + partition being subscribed to.
* `getLatest`: Get the latest record for the table + partition being subscribed to (using range key desc).

Due to Lambda request/response limits. If the user has requested to `getAll` or `getLatest`, the data will be sent back as part of the HTTP response if it is smaller than 5.58mb. If the response is larger, it is instead sent via websockets to the subscribed connection.

Parameters:
* connectionId [string] * - The connection ID to subscribe. For `SMS` subscription types this should be `sms_{username}`.
* tableName [object] * - Table to subscribe to.
* partitionKeyValues [List\<string\>] - Zero or more partition keys to subscribe to.
* type [string] - Subscription type: `SMS` or `WebSockets`.
* getAll [bool] - Load all data for the table + partition being subscribed to. Only valid for `WebSockets` subscription types.
* getLatest [bool] - Get the latest record for the table + partition being subscribed to (using range key desc). Only valid for `WebSockets` subscription types.
* afterLastUpdateDatetime [string] - An ISO8601 timestamp. Valid when using `getAll`. Only alerts with a `LastUpdateDatetime` after this timestamp will be returned in the subscription response.
* projectionExpression [string] - Valid when using `getAll`. Used to limit the fields returned in the initial subscription payload. Useful is you're only interested a subset of fields in a large dataset. Updates sent during the subscription will still contain all fields.
* ttl [int] - TTL for the subscription, if not provided subscription expires in 2.5 hours (API Gateway will automatically disconnect the websocket connection after 2 hours).
* filterV2 [string] - A stringified object containing an appropriate filter for filtering alerts. Refer to Subscription Filtering below.
* smsFormatV2 [string] - A format to use when sending an alert via SMS. Uses the Sigulink format: https://github.com/Singulink/Singulink.Text.TokenFormatter. The alert is available via the `Alert` token.  Only valid for `SMS` subscription types.
* smsMobile [string] - An international formatted phone number (e.g. +61400111222) for sending SMS alerts. Only valid for `SMS` subscription types.
* smsSenderId [string] - The sender ID to use when sending the SMS. Only valid for `SMS` subscription types.

Response Parameters (body):
* Items [List\<object\>] -  Will be null if the user did not request any data via `getAll` or `getLatest`, or the user was already subscribed. Otherwise will be list containing 0 or more records.
* SentViaWebsockets [bool] - Bool to indicate if the `getAll` or `getLatest` data was sent via websockets due to the size of the data exceeding 9.8mb.

Example Response (user requested `getAll` and not already subscribed to the table + partitionKeyValue):

```
{
    "statusCode": "200",
    "body": "{\"Items\":[{\"ToolExchangeDate\":\"test-asx-20210831\",\"Datetime\":\"2021-08-31-T11:34:00+00:00\",\"Instrument\":\"ZZA.ASX\"},...],\"SentViaWebsockets\":false}
    "headers": {
        // Suitable CORS headers.
    }
}
```

Example Response (user requested `getAll` and not already subscribed to the table + partitionKeyValue, but the payload was too large for the HTTP response):

Note: Data is pushed via websocket instead.

```
{
    "statusCode": "200",
    "body": "{\"Items\":[],\"SentViaWebsockets\":true}
    "headers": {
        // Suitable CORS headers.
    }
}
```

Example Response (user requested `getAll` but was already subscribed to the table + partitionKeyValue):

```
{
    "statusCode": "200",
    "body": "{\"Items\":null,\"SentViaWebsockets\":false}
    "headers": {
        // Suitable CORS headers.
    }
}
```

##### Subscription Filtering:

When subscribing the user may provide a filter to reduce the number of alerts they receive. Note that the API supports specific filters as listed below. This used to be dynamic with Python but provide difficult with dotnet on Lambda.

###### Notification Category Filter

Filters alerts to only those with a matching `Category`.

When subscribing, `filterV2` should be a *stringified JSON object* containing the following:
* $type [string] - `Notifications`
* Categories [List\<string\>] - One or more categories to match on.

Example:
```
{
    "$type": "Notifications",
    "Categories": [
        "Staff",
        "AnotherCategory"
    ]
}
```

The following alert would match the above filter.

```
{
    "ToolExchangeDate": "Something",
    "DateTime": "Today",
    "Category": "Staff"
}
```

Example Request Body:

```
{
    "tableName": "test-alerts",
    "connectionId": "abc-123",
    "partitionKeyValues": [
        "test-asx-20210831"
    ],
    "getAll": true
}
```

### Archiving

Audit records are automatically created when an item is deleted in DynamoDB (delete event comes via streams), provided that an archive table exists for the source table. The audit table must be constructed as follows:

* Archive table must have same name as the source table, suffixed by `-archive`. E.g. if table source table is `investments`, archive table must be named `investments-archive`.
* The keys of the archive table must match that of the source table. 
* The `ArchiveDatetime` property is reserved. If the item being deleted already has this property, it will be replaced. 

When the stream Lambda handler `alerts-websockets-api-streams-publisher-stream-handler` receives a delete event and an archive table exists for the soure table, it pushes a record to the `alerts-websockets-api-streams-publisher-create-archive-record-queue` SQS queue. Another Lambda processes this SQS queue and creates the archive record.

### Auditing

Audit records are automatically created when a POST is made to `/update-item`, provided that an audit table exists for the source table. The audit table must be constructed as follows:

* Audit table must have same name as the source table, suffixed by `-audit`. E.g. if table source table is `investments`, audit table must be named `investments-audit`.
* The HASH key of the Audit table must be derived from all the keys in the source table, joined together. E.g. if the source table has a HASH and a RANGE key of `OrderType` and `CreateDatetime`, the HASH key of the Audit table must be `OrderTypeCreateDatetime`. 
* The RANGE key of the Audit table must be `UpdateDatetime`.

When the Lambda beneath the `/update-item` path detects that an audit table exists for the item being updated, it pushes a record to the `alerts-rest-api-create-audit-record-queue` SQS queue. A separate Lambda processes this SQS queue and creates the audit record.

Each record in an audit table captures a single changeset (i.e. an individual call to `/update-item`). The original values are not stored in the changeset, only the new values are. This means that a changeset record can tell you what fields changed, what they changed to, and when they changed, but it doesn't tell you their original values changed from. You may need to query the full audit trail for a given record to determine the _old_ value for a given changeset. When a source record a created for the very first time using `/update-item`, the full record is persisted to the audit table.

A diagram detailing this audit process is available here:

https://lucid.app/lucidchart/151a67a2-a1f7-4c96-9e07-97ff554cb6a2/edit?page=0_0#

An example of create / update follows, depicting the state changes in DynamoDB in both the source and audit tables.

---

Step 1: User `bob` creates record via a POST to `/update-item`.

State of **investments-table** after update.

| OrderType | CreateDatetime       | QtyRequired |
| --------- | -------------------- | ----------- |
| DayOrder  | 2021-10-05T16:14:00Z | 5           |

State of **investments-table-audit** after update.

| OrderTypeCreateDatetime        | UpdateDatetime             | UpdateUsername | QtyRequired |
| ------------------------------ | -------------------------- | -------------- | ----------- |
| DayOrder\|2021-10-05T16:14:00Z | 2021-10-05T16:14:01.48910Z | bob            | 5           |

---

Step 2: User `alice` updates the same record via a POST to `/update-item`, adding a new property `QtyHeld`

State of **investments-table** after update.

| OrderType | CreateDatetime       | QtyRequired | QtyHeld |
| --------- | -------------------- | ----------- | ------- |
| DayOrder  | 2021-10-05T16:14:00Z | 5           | 4       |

State of **investments-table-audit** after update.

_Note: In contrast to `null`, `<no_prop>` means that the property is not at present on the record at all._

| OrderTypeCreateDatetime        | UpdateDatetime             | UpdateUsername | QtyRequired | QtyHeld     |
| ------------------------------ | -------------------------- | -------------- | ----------- | ----------- |
| DayOrder\|2021-10-05T16:14:00Z | 2021-10-05T16:14:32.44562Z | alice          | \<no_prop\> | 4           |
| DayOrder\|2021-10-05T16:14:00Z | 2021-10-05T16:14:01.48910Z | bob            | 5           | \<no_prop\> |

---

## WebSockets API

Please refer to the above documentation for notes on Authorization and Subscriptions. This section largely documents the payloads sent to subscribed users. Knowledge from earlier sections about how the user gains access to the WebSockets API and how the user subscribes is assumed.

### Routes:

There are a very limited number of routes; REST is used as much as possible.

#### $connect

Called when a user connects. This invokes a Lambda which persists the user's connection details to the `env-ws-connections` DynamoDB table.

#### $disconnect

Called when a user disconnects. This invokes a Lambda which removes all data about the connection from DynamoDB. This includes deleting their connection from `env-ws-connections` and deleting any subscriptions ffrom `env-ws-subscriptions`.

#### getConnectionId

A route which simply returns the user's connection ID to them. The user requires this connection ID in order to subscribe via REST.

```
200: {"$type":"GetConnectionId","ConnectionId":"abc-123"}
```

#### ping

A route which sends a 200 response to the connected user. Used by clients to verify that the connection is alive.

```
200: {"$type":"Pong"}
```

### Subscribed Data Events

When table updates are streamed to the user, the following payload structure is used.

Note that updates are streamed to users in Table + Partition Key Value groups. This is irrespective of whether or not the user has subscribed to all Partition Key Values in the table.

```
{
    "$type": "Alerts",
    "NewOrUpdated": [
        {
            ...
        },
        ...
    ], 
    "Deleted": [
        {
            ...
        },
        ...
    ],
    "TableName": "env-alerts",
    "PartitionKeyName": "ToolExchangeDate",
    "PartitionKeyValue": "owl-asx-20210904",
    "Chunk": 1,
    "ChunkOf": 1,
    "InitialPayload": false
}
```

Fields:

* $type [string] - The type of payload. Always "Alerts" in this instance.
* NewOrUpdated [List\<object\>] - Any new or updated records (in full).
* Deleted [List\<object\>] - Any deleted records (in full).
* TableName [string] - The table that has raised the update. All alerts contained in this update have come from this table.
* PartitionKeyName [string] - The name of the partition key in the table.
* PartitionKeyValue [string] - The value of the partition key that has raised the update. All alerts contained in the update will have this partition key value.
* Chunk [int] - The number chunk (i.e. 1: chunk 1 of ?).
* ChunkOf [int] - The total number of chunks chunk (i.e. 3: chunk ? of 3).
* InitialPayload [bool] - True when streaming / returning the initial data to the user at subscribe-time, otherwise false.

## API Familiarisation Tutorial

To get a better understanding of how the API's are pieced together it may be useful to follow the below tutorial. This will get you set up and interacting with 
both of the API's via Insomnia (a desktop API client).

In this tutorial we will:

1. Set up Insomnia to interact with the APIs.
2. Obtain an Auth token.
3. Query data from the instruments table.
4. Subscribe to alerts via websockets.

Lets get into it.

1. Install and launch Insomnia Free https://insomnia.rest/
2. Obtain the OpenAPI specification for the Alerts REST API. Please note that these steps may change if AWS update their UI. If this then use brain.
   1. Log into the AWS console.
   2. Go to the Amazon API Gateway dashboard.
   3. Open the `alerts-rest-api` API.
   4. Open Stages on the left.
   5. Select the Prod stage on the left.
   6. Click the Export tab on the right.
   7. Select the "OpenAPI 3" option and export as YAML.
   8. Copy the YAML to clipboard.
      <br/>![](readme-export-openapi-3-specification.png)
3. Import OpenAPI specification into Insomnia.
   1. In Insomnia you should be able to find a Create dropdown button somewhere (top right). Click this and select the Import From Clipboard option.
   2. Import it into a New Workspace, and as a Request Collection.
   3. Open your newly imported Request Collection.
      <br/>![](readme-import-insomnia.png)
4. Obtain Auth token from the Alerts REST API.
   1. Select the POST /auth on the left.
   2. Modify the JSON body. Replace `your_username` with your username:
        ```
        {
            "guiVersion": "1.0.0.0",
            "guiName": "Insomnia",
            "username": "your_username"
        }
        ```
    1. Click Send.
    2. If the `username` was known by the API then you should receive a 200 response with a token in the body (long string starting with ey...).
       1. If not, then there should have been an approval request sent to Slack. Approve this and send the request again in 5-10 seconds, you should then receive the token.
    3. Copy token to clipboard.
5.  Lets take a look at this token (optional).
    1. Open https://jwt.io 
    2. Paste the token into the Encoded section.
    3. Look at the Payload section on the right.
       1. `iat` is issued at, `exp` is expiry, then it's got a few other bits and pieces identifying the user.
6. Now we'll start querying some data from the Alerts REST API using the token. 
    1. Back in Insomnia. We need to add the token to our environment settings.
    2. There should be a dropdown around the top left: "OpenAPI env". Click this. Click Manage Environments.
    3. It should display a JSON object which you can modify.
    4. Change the `authorization` property to `Bearer {token}` replacing `{token}` with the token you got earlier. E.g. `Bearer eyJhbGciOiJSUz...`
    5. Click Done or close the window.
    6. Click GET /query on the left.
    7.  Click the Query tab. We need to update some of the variables.
        1. Set `tableName` to `uat-instruments`.
        2. Set `partitionKeyValue` to `asx-yyyyMMdd` replacing `yyyyMMdd` with today's date e.g. `20221031`.
        3. Set `rangeKeyValue` to `BHP.ASX`.
        4. Set `rangeKeyCondition` to `eq`.
        5. Ensure all of the Query variables you just changed are checked (enabled). They should appear in the URL preview at the top.
        6. Ensure no other Query variables are checked.
    8.  Click the Header tab. This is annoying but we need to disable that first `Authorization` header.
        <br/>![](readme-insomnia-disable-auth-header.png)

    9.  Click Send. You should receive a 200 with the BHP.ASX instrument.
        <br/>![](readme-insomnia-query-response.png)
7. Now we'll subscribe to streaming data via WebSockets.
    1. Install `wscat`. You'll need to install `node.js` and then install `wscat` globally via npm. https://www.npmjs.com/package/wscat
    2. Connect to the API Gateway WebSocket endpoint:
        1. `wscat -c https://socks.alerts.uat.31may.trade?authorizationToken=Bearer%20{token}` replacing `{token}` with the token you got earlier. E.g. `wscat -c https://socks.alerts.uat.31may.trade?authorizationToken=Bearer%20eyJhbG...`
    3. Get your WebSocket Connection ID. Send the following payload:
        ```
        > {"action":"getConnectionId"}
        < {"$type":"GetConnectionId","ConnectionId":"a2ejMASKED="}
        ```
    4. Copy the Connection ID value to your clipboard (`a2ejMASKED=`)
    5. Leave the `wscat` window running, we'll return to this later.
    6. Back to Insomnia. Click POST /subscribe on the left.
    7. In the JSON tab on the right, edit the payload. You'll need to use the correct Connection ID you copied before, and replace `yyyyMMdd` with today's date e.g. `20221031`:
        ```
        {
            "getAll": true,
            "connectionId": "a2ejMASKED=",
            "tableName": "uat-alerts",
            "partitionKeyValues": [
                "pm-asx-yyyyMMdd"
            ]
        }
        ```
    8.  Click the Header tab. This is annoying but we need to disable that first `Authorization` header.
        <br/>![](readme-insomnia-disable-auth-header.png)
    9. Click Send. You should receive a 200 reply with the today's Position data in the response.
    10. Check your `wscat` window. You should eventually see changes streaming through.
8. Finished! To clean up you can simply CTRL+C the `wscat` window which will close the WebSocket connection and void any subscriptions.

A few footnotes on the tutorial. WebSockets connections can only live for a maximum of 2 hours, but may be terminated sooner for various reasons. The C# clients that we've developed
have logic to automatically reconnect and resubscribe, and query for any data missed during the brief connection outage. A WebSocket connection can have multiple subscriptions, in our
example we just created one subscription. Finally, there is presently no way to unsubscribe without terminating the WebSocket connection.