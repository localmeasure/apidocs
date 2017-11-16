---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ

toc_footers:
  - <a href='https://dashboard.getlocalmeasure.com/account/?tab=plugins'>Sign Up for a Key</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Localmeasure API! You can use our API to access Localmeasure API endpoints.

# Authentication

> Make sure to replace `abc123` with your API key.

Localmeasure uses API keys to allow access to the API. You can register a new Localmeasure API key at our [plugin portal](http://dashboard.getlocalmeasure.com/account/?tab=plugins).

The Localmeasure API is available from the url below:

`https://api.getlocalmeasure.com/v1/`

Localmeasure expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: abc123`

or as a parameter on a request that looks like the following:

`https://api.getlocalmeasure.com/v1/request?access_token=abc123`

<aside class="notice">
You must replace <code>abc123</code> with your personal API key.
</aside>

# Identify

> Example payload:

```json
  {
    "identifier": "00:14:22:01:23:45",
    "source": "facebook",
    "source_id": "71485571121",
    "first_name": "John",
    "last_name": "Smith",
    "avatar_image":"https://cdn1.iconfinder.com/data/icons/user-pictures/100/male3-512.png",
    "birthdate":"1970-05-02",
    "gender":"male",
    "bio":"An all round great guy, enjoys long walks on the beach.",
    "verified":true,
    "hometown":"New York, New York",
    "location":"Sydney, Australia",
    "link":"https://instagram.com/johnsmith/",
    "followers_count":500,
    "following_count":200,
    "consent": true,
    "email": "john.smith@gmail.com",
    "website": "https://google.com",
    "custom_fields"[{
        "key":"favourite_food",
        "value":"Pizza"
    },{
        "key":"loyalty_status",
        "value":"Platinum"
    }]
  }
```

> On success of this request it will return a 200 and JSON structured like this:

```json
  {
    "status": "ok",
  }
```

Identify lets you tie a user to their actions and record traits about them. It includes a unique identifier and any optional traits you know about them like their email, name, etc.

### HTTP Request

`POST /v1/identify`

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
identifier | string | true | A unique identifier, example Mac Address or other device id
source | string | true | The network where this data is coming from (facebook, instagram, twitter, email, external)
souce_id | string | true | The original networks id. example facebook id (71485571121)
first_name | string | true | First name of the user
last_name | string | true | Last name of the user
avatar_image | string | false | An image representing the user
birthdate | string | false | The birthdate of the user in the format YYYY-MM-DD
gender | string | false | The gender of the user (male,female)
bio | string | false | A breif description of the user
verified | boolean | false | If the user has been verified by the source identity provider
hometown | string | false | The hometown of the user
location | string | false | The location the user resides
link | string | false | A link to the users profile from the source identity provider
followers_count | number | false | The current amount of followers
following_count | number | false | The current amount of users that they are following
email | string | false | The email of the user
website | string | false | The website of the user
marketing_consent | string | false | Whether the user gives consent to recieve marketing material
custom_fields | array | false | An array of custom fields with key and value.
custom_fields.key | string | false | The key for the custom field example "hotel_code"
custom_fields.value | string | false | The value for the field example "abc123"

<aside class="success">
Remember — you need to have your request authenticated. If you don't you will recieve a 403 request error.
</aside>


# Geolocation


> Example payload:

```json
  {
    "identifier": "00:14:22:01:23:45",
    "longitude":151.20919,
    "latitude":-33.88668,
  }
```

> Or Bulk Example payload:

```json
  [{
    "identifier": "00:14:22:01:23:45",
    "longitude":151.20919,
    "latitude":-33.88668,
  },{
    "identifier": "00:13:00:AE:44:12",
    "longitude":151.20980,
    "latitude":-33.8899,
  },{
    "identifier": "00:14:BC:00:22:11",
    "longitude":151.20965,
    "latitude":-33.88211,
  }]
```

> On success of this request it will return a 200 and JSON structured like this:

```json
  {
    "status": "ok",
  }
```

Geolocation records latitude and longitude position of a identity

### HTTP Request

`POST /v1/geolocation`

### Geolocation Frequency

We recommend sending location events regularly, every minute if possible.

If we don't recieve any location events for a user for over a 30 minute period we will mark the user as exited the location

### Body Parameters

Parameter | Type | Required | Description 
--------- | ------- | ------- | -----------
identifier | string | true | A unique identifier, example Mac Address or other device id
longitude | float | true | Longitude of the identified user
latitude | float | true | Latitude of the identified user

<aside class="notice">
Remember - To use this endpoint you will need to be authenticated and use an identifier that has been added.
</aside>