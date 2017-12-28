---
title: API Reference

#language_tabs: # must be one of https://git.io/vQNgJ

search: true
---

# Introduction

Welcome to the Knolskape API!<br/>
The main goal of this documentation is to get Knolskape and Accendo on the same page regarding this API Consuming.<br/>


# API Flow
In Knolskape, you will interact with Simulations and Users (Candidates). Simulations are a set of services. The relationship between Simulations and Users is that **a simulation will have many users.** 

Kindly refer below for the flow of API usage:

1) Using <a href="#get-all-simulation"> Get All Simulation Api</a>, Accendo pulls all Services.<br />
2) Set up a project on Accendo site by selecting available Services and adding Users to the project.<br />
3) Using <a href="#register-users"> Register Users API</a>, Accendo registers Users by projectId, users list and services list.<br />
4) Users log in to Accendo site, Accendo redirects Users to Knolskape site (the simulation page) using the links from <a href="#register-users"> Register Users API</a> response.<br />
5) Users complete the Simulations.<br />
6) Knolskape sends a server-side update (Simulation Completion) to Accendo site.<br /> 
    using the Callback URL provided by Accendo during user registration.<br />
7) Knolskape redirects (Simulation Completion) Users back to Accendo site <br /> 
    using the Redirect URL provided by Accendo during user registration.<br />
8) Accendo can pull the status and scores using <br /> 
    <a href="#get-simulation-status-and-scores-user-level"> Get Simulation Status and Scores - Candidate level</a> or <br /> <a href="#get-simulation-status-and-scores-project-level"> Get Simulation Status and Scores - Project level </a> .<br />

# Environments

KNOLSKAPE supports two environments - production and staging. Token and URL will be dependent on environments. Below are the details

### Production:

variable | value
---- | ----
apiBaseUrl | https://api.knolskape.com
platformId | 2
apptoken | to be provided by knolskape.

### Staging:

This is for testing purpose.

variable | value
---- | ----
apiBaseUrl | https://api-test.knolskape.com
platformId | 2
apptoken | to be provided by knolskape.




# Authentication

All APIs must have **apptoken** in the header for Authentication.

> Example Header:

```json
  {
      "apptoken":"{token}"
  }
```

> Token for production and staging environment will be given by KNOLSKAPE separately.

### Header Parameters

---------   | Parameter   | Description
---------   | ---------   | -----------
mandatory   | apptoken    | Token for authentication

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

`GET {apiBaseUrl}/ct/simulations`

### Query Parameters

Parameter  | Description
---------  | -----------
platformId | {platformId}


# Users

## Register Users

This returns the list of links with the custom token in it. These links can be clicked by Users directly to access the Simulations. If a project has M tools (Services) and N Users given in payload, it will return M*N links in the response JSON. The services inside payload is an array of service names chosen for this given project.

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
        "link": "https://accounts-test.knolskape.com/ct-simulation?custom_token={custom_token1}",
        "token": "{custom_token1}"
      }
    ]
  },
  {
    "service": "cq-v2",
    "users": [
      {
        "userId": "1",
        "link": "https://accounts-test.knolskape.com/ct-simulation?custom_token={custom_token2}",
        "token": "{custom_token2}"
      }
    ]
  }
]
```

### HTTP Request

`POST {apiBaseUrl}/ct/simulations/register`

### Query Parameters

Parameter  | Description
---------  | -----------
platformId | {platformId}

### Request Parameters

-------     | Parameter   | Description
---------   | ---------   | -----------
mandatory    | projectId   | project id from Accendo side.
mandatory    | users       | list of users to be registered. (refer to the table below)
mandatory    | services    | list of services names to register the users to, service names of simulation can be acquired in Get All Services API.


<aside class="notice">
Use <b>userId</b> (along with projectId and serviceName) instead of <b>token</b> to identify the user in given project and service. Since we are using the token to give access to Simulations, it's better not to expose this token and use another identifier which can't be used to access any Simulations (like the userId). userId will be used to check simulation status and get scores using 
<a href="#get-simulation-status-and-scores-user-level"> Get Simulation Status and Scores - Candidate level</a>.
</aside>

-------     | Users       | Description
---------   | ---------   | -----------
mandatory   | email       | user email.
optional    | firstName   | user first name.
optional    | lastName    | user last name.
mandatory   | redirectUrl | redirect URL (can be unique to project) for redirecting back to Accendo after completion or when the browser session ends. This is common to all users in the project. During redirection, KNOLSKAPE will append userId and serviceName to it.
mandatory   | callbackUrl | callback URL for KNOLSKAPE to call after user **completes** a simulation. (refer the Callback Parameters table below). This is called when the user has finished playing the Simulation. This URL will be unique to project and user.

### Response Fields

-------     | Users       | Description
---------   | ---------   | -----------
mandatory   | service     | service name
mandatory   | userId      | This is unique to user therefore if the same user is uploaded in more than one project then userId will be same.
mandatory   | link        | Url for the Simulation.
mandatory   |token        | This is used to give access to the Simulation. This is the unique combination of projectId, serviceName, and userId. If there are M services, N users for a project then the total number of unique tokens will be M * N for that project.


## Get Simulation Status and Scores 

There are two endpoints to retrieve the Scores (metrics) and Status for users. One is to retrieve only for one user or other is to retrieve for particular project and service. Response to both the APIs is same. Apart from some common fields in **metricsData** all fields are dependent on Service hence can be different. 

#### Common fields 


-- | Fields | Description 
--------| ------ | -----------
mandatory  | status | This shows the status of a particular user in particular project and service. Supported values are given in below table.
mandatory  | started | Epoch timestamp which shows at what time the user has started playing the simulation.
mandatory  | completed | Epoch timestamp which shows at what time the user has completed the simulation.
mandatory  | userId | userId of a user to uniquely identify the user.
mandatory  | token  | token unique to user, project and service.

#### Available Status list

Parameter   | Description
---------   | -----------
STARTED     | user has started the simulation but has yet to complete.
NOT_STARTED | user has been registered to the simulation but has yet to start the simulation.
COMPLETED   | user has completed the simulation.

#### Simulation specific fields.

Please refer the excel <a href="http://knol.ly/accendo-simulation-metrics">here</a>.

### User level

This endpoint retrieves Simulation Status and Scores for a specific user. 

> Example Response:

```json
{
    "metrics": [
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
            "startedAt": 1505128287,
            "completedAt": 1605128302,
            "userId":1,
            "timeLeft": "88:21",
            "avgAdoption": "24.33",
            "progress": "14",
            "noOfConversion": "1",
            "aggregateScore": "20.94",
            "percentile": "7.51",
            "competency": "7.51",
            "rank": 1,
            
        }
    ]
}
```

### HTTP Request

`GET {apiBaseUrl}/ct/simulation/{serviceName}/metrics/project/{projectIid}/user/{userId}?platformId=2`

### Query Parameters

Parameter  | Description
---------  | -----------
platformId | {platformId}

### URI Parameters

-------     | Parameter   | Description
---------   | ---------   | -----------
mandatory    | serviceName | service name, which can be acquired in Get All Services API.
mandatory    | projectIId  | project id from Accendo side.
mandatory    | userId      | user id unique identifier generated from KNOLSKAPE side.



### Response  Fields

According to the description above.


## Get Simulation Status and Scores - Project level

This endpoint retrieves Simulation Status and Scores for all users in a specific project and service. 

> Example Response:

```json
{
    "metrics": [
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
            "tokenId": "OnUny897Ywna3ezJzef8RuhxaNbUboxD",
            "status": "COMPLETED",
            "started": "",
            "completed": "",
            "userId":1,
            "timeLeft": "88:21",
            "avgAdoption": "24.33",
            "progress": "14",
            "noOfConversion": "1",
            "aggregateScore": "20.94",
            "percentile": "7.51",
            "competency": "7.51",
            "rank": 1,

        },
        {
            "tokenId": "AnUny897Ywna3ezJzef8RuhxaNbUboxD",
            "status": "NOT_STARTED",
            "started": null,
            "completed": null,
            "userId":1,
            "timeLeft": "88:21",
            "avgAdoption": "24.33",
            "progress": "14",
            "noOfConversion": "1",
            "aggregateScore": "20.94",
            "percentile": "7.51",
            "competency": "7.51",
            "rank": 1,
        },
        {
            "tokenId": "BnUny897Ywna3ezJzef8RuhxaNbUboxD",
            "status": "STARTED",
            "started": 1505128287,
            "completed": 1605128302,
            "userId":1,
            "timeLeft": "88:21",
            "avgAdoption": "24.33",
            "progress": "14",
            "noOfConversion": "1",
            "aggregateScore": "20.94",
            "percentile": "7.51",
            "competency": "7.51",
            "rank": 1,
        },
    ]
}
```

### HTTP Request

`GET {apiBaseUrl}/ct/simulation/{serviceName}/metrics/project/{projectIid}?platformId=2`

### Query Parameters

Parameter  | Description
---------  | -----------
platformId | {platformId}

### URI Parameters

-------     | Parameter   | Description
---------   | ---------   | -----------
mandatory    | serviceName | service name, which can be acquired in Get All Services API.
mandatory    | projectIId  | project id from Accendo side.


### Response Fields

According to the description above.

# Simulations Completion

## Redirect Url

Redirect is a get call to the Redirect URL provided by Accendo during user registration using the <a href="#register-users"> Register Users API</a>.

### HTTP Request

`GET {REDIRECT_URL}?userId={userId}&serviceName={serviceName}&projectId={projectId}`

### Query Parameters
-------     | Parameter       | Description
---------   | ---------       | ---------
mandatory   | serviceName     | name of service that user has completed.
mandatory   | userId          | user id unique identifier generated from KNOLSKAPE side.
mandatory   | projectId       | Accendo project Id. 

## Callback Url

Callback is a post call to the Callback URL provided by Accendo during user registration using the <a href="#register-users"> Register Users API</a>. Note: This is unique to project and user.

### HTTP Request

`POST {CALLBACK_URL}`

> Example Request:

```json
{
    "serviceName":"iLead",
    "Scores":{
        "timeLeft": "88:21",
        "avgAdoption": "24.33",
        "progress": "14",
        "noOfConversion": "1",
        "aggregateScore": "20.94",
        "percentile": "7.51",
        "competency": "7.51",
        "rank": 1,
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
mandatory    | scores          | dependent on simulation. Please refer <a href="#simulation-specific-fields"> excel mentioned above </a>.
