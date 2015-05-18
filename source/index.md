---
title: Merus API Reference

language_tabs:
  - shell
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

Welcome to the Merus API dcoumentation.

Sample code is available in shell and JavaScript.

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

    `POST https://kepler.example.com/auth/token`

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

# Apps

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

### Request

`POST /v1/apps`

### Parameters

Name           | Type   | Description
---------------|--------|------------------------------------------
`redirect_uri` | string | **Required** The application's callback URL
`name`         | string | The applicatin's display name

# CaseFiles
# Documents
# Users

## Getting the current user
```shell
curl -H 'Authorization: Bearer 06d83c40-c22f-482c-ae30-31d10fd8e9e6' \
  https://api.meruscase.com/v1/users/me`
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

Gets information on the current user

### Request
`GET /v1/users`

## Getting a list of users

```shell
curl -H 'Authorization: Bearer 06d83c40-c22f-482c-ae30-31d10fd8e9e6' \
  https://api.meruscase.com/v1/users`
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

