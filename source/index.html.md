---
title: API Reference

#language_tabs: # must be one of https://git.io/vQNgJ

search: true
---

# Introduction

Welcome to the Knolskape API!<br/>
The main goal of this documentation is to get Knolskape and Accendo on the same page regarding this API Consuming.<br/> **Do feel free to change whatever seems convenient to you.**<br/>
Kindly ensure to fill in the “(to be filled by Knolskape)” sections 
and feel free to add “(to be filled by Accendo)” as well.


# API Flow
In Knolskape, you will interact with Simulations and Users (Candidates). Simulations are a set of services. The relationship between Simulations and Users is that **a simulation will have many users.** 

Kindly refer below for the flow of API usage:

1) Using <a href="#get-all-simulation"> Get All Simulation Api</a>, Accendo pulls all Services.<br />
2) Set up a project on Accendo site by selecting available Services and adding Users to the project.<br />
3) Using <a href="#register-users"> Register Users API</a>, Accendo registers Users by projectId, users list and services list.<br />
4) Users log in to Accendo site, Accendo redirects Users to Knolskape site (the simulation page) using the links from <a href="#register-users"> Register Users API</a> response.<br />
5) Users complete the Simulations.<br />
6) Knolskape sends a server-side update (Simulation Completion) to Accendo site.<br /> 
    using the Callback URL provided by accendo during user registration.<br />
7) Knolskape redirects (Simulation Completion) Users back to Accendo site <br /> 
    using the Redirect URL provided by accendo during user registration.<br />
8) Accendo can pull the status and scores using <br /> 
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

This provides a list of Simulations that have been enabled for Accendo platform. 

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
      "email": "accendouser1@mailinator.com",
      "firstName": "User1",
      "lastName": "Accendo",
      "redirectUrl":"{REDIRECT_URL}",
      "callbackUrl":"{CALLBACK_UR}"
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

This returns the list of links with custom token in it. These links can be clicked by Users directly to access the Simulations. If a project has M tools (Services) and N Users given in payload, it will return M*N links in the response json. The Services inside payload is an array of service names chosen for this given project.

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
mandatory    | services    | list of services names to register the users to, service names of simulation can be acquired in Get All Services API.

<aside class="notice">
Use <b>userId</b> instead of <b>token</b> to identify the user. Since we are using the token to give access to Simulations, it's better not to expose this token and use another identifier which can't be used to access any Simulations (like the userId). userId will be used to check simulation status and get scores using 
<a href="#get-simulation-status-and-scores-user-level"> Get Simulation Status and Scores - Candidate level</a>.
</aside>

-------     | Users       | Description
---------   | ---------   | -----------
mandatory   | email       | user email.
optional    | firstName   | user first name.
optional    | lastName    | user last name.
mandatory   | redirectUrl | redirect url for redirecting back to accendo after completion or when the browser session ends.
mandatory   | callbackUrl | callback url for knolskape to call after user **completes** a simulation. (refer the Callback Parameters table below)

### Response Fields

-------     | Users       | Description
---------   | ---------   | -----------
mandatory   | service     | service name
mandatory   | userId      | user id unique identifier generated from knolskape side.
mandatory   | link        | url for the Simulation .
mandatory   | token       | used to give access to the Simulation.

## Get Simulation Status and Scores - User level

This endpoint retrieves Simulation Status and Scores for a specific user.

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

### Query Parameters

Parameter  | Description
---------  | -----------
platformId | **(to be filled by knolskape)**

### URI Parameters

-------     | Parameter   | Description
---------   | ---------   | -----------
mandatory    | serviceName | service name, which can be acquired in Get All Services API.
mandatory    | projectIid  | project id from accendo side.
mandatory    | userId      | user id unique identifier generated from knolskape side.

### Available Status list

Parameter   | Description
---------   | -----------
STARTED     | user has started the simulation but has yet to complete.
NOT_STARTED | user has been registered to the simulation but has yet to start the simulation.
COMPLETED   | user has completed the simulation.

### Response  Fields

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
**(to be filled by knolskape)** | **(to be filled by knolskape)**
## Get Simulation Status and Scores - Project level

This endpoint retrieves Simulation Status and Scores for all users in a specific project. 

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

### Query Parameters

Parameter  | Description
---------  | -----------
platformId | **(to be filled by knolskape)**

### URI Parameters

-------     | Parameter   | Description
---------   | ---------   | -----------
mandatory    | serviceName | service name, which can be acquired in Get All Services API.
mandatory    | projectIid  | project id from accendo side.


### Response  Fields

Parameter       | Description
---------       | -----------
**(to be filled by knolskape)**     | **(to be filled by knolskape)**

# Simulations Completion

## Redirect Url

Redirect is a get call to the Redirect URL provided by Accendo during user registration using the <a href="#register-users"> Register Users API</a>.

### HTTP Request

`GET {REDIRECT_URL}?userId={userId}&serviceName={serviceName}`

### Query Parameters
-------     | Parameter       | Description
---------   | ---------       | ---------
mandatory   | serviceName     | name of service that user has completed.
mandatory   | userId          | user id unique identifier generated from knolskape side.

## Callback Url

Callback is a post call to the Callback URL provided by Accendo during user registration using the <a href="#register-users"> Register Users API</a>.

### HTTP Request

`POST {CALLBACK_UR}`

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
mandatory    | serviceName     | name of service that user has completed.
mandatory    | Scores          | **(to be filled by knolskape)**