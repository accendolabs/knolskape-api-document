---
title: API Reference

#language_tabs: # must be one of https://git.io/vQNgJ

search: true
---

# Introduction

Welcome to the Knolskape API! You can use our API to access simulations API endpoints.


# Simulation

## Get All Simulation

> Example Response:

```json
[
  {
      "serviceName": "bybhtml",
      "simulationName": "BYB V2"
  },
  {
      "serviceName": "cq-v2",
      "simulationName": "ChangeQuest v2"
  },
  {
      "serviceName": "ileadhtml",
      "simulationName": "iLead V2"
  }
]
```

This gives list of simulations enabled for accendo platform.

### HTTP Request

`GET https://api-test.knolskape.com/ct/simulations`

### Query Parameters

Parameter  | Description
---------  | -----------
platformId | -----------


# Users

## Register Users

> Example Request:

```json
{
  "projectId": 125,
  "users": [
    {
      "id":1,
      "email": "accendouser1@mailinator.com",
      "firstName": "User1",
      "lastName": "Accendo",
      "redirectUrl":"http://kaliber.com/redirect",
      "callbackUrl":"http://kaliber.com/callback?token=f8XvlPhHpBcYVg7Pv9jBXVDDejNLyO12EgqWTomwh3th4tpYFyiORforoeqz"
    }
  ],
  "services": [
    "ilead",
    "cq-v2"
  ]
}
```

> Example Response:

```json
[
  {
    "service": "ilead",
    "users": [
      {
        "email": "accendouser1@mailinator.com",
        "link": "https://accounts.knolskape.com/ct-simulation?custom_token={custom_token1}",
        "token": "{custom_token1}"
      }
    ]
  },
  {
    "service": "cq-v2",
    "users": [
      {
        "email": "accendouser1@mailinator.com",
        "link": "https://accounts.knolskape.com/ct-simulation?custom_token={custom_token2}",
        "token": "{custom_token2}"
      }
    ]
  }
]
```

This returns the list of links with custom token in it. These links can be clicked by the users directly to access the simulations.So if a project has M tools(services) and N users given in payload, it will return M*N links in response json. The services inside payload is array of service names choosen for this given project.

### HTTP Request

`POST https://api-test.knolskape.com/ct/simulations/register`

### Query Parameters

Parameter  | Description
---------  | -----------
platformId | -----------

### Request Parameters

Parameter   | Description
---------   | -----------
projectId   | project id from accendo side.
users       | list of users to be registered. (refer to the table below)
services    | list of services names to register the users to, You can get the service names of simulation in Get All Services api.

Users       | Description
---------   | -----------
userId      | user id from accendo side.
email       | user email.
firstName   | user first name.
lastName    | user last name.
redirectUrl | redirect url for redirecting back to accendo after completion or the browser session ends.
callbackUrl | callback url for knolskape to call after user **complete** a simulation. (refer to the Callback Parameters table below)

### Callback Parameters
callback is a post call to the callback url.

Parameter       | Description
---------       | -----------
serviceName     | service name that user completed.
Scores          | list of scores.

## Get Simulation Status and Scores - User level

> Example Response:

```json
{
    "metrics": [
        {
            "key": "status",
            "name": "Status"
        },
        {
            "key": "rank",
            "name": "Rank"
        },
        {
            "key": "noOfConversion",
            "name": "No. of Conversions"
        },
        {
            "key": "aggregateScore",
            "name": "Weighted Average Adoption"
        },
        {
            "key": "percentile",
            "name": "Average Adoption (percentile)"
        },
        {
            "key": "avgAdoption",
            "name": "Average Adoption"
        },
        {
            "key": "progress",
            "name": "Progress (in days)"
        },
        {
            "key": "timeLeft",
            "name": "Time Remaining (90 mins)"
        }
    ],
    "metricsData": [
        {
            "tokenId": "BnUny897Ywna3ezJzef8RuhxaNbUboxD",
            "status": "COMPLETED",
            "timeLeft": "88:21",
            "avgAdoption": "24.33",
            "progress": "14",
            "noOfConversion": "1",
            "aggregateScore": "20.94",
            "percentile": "7.51",
            "competency": "7.51",
            "rank": 1,
            "startedAt": 1505128287,
            "completedAt": 1605128302
        }
    ]
}
```

### HTTP Request

`GET https://api-test.knolskape.com/ct/simulation/{{serviceName}}/metrics/project/{{projectIid}}/user/{{userId}}?platformId=2`

This endpoint retrieves Simulation Status and Scores for a specific user.

### Query Parameters

Parameter  | Description
---------  | -----------
platformId | -----------

### URI Parameters

Parameter   | Description
---------   | -----------
serviceName | service name, You can get the service names of simulation in Get All Services api.
projectIid  | project id from accendo side.
userId      | user id from accendo side.
## Get Simulation Status and Scores - Project level

> Example Response:

```json
{
    "metrics": [
        {
            "key": "status",
            "name": "Status"
        },
        {
            "key": "rank",
            "name": "Rank"
        },
        {
            "key": "noOfConversion",
            "name": "No. of Conversions"
        },
        {
            "key": "aggregateScore",
            "name": "Weighted Average Adoption"
        },
        {
            "key": "percentile",
            "name": "Average Adoption (percentile)"
        },
        {
            "key": "avgAdoption",
            "name": "Average Adoption"
        },
        {
            "key": "progress",
            "name": "Progress (in days)"
        },
        {
            "key": "timeLeft",
            "name": "Time Remaining (90 mins)"
        }
    ],
    "metricsData": [
        {
            "tokenId": "oBk4PdXHcbGXWcKuNCMnFWCUTTpPnxoA",
            "status": "STARTED",
            "startedAt": null,
            "completedAt": null
        },
        {
            "tokenId": "ZywFBkgDmQtaS8uxB9KaWfUGZcodzCEq",
            "status": "NOT_STARTED",
            "startedAt": null,
            "completedAt": null
        },
        {
            "tokenId": "BnUny897Ywna3ezJzef8RuhxaNbUboxD",
            "status": "STARTED",
            "timeLeft": "88:21",
            "avgAdoption": "24.33",
            "progress": "14",
            "noOfConversion": "1",
            "aggregateScore": "20.94",
            "percentile": "7.51",
            "competency": "7.51",
            "rank": 1,
            "startedAt": 1505128287,
            "completedAt": null
        },
    ]
}
```

### HTTP Request

`GET https://api-test.knolskape.com/ct/simulation/{{serviceName}}/metrics/project/{{projectIid}}?platformId=2`

This endpoint retrieves Simulation Status and Scores for all Users in a specific Project. 

### Query Parameters

Parameter  | Description
---------  | -----------
platformId | -----------

### URI Parameters

Parameter   | Description
---------   | -----------
serviceName | service name, You can get the service names of simulation in Get All Services api.
projectIid  | project id from accendo side.

