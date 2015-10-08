---
title: Merus API Reference

language_tabs:
  - shell
  - javascript

toc_footers:
  - <a href='#creating-an-app'>Sign Up for a Developer Key</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

Welcome to the Merus API dcoumentation.

Sample code is available in shell and JavaScript.

## Stability

Endpoints (and some endpoints' parameters) are annotated with a stability attribute, which will be set to one of the following values:

Stability     | Description
--------------|------------
`production`  | Ready for production use; only subject to emergency or compatible changes.
`development` | Ready for use in developing new features; subject to breaking changes.
`private`     | Intended for internal Merus use only; subject to change and compabilty only with internal apps.

# Authentication

## OAuth

The Merus API uses OAuth 2.0 to provide third party apps with access to users' accounts without directly requiring their password.

Accessing most endpoints requires an OAuth access token.

In order to use OAuth, you must [register an app](#apps) with the API. This will provide an app id and key which can be used to authenticate users.

1. Redirect users to request access:

    ### Request

    `GET https://api.meruscase.com/auth/authorize`

    ### Parameters

    Name           | Type    | Description
    ---------------|---------|---------------------------------------------------------------------------
    `client_id`    | integer | **Required** The app's id
    `redirect_uri` | string  | **Required** The app's callback URL
    `response_type`| string  | **Required** The OAuth2 response type. Currently only `code` is supported.

2. Exchange the authorization code for an access token

    After the user has authorized the application, they will be redirected
    to the `redirect_uri`, with a `code` param. This param can be exchanged
    for an access token:

    ### Request

    `POST https://api.meruscase.com/auth/token`

    ### Parameters

    Name            | Type    | Description
    ----------------|---------|-------------------------------------------------------------------------------
    `client_id`     | integer | **Required** The app's id
    `client_secret` | string  | **Required** The app's secret key
    `code`          | string  | **Required** The OAuth2 access code received after a successful authorization.

## HTTP Basic Auth

```shell
curl -u user@example.com https://api.meruscase.com/v1/users/me
```

```javascript
var Merus = require('merus'),
    merus = Merus('user@example.com', 'opensesame');

merus.users.me(function(err, data){
  console.log(data);
});
```

```json
{
  "id": 2000,
  "username": "user@example.com",
  "firm_id": 101,
  "user_type_id": 9,
  "created": "2013-07-19T10:35:08.000Z",
  "modified": "2013-07-19T15:04:04.000Z"
}
```

The API can also be accessed with HTTP basic auth, using a user's credentials. This is provided for scripting and debugging purposes.

<aside class="warning">
Third party applications <b>must not ask for or collect Merus credentials</b>. Users should be directed through the OAuth flow.
</aside>

## Rate Limiting

The API limits apps to 1000 requests per user per hour.

And app can observe the rate limit, remaining quota, and reset time in any API request's response headers.

```http
HTTP/1.1 200 OK
X-Ratelimit-Limit: 1000
X-Ratelimit-Remaining: 998
X-Ratelimit-Reset: 1433196000
```

## Creating a User

Stability `Private`

Creates a new user.

<aside class="notice">&#128679; Documentation is in progress. &#128679;</aside>

## Creating an Authorization

Stability `Private`

Creates an authorization on behalf of a user.

<aside class="notice">&#128679; Documentation is in progress. &#128679;</aside>

# Activities

## Getting a list of activity tags

```shell
curl -H 'Authorization: Bearer 06d83c40-c22f-482c-ae30-31d10fd8e9e6' \
  https://api.meruscase.com/v1/activities/tags
```

```javascript
merus.activities.tags(function(err, res){
  console.log(res);
});
```

```json
[{
  "id": 1,
  "name": "Hidden",
  "is_global": true
},{
  "id": 2,
  "name": "Automatic",
  "is_global": true
},{
  "id": 19618,
  "name": "In Person Visit",
  "is_global": false
}]
```

Stability: `development`

Gets a list of activity tags accessible to the user.

### Request

`GET /v1/activities/tags`

# Apps

## Getting Apps

```shell
curl -u hello@example.com https://api.meruscase.com/v1/apps
```

```javascript

merus.apps.all(function(err, res){
  console.log(res);
});

```

```json
[{
  "id": 12,
  "name": "Hello World",
  "key": "b827e00a-5924-42b4-a59f-2dccc557e68b",
  "callback_url": "https://example.com/auth/kepler/callback"
},{
  "id": 14,
  "name": "FaceSpace",
  "key": "2f337c7a-ec5e-4878-bed0-b2ef4686159c",
  "callback_url": "https://example.org/auth/kepler/callback"
}]
```

Stability: `Production`

Lists all apps registered to a user.

### Request

`GET /v1/apps`

## Creating an App

```shell
curl -u hello@example.com \
  -d name="Hello World" \
  -d redirect_uri=https://example.com/auth/merus/callback \
  https://api.meruscase.com/v1/apps
```

```javascript

merus.apps.create(function(err, res){
  console.log(res);
});
```

```json
{
  "id": 12,
  "name": "Hello World",
  "key": "b827e00a-5924-42b4-a59f-2dccc557e68b",
  "callback_url": "https://example.com/auth/kepler/callback"
}
```

Stability: `Production`

API consumers must register an app in order to initiate OAuth sessions and act on behalf of a user. Apps may be created using an existing Merus account.

### Request

`POST /v1/apps`

### Parameters

Name           | Type   | Description
---------------|--------|------------------------------------------
`redirect_uri` | string | **Required** The application's callback URL
`name`         | string | The application's display name

# CaseFiles

## Searching for a casefile

```shell
curl -H 'Authorization: Bearer 06d83c40-c22f-482c-ae30-31d10fd8e9e6' \
  https://api.meruscase.com/v1/casefiles/search?name=ADJ2001
```

```javascript
var params = {name: 'ADJ2001'};
merus.casefiles.search(params, function(err, res){
  console.log(res);
});
```

Stability: `Production`

Searches for casefiles that match the given query parameters. Search parameters
use an implicit wildcard, so a parameter with a value of "ABC" will match a
casefile with "ABC123".

### Request

`GET /v1/casefiles/search`

### Parameters

Name               | Type   | Description
-------------------|--------|------------------------------------------
`name`             | string | The casefile's name

## Adding a document

```shell
curl -H 'Authorization: Bearer 06d83c40-c22f-482c-ae30-31d10fd8e9e6' \
  -F 'file=@doc.rtf;type=application/rtf' \
  https://api.meruscase.com/v1/casefiles/100/documents

# Or, to upload with a custom activity:

curl -H 'Authorization: Bearer 06d83c40-c22f-482c-ae30-31d10fd8e9e6' \
  -F 'file=@doc.rtf;type=application/rtf' \
  -F 'activity[body]=Hello, Merus!' \
  -F 'activity[tags][]=Support' \
  https://api.meruscase.com/v1/casefiles/101/documents
```

Stability: `Production`

Adds a document to the casefile.

An activity is created in the casefile along with the document. This activity is autogenerated by default and has a tag of "Document". Additional tags can be specified, and the activity's body can be set as well. Tags must be available in the user's firm, otherwise they will be silently discarded.

### Request

`POST /v1/casefiles/:id/documents`

### Parameters

Name               | Type   | Stability     | Description
-------------------|--------|---------------|------------------
`file`             | file   | `production`  | The file contents
`activity[body]`   | string | `development` | The body of the activity that will be attached to the casefile.
`activity[tags][]` | string | `development` | A string name of a tag that will be applied to the activity. May be repeated for multiple tags.

### Supported Document Types

The following MIME types are supported:

* `application/pdf`
* `application/vnd.openxmlformats-officedocument.wordprocessingml.document`
* `application/msword`
* `application/wordperfect`
* `application/rtf`
* `text/plain`

## Adding a ledger item

```shell
curl -H 'Authorization: Bearer 06d83c40-c22f-482c-ae30-31d10fd8e9e6' \
  https://api.meruscase.com/v1/casefiles/100/ledgers \
  -d 'user_id=123' \
  -d 'created_by=123' \
  -d 'ledger_type=expense/generic' \
  -d 'description=Lunch with Bob' \
  -d 'date=2015-10-04 12:30:00' \
  -d 'amount=40.00'
```

Stability: `Production`

Adds an entry to the ledger.

The entry should have one of the following:

* an `amount`
* a number of `hours` and an `hourly_rate`
* or a `unit_cost` and `item_qty`

The amount will be calculated accordingly.

The bill-to contact will be set to the casefile's bill-to contact by default.

### Request

`POST /v1/casefiles/:id/ledgers`

### Parameters

Name                     | Type    | Description
-------------------------|---------|-------------------
`created_by`             | integer | The user who is attached to the ledger.
`ledger_type`            | string  | A string representation of one of the supported ledger types.
`description`            | string  | A description of the item.
`date`                   | date    | The date of the item.
`account`                | string  | The string representation of the account.
`hours`                  | float   | The number of hours associated with the entry.
`hourly_rate`            | float   | The hourly rate to compute the total of the entry.
`amount`                 | float   | The total amount of the item.
`unit_cost`              | float   | The amount of one unit associated with the entry.
`item_qty`               | integer | The quantity of items associate with the entry.
`task_code`              | string  | The task code
`activity_code`          | string  | The activity code
`expense_code`           | string  | The expense code
`alternate_billing_code` | string  | The alternate billing code
`minimum_time_increment` | integer | Billing increment in minutes. Either 6 (tenths) or 15 (quarters).

### Supported Ledger Types

The following ledger types are supported:

* `expense/generic`
* `invoice/billable-time`

### Supported Accounts

The following account slugs are supported:

* `operating/general`
* `client/general`

# Documents

## Adding a document

```shell
curl -H 'Authorization: Bearer 06d83c40-c22f-482c-ae30-31d10fd8e9e6' \
  -F 'file=@doc.pdf;type=application/pdf' \
  https://api.meruscase.com/v1/documents
```

Stability: `Production`

Adds a document to the firm's document index.

### Request

`POST /v1/documents`

### Parameters

Name   | Type  | Description
-------|-------|-------------------
`file` | file  | The file contents

### Supported Document Types

The following MIME types are supported:

* `application/pdf`

# Users

## Getting the current user
```shell
curl -H 'Authorization: Bearer 06d83c40-c22f-482c-ae30-31d10fd8e9e6' \
  https://api.meruscase.com/v1/users/me
```

```javascript
merus.users.me(function(err, res){
  console.log(res);
});
```

```json
{
  "id": 25,
  "username": "test-user",
  "created": "2015-04-14T17:47:20.000Z",
  "modified": "2015-04-14T17:47:20.000Z"
}
```

Stability: `Production`

Gets information on the current user

### Request
`GET /v1/users/me`

## Getting a list of users

```shell
curl -H 'Authorization: Bearer 06d83c40-c22f-482c-ae30-31d10fd8e9e6' \
  https://api.meruscase.com/v1/users
```

```javascript
merus.users(function(err, res){
  console.log(res);
});
```

```json
[{
  "id": 25,
  "username": "test-user",
  "created": "2015-04-14T17:47:20.000Z",
  "modified": "2015-04-14T17:47:20.000Z"
},{
  "id": 28,
  "username": "other-test-user",
  "created": "2015-04-14T17:47:20.000Z",
  "modified": "2015-04-14T17:47:20.000Z"
}]
```

Stability: `Production`

Gets information on all visible users

### Request
`GET /v1/users`
