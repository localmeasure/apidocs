---
title: Local Measure API

language_tabs: # must be one of https://git.io/vQNgJ

toc_footers:
  - <a href='https://dashboard.getlocalmeasure.com/account/?tab=plugins'>Sign Up for a Key</a>

includes:
  - errors

search: true
---

# Overview

## API

Create rich profiles of your customers by sending customer data from your external data sources. This API is used to identify customers and create activities within those customers profiles in Local Measure.

There is currently three endpoints in the Local Measure API spec.

## Use Cases

### Wi-Fi splash pages & captive portals

If you are using social media or email sign-in on a splash page or website you can use the API to send identities and sign-in events which creates or updates customer profiles within Local Measure.

### Wi-Fi location analytics

Use the API to create customer profiles and update the customers visit history automatically using the Wi-Fi device location analytics. Visit history powers more relevant search plus automated rules and alerts for repeat visitors.

### Arrival reporting from hotel PMSs

Send arrivals reports into Local Measure by sending guest identities and arrival & departure dates. Include custom fields such as rate codes, special services, package codes etc to provide more detailed contect for the operational teams using Local Measure.

### Loyalty & custom profile data

When identifying a customer in the Local Measure API you are able to send custom fields which give a profile more context and power more sophisticated automations and alerting rules. Creating custom fields such as loyalty level and loyalty ID enable your teams to deliver the right level of service to your guests.

### Link your CRM to Local Measure Customer Profiles

Coming Soon! We will enable you to create a link between customer/guest records in your own CRM or PMS with the Local Measure. Using a unique customer identifier or custom fields you can search and link records between systems.

<aside class="notice">
Use of the API is for customers using the Customer Profiles Add-On. Speak to your Account Manager for more information.
</aside>

# Authentication

The Local Measure API uses an API key to authenticate access to the API and map data to the correct account. It is available at `https://public-api.getlocalmeasure.com/`.

Local Measure expects an API key in all API requests. We support two methods of sending the API key: HTTP Headers or via a url parameter.

### Header
Include an `Authorisation` header in your HTTP request with your API key

`Authorization: <your-api-key>`

### Parameter
Include the `access_token` parameter in your HTTP uri with your API key as the value

`https://public-api.getlocalmeasure.com/v1/<endpoint>?access_token=<your-api-key>`

You can register a new Local Measure API key in our [plugin portal](http://dashboard.getlocalmeasure.com/account/?tab=plugins).

# Identify

Identify lets you tie a user to their actions and record traits about them. It includes a unique identifier and any optional traits you know about them like their email, name, etc.

### HTTP Request

`POST /v1/identify`

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
vendor_id | string | true | A unique identifier, example Mac Address or other device id
first_name | string | true | First name of the user
last_name | string | true | Last name of the user
network | string | false | The network where this data is coming from (facebook, instagram, twitter)
network_id | string | false | The original networks id. example facebook id (71485571121)
avatar_image | string | false | An image representing the user
birthdate | string | false | The birthdate of the user in the format YYYY-MM-DD
gender | string | false | The gender of the user (male, female)
bio | string | false | A brief description of the user
verified | boolean | false | If the user has been verified by the source identity provider
hometown | string | false | The hometown of the user
location | string | false | The location the user resides
link | string | false | A link to the users profile from the source identity provider
followers_count | number | false | The current amount of followers
following_count | number | false | The current amount of users that they are following
email | string | false | The email of the user
website | string | false | The website of the user
marketing_consent | string | false | Whether the user gives consent to receive marketing material
custom_fields | array | false | An array of custom fields with key and value.
custom_fields.key | string | false | The key for the custom field example "hotel_code"
custom_fields.value | string | false | The value for the field example "abc123"

<aside class="success">
Remember — you need to have your request authenticated. If you don't you will receive a 403 request error.
</aside>

> Example payload:

```json
  {
    "vendor_id": "00:14:22:01:23:45",
    "first_name": "John",
    "last_name": "Smith",
    "network": "facebook",
    "network_id": "71485571121",
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
    "marketing_consent": true,
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


# Geolocation


> Example payload:

```json
  {
    "id": "00:14:22:01:23:45",
    "longitude":151.20919,
    "latitude":-33.88668,
    "seen_at":"2017-11-29T08:09:57Z",
  }
```

> Or Bulk Example payload:

```json
  [{
    "id": "00:14:22:01:23:45",
    "longitude":151.20919,
    "latitude":-33.88668,
    "seen_at":"2017-11-29T08:09:57Z",
  },{
    "id": "00:13:00:AE:44:12",
    "longitude":151.20980,
    "latitude":-33.8899,
    "seen_at":"2017-11-29T08:09:57Z",
  },{
    "id": "00:14:BC:00:22:11",
    "longitude":151.20965,
    "latitude":-33.88211,
    "seen_at":"2017-11-29T08:09:57Z",
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

If we don't receive any location events for a user for over a 30 minute period we will mark the user as exited the location

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
vendor_id | string | true | A unique identifier, example Mac Address or other device id
longitude | float | true | Longitude of the identified user
latitude | float | true | Latitude of the identified user
seen_at | string | true | A datetime when the id was last seen. In the format of a RFC 3339 datetime ( 2017-11-29T08:09:57Z )
<aside class="notice">
Remember - To use this endpoint you will need to be authenticated and using an identity that has been already added.
</aside>
