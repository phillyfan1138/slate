---
title: API Reference

language_tabs:
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Model API! You can use our API to access Model API endpoints.

We have language bindings in Javascript, though any websocket enabled program should be able to access the model APIs. You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

##SSL Connection

URI: `wss://myBank.com:3000/ws`

URL: `https://myBank.com/v1/models`

# Authentication

Authentication is Active Directory based.  

`Authorization: personalkey`

<aside class="notice">
You must replace <code>personalkey</code> with your personal API key.
</aside>

# Public

## Get All Models

### HTTP Request



```javascript
var request = require('request');
var url='https://myBank.com/v1/models/:OPTIONAL_ID';
request.get(url, function(error, response, body){
  console.log(body);
});
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Market Risk Model",
    "rating": "High",
    "runtime": 100000,
    "permission": 8
  },
  {
    "id": 2,
    "name": "Economic Capital Model",
    "rating": "High",
    "runtime": 10000,
    "permission": 6
  }
]
```
This endpoint retrieves all models.

`GET https://myBank.com/v1/models/:OPTIONAL_ID`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
id | none | If not set, then all models returned

##Get model inputs
```javascript
var request = require('request');
var url='https://myBank.com/v1/models/:MODEL_ID/inputs';
request.get(url, function(error, response, body){
  console.log(body);
});
```
> The above command returns JSON structured like this:

```json
{
  "id": "<ID>",
  "port":"<PORT>",
  "inputs": {
    "rate":0.04
  }
}
```
This endpoint retrieves the inputs that the model is expecting and the type.

`GET https://myBank.com/v1/models/:MODEL_ID/inputs`

The response returns a JSON object with the following results:

Parameter | Value | Description
--------- | ------- | -----------
id | integer | Model ID
port | integer | Websocket port
inputs | object | Key value mapping of input name to input type.  For example, "rate":"number" indicates that the model is expecting a parameter called "rate" which has a numeric value.



##Run a model
```javascript
var WebSocket=require('ws'); //if using nodejs
var w = new WebSocket("wss://myBank.com:<PORT>/ws/");
w.send(JSON.stringify({
    "id": "<ID>",
    "action": "run",
    "inputs":"<INPUT_OBJECT>"
}));
```
> The above command returns JSON structured like this:

```json
{
  "id": "<ID>",
  "runtime": "<RUNTIME_IN_MS>",
  "result": [
    {
      "forecast":500
    }
  ]
}
```
This command runs a model.  

`wss://myBank.com:<PORT>/ws/`

Note that the ability to run a model depends on the level of permission.  A person authorized for level 7 may run the EC model but not the Market Risk Model, for example.  This command is necessarily websocket based as each model hosts its own websocket server.  The inputs value should correspond to the results of the request for the model inputs.  The port comes from the results of the request for the model inputs.

##Get most recent model results

```javascript
var request = require('request');
var url='https://myBank.com/v1/models/:MODEL_ID/results';
request.get(url, function(error, response, body){
  console.log(body);
});
```

> The above command returns JSON structured like this:

```json
{
  "id": 1,
  "results": [
    {
      "forecast":500
    }
  ],
  "runtime": "<RUNTIME_IN_MS>",
  "timestamp": "<JAVASCRIPT_TIMESTAMP>",
  "inputs":{
    "rate":0.04
  }
}
```
This endpoint retrieves the most recent results from a given model.

`GET https://myBank.com/v1/models/:MODEL_ID/results`

Parameter | Value | Description
--------- | ------- | -----------
id | integer | Model ID
timestamp | date | Time of last model run
results | array | Array of results which depend on the model
inputs | object | Key value mapping of input name to input type for the inputs used in the model run

##Get ongoing monitoring results

```javascript
var request = require('request');
var url='https://myBank.com/v1/models/:MODEL_ID/ongoingmonitoring';
request.get(url, function(error, response, body){
  console.log(body);
});
```
> The above command returns JSON structured like this:

```json
{
  "id": "<ID>",
  "breaches": [
    {
      "timestamp":1459717356770,
      "expected":500,
      "actual":520,
      "attestation":1459717356774
    },
  ],
  "timestamp": "<JAVASCRIPT_TIMESTAMP>"
}
```
This endpoint retrieves the ongoing monitoring results from a given model.

`GET https://myBank.com/v1/models/:MODEL_ID/ongoingmonitoring`

The response returns a JSON object with the following results:

Parameter | Value | Description
--------- | ------- | -----------
id | integer | Model ID
timestamp | date | Time of last update to ongoing monitoring
breaches | array | Array of breaches of the threshold



##Get model assumptions
```javascript
var request = require('request');
var url='https://myBank.com/v1/models/:MODEL_ID/assumptions';
request.get(url, function(error, response, body){
  console.log(body);
});
```
> The above command returns JSON structured like this:

```json
{
  "id": 1,
  "assumptions": [
    {
      "assumption":"linearity",
      "severity":8
    }
  ],
  "timestamp": "<JAVASCRIPT_TIMESTAMP>"
}
```
This endpoint retrieves the ongoing monitoring results from a given model.

`GET https://myBank.com/v1/models/:MODEL_ID/assumptions`

The response returns a JSON object with the following results:

Parameter | Value | Description
--------- | ------- | -----------
id | integer | Model ID
timestamp | date | Time of last update to the assumptions
assumptions | array | Array of assumptions


##Get model limitations
```javascript
var request = require('request');
var url='https://myBank.com/v1/models/:MODEL_ID/limitations';
request.get(url, function(error, response, body){
  console.log(body);
});
```
> The above command returns JSON structured like this:

```json
{
  "id": 1,
  "limitations":  [
    {
      "limitation":"data",
      "severity":6
    }
  ],
  "timestamp": "<JAVASCRIPT_TIMESTAMP>"
}
```
This endpoint retrieves the ongoing monitoring results from a given model.

`GET https://myBank.com/v1/models/:MODEL_ID/limitations`

The response returns a JSON object with the following results:  

Parameter | Value | Description
--------- | ------- | -----------
id | integer | Model ID
timestamp | date | Time of last update to the limitations
assumptions | array | Array of limitations
