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

Create rich profiles of your customers by sending customer data from your external data sources. This API is used to identify customers and create activities within those customers profiles in Local Measure.


## Use Cases

### Wi-Fi splash pages & captive portals

If you are using social media or email sign-in on a splash page or website you can use the API to send identities which creates or updates customer profiles within Local Measure.

### Wi-Fi location analytics

Use the API to create customer profiles and update the customers visit history automatically using the Wi-Fi device location analytics. Visit history powers more relevant search plus automated rules and alerts for repeat visitors.

### Arrival reporting from hotel PMSs

Send arrivals reports into Local Measure by sending guest identities and arrival & departure dates. Include custom fields such as rate codes, special services, package codes etc to provide more detailed contect for the operational teams using Local Measure.

### Loyalty & custom profile data

When identifying a customer in the Local Measure API you are able to send custom fields which give a profile more context and power more sophisticated automations and alerting rules. Creating custom fields such as loyalty level and loyalty ID enable your teams to deliver the right level of service to your guests.

### Link your CRM to Local Measure Customer Profiles

We enable you to create a link between customer/guest records in your own CRM or PMS with the Local Measure. Using a customer customer identifier or custom fields you can search and link records between systems.

<aside class="notice">
Use of the API is for customers using the Customer Profiles Add-On. Speak to your Account Manager for more information.
</aside>

# Authentication

The Local Measure API uses an API key to authenticate access to the API and map data to the correct account. It is available at `https://public-api.getlocalmeasure.com/`.

Local Measure expects an API key in all API requests. We support two methods of sending the API key: HTTP Headers or via a url parameter.

### Header
Include an `Authorisation` header in your HTTP request with your API key

`Authorization: your-api-key`

### Parameter
Include the `access_token` parameter in your HTTP uri with your API key as the value

`https://public-api.getlocalmeasure.com/v1/endpoint?access_token=your-api-key`

You can register a new Local Measure API key in our [plugin portal](http://dashboard.getlocalmeasure.com/account/?tab=plugins).

# Identify

Identify a customer to Local Measure by calling this endpoint. A customer profile will be created or updated based on the fields provided.

Customer profiles are updated when a matching profile is found based on the following precedence criteria:

- customer_id
- email
- profiles.source_id

### HTTP Request

`POST /v1/identify`

> Example payload:

```json
  {
    "customer_id": "00:14:22:01:23:45",
    "profiles": [{
        "source": "facebook",
        "source_id": "87235872",
        "link": "https://facebook.com/john.smith.478653",
    },{
        "source": "instagram",
        "source_id": "245986569842",
        "link": "https://instagram.com/johnsmith/",
    },{
        "source": "twitter",
        "source_id": "72735729779824",
        "link": "https://twitter.com/johnnysmiddy/",
    },{
        "source": "salesforce",
        "source_id": "37461828371",
        "link": "https://salesforce.com/customer/abc123"
    }],
    "traits": {
        "first_name": "John",
        "last_name": "Smith",
        "email": "john.smith@gmail.com",
        "loyalty_level": "Elite Plus",
        "loyalty_number": "AU8759342",
        "birthdate": "1974-08-01",
        "gender": "male",
        "marketing_consent": true
    }
  }
```

> On success of this request it will return a 200 and JSON with a link back to the profile inside Local Measure like this:

```json
  {
    "link": "https://app.getlocalmeasure.com/customers/1787201767960751/profile",
  }
```

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
customer_id | string | true | An identifier for the customer. If you dont have one you can use their device mac or email address instead.
profiles | array | false | An array of other social/crm profiles to link with
profiles.source | string | true | The name of the social/crm platform where this profile comes from.
profiles.source_id | string | true | The customers ids on the social/crm platform id i.e. Facebook id (71485571121)
profiles.link | string | false | A link to the customers social profile
traits | object | false | An object of traits.


### Traits
Traits are pieces of information you know about a user that are included in an identify call. These could be demographics like age or gender, or loyalty information.

### Reserved Traits
We've reserved some traits that have meanings for users and they are handled in special ways.

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
first_name | string | true | First name of the customer
last_name | string | true | Last name of the customer
email | string | false | The email of the customer
marketing_consent | boolean | false | Whether the customer gives consent to receive marketing material
birthdate | string | false | The birthdate of the customer in the format YYYY-MM-DD
gender | string | false | The gender of the customer
avatar_image | string | false | An image representing the customer
bio | string | false | A breif description of the customer
hometown | string | false | The home town of the customer
link | string | false | A link to the original customer
website | string | false | The customers website

<aside class="notice">
Only use reserved traits for their intended purpose.
</aside>

# Geo-Location

The geo-location endpoint uses the device wi-fi position to automatically create and end visits in a customer profile.

### HTTP Request

> Example payload:

```json
  {
    "customer_id": "00:14:22:01:23:45",
    "longitude": 151.20919,
    "latitude": -33.88668,
    "seen_at": "2017-11-29T08:09:57Z",
  }
```

> Or Bulk Example payload:

```json
  [{
    "customer_id": "00:14:22:01:23:45",
    "longitude": 151.20919,
    "latitude": -33.88668,
    "seen_at": "2017-11-29T08:09:57Z",
  },{
    "customer_id": "00:13:00:AE:44:12",
    "longitude": 151.20980,
    "latitude": -33.8899,
    "seen_at": "2017-11-29T08:09:57Z",
  },{
    "customer_id": "00:14:BC:00:22:11",
    "longitude": 151.20965,
    "latitude": -33.88211,
    "seen_at": "2017-11-29T08:09:57Z",
  }]
```

> On success of this request it will return a 200 and JSON structured like this:

```json
  {
    "status": "ok",
  }
```

`POST /v1/geolocation`

### Frequency

We recommend sending location events regularly, every minute if possible.

When the customer geo-location is seen for the first time or after a previous exit we create a visit.
If we don't receive any location events for a device for a 30 minute period we will mark the customer visit as exited.

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
customer_id | string | true | A customer identifier of customer. If you dont have one you can use the persons device mac or email address instead.
longitude | float | true | Longitude of the identified device
latitude | float | true | Latitude of the identified device
seen_at | string | true | A datetime when the device was last seen. In the format of a RFC 3339 datetime ( 2017-11-29T08:09:57Z )

<aside class="notice">
Remember - To use this endpoint you will need to be authenticated and provide an existing customer identity using the identify endpoint first.
</aside>

# Geo-Venue

The geo-venue endpoint uses the device venue or zone to automatically create and end visits in a customer profile.

### HTTP Request

> Example payload:

```json
  {
    "customer_id": "00:14:22:01:23:45",
    "venue_id": "FJHKL334",
    "name": "Level 1",
    "address": "3 Drewberry Lane",
    "seen_at": "2017-11-29T08:09:57Z",
  }
```

> Or Bulk Example payload:

```json
  [{
    "customer_id": "00:14:22:01:23:45",
    "venue_id": "FJHKL334",
    "name": "Level 1",
    "address": "3 Drewberry Lane",
    "seen_at": "2017-11-29T08:09:57Z",
  },{
    "customer_id": "00:13:00:AE:44:12",
    "venue_id": "FJHKL336",
    "name": "Level 3",
    "address": "3 Drewberry Lane",
    "seen_at": "2017-11-29T08:09:57Z",
  },{
    "customer_id": "00:14:BC:00:22:11",
    "venue_id": "FJHKL338",
    "name": "Level 5",
    "address": "3 Drewberry Lane",
    "seen_at": "2017-11-29T08:09:57Z",
  }]
```

> On success of this request it will return a 200 and JSON structured like this:

```json
  {
    "status": "ok",
  }
```

`POST /v1/geovenue`

### Frequency

We recommend sending venue events regularly, every minute if possible.

When the customer geo-venue is seen for the first time or after a previous exit we create a visit.
If we don't receive any venue events for a device for a 30 minute period we will mark the customer visit as exited.

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
customer_id | string | true | A customer identifier of customer. If you dont have one you can use the persons device mac or email address instead.
venue_id | string | true | A venue identifier.
name | string | true | The name of the venue or zone.
address | string | false | The address of the venue or zone.
seen_at | string | true | A datetime when the device was last seen. In the format of a RFC 3339 datetime ( 2017-11-29T08:09:57Z )

<aside class="notice">
Remember - To use this endpoint you will need to be authenticated and provide an existing customer identity using the identify endpoint first.
</aside>
