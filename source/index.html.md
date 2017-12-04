---
title: API Reference

#language_tabs: # must be one of https://git.io/vQNgJ

search: true
---

# Introduction

Welcome to the Knolskape API!<br />
The main goal of this documentaion is to get Knolskape and Accendo on the same page regard this API Consuming.<br />
**so feel free to change whatever seems convenient to you.**<br />
kindly make sure to fill the (to be filled by Knolskape) sections.<br/> and feel free to add (to be filled by Accendo) as well.

# API Flow
In Knolskape, you will interact with Simulations and Users(Candidates).
A Simulations is a set of sevecies. The relationship is, Simulations has many Users.

The API usage flow will kicks in as per below:

1) Using the <a href="#get-all-simulation"> Get All Simulation Api</a> Accendo pulls all Services.<br />
2) Setup project on accendo site by selecting services and adding users to the project.<br />
3) Using the <a href="#register-users"> Register Users Api</a> Accendo registers users by projectId , users list and services list.<br />
4) User logs in to accendo site, Accendo redirects the user to Knolskape site - the simulation page- using the links from <a href="#register-users"> Register Users Api</a> response.<br />
5) User completes the simulation.<br />
6) Knolskape sends a server-side update (Simulation Completion) to Accendo site.<br /> 
    using the Callback URL provided by accendo during user registration.<br />
7) Knolskape redirects (Simulation Completion) the user back to Accendo site.<br /> 
    using the Redirect URL provided by accendo during user registration.<br />
8) Accendo can pulls the status and scores using <br /> 
    <a href="#get-simulation-status-and-scores-user-level"> Get Simulation Status and Scores - Candidate level</a> or <br /> <a href="#get-simulation-status-and-scores-project-level"> Get Simulation Status and Scores - Project level </a> .<br />
# Authentication

**(to be filled by knolskape)**

> Example Header:

```json
  {
      "appId": " ",
      "appSecret": " "
  }
```

### Header Parameters

-------     | Parameter   | Description
---------   | ---------   | -----------
mandatory    | appId      | **(to be filled by knolskape)**
mandatory    | appSecret  | **(to be filled by knolskape)**

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
platformId | **(to be filled by knolskape)**


# Users

## Register Users

> Example Request:

```json
{
  "projectId": 125,
  "users": [
    {
      "userId":1,
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
        "userId": "1",
        "link": "https://accounts.knolskape.com/ct-simulation?custom_token={custom_token1}",
        "token": "{custom_token1}"
      }
    ]
  },
  {
    "service": "cq-v2",
    "users": [
      {
        "userId": "1",
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
platformId | **(to be filled by knolskape)**

### Request Parameters

-------     | Parameter   | Description
---------   | ---------   | -----------
mandatory    | projectId   | project id from accendo side.
mandatory    | users       | list of users to be registered. (refer to the table below)
mandatory    | services    | list of services names to register the users to, You can get the service names of simulation in Get All Services api.

-------     | Users       | Description
---------   | ---------   | -----------
mandatory   | userId*     | user id from accendo side.
mandatory   | email       | user email.
optional    | firstName   | user first name.
optional    | lastName    | user last name.
mandatory   | redirectUrl | redirect url for redirecting back to accendo after completion or the browser session ends.
mandatory   | callbackUrl | callback url for knolskape to call after user **complete** a simulation. (refer to the Callback Parameters table below)

<aside class="notice">
using <b>userId</b> instead of <b>tokenId</b> to identify the user. 
since we are using the tokenId to give access to simulations so it's better not to expose this tokenId,
and use another identifier which can't be used to access any simulations (like the userId).
</aside>



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
platformId | **(to be filled by knolskape)**

### URI Parameters

-------     | Parameter   | Description
---------   | ---------   | -----------
mandatory    | serviceName | service name, You can get the service names of simulation in Get All Services api.
mandatory    | projectIid  | project id from accendo side.
mandatory    | userId      | user id from accendo side.

### Available Status list

Parameter   | Description
---------   | -----------
STARTED     | user started the simulation but not completed yet.
NOT_STARTED | user rigsterd to simulation but not started yet.
COMPLETED   | user completed the simulation.

### Response  Parameters

Parameter       | Description
---------       | -----------
metricsData     | **(to be filled by knolskape)**
status          | **(to be filled by knolskape)**
avgAdoption     | **(to be filled by knolskape)**
progress        | **(to be filled by knolskape)**
noOfConversion  | **(to be filled by knolskape)**
aggregateScore  | **(to be filled by knolskape)**
percentile      | **(to be filled by knolskape)**
competency      | **(to be filled by knolskape)**
rank            | **(to be filled by knolskape)**
startedAt       | **(to be filled by knolskape)**
completedAt     | **(to be filled by knolskape)**

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
platformId | **(to be filled by knolskape)**

### URI Parameters

-------     | Parameter   | Description
---------   | ---------   | -----------
mandatory    | serviceName | service name, You can get the service names of simulation in Get All Services api.
mandatory    | projectIid  | project id from accendo side.


# Simulations Completion

## Redirect Url
**(to be filled by Accendo)**
## Callback Url
callback is a post call to the callback url provided by accendo during user registration using the <a href="#register-users"> Register Users Api</a>.

> Example Request:

```json
{
    "serviceName":"iLead",
    "Scores":{
        "aggregateScore": "20.94",
    }
}
```

> Example Response:

```json
{
    "status":"success"
}
```
### Callback Parameters
-------     | Parameter       | Description
---------   | ---------       | ---------
mandatory    | serviceName     | service name that user completed.
mandatory    | Scores          | **(to be filled by knolskape)**