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
Include an `Authorization` header in your HTTP request with your API key

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
    "link": "https://app.getlocalmeasure.com/customers/1787201767960751/profile"
  }
```

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
`customer_id` | string | true | An identifier for the customer. If you dont have one you can use their device mac or email address instead.
`profiles` | array | false | An array of other social/crm profiles to link with
`profiles.source` | string | true | The name of the social/crm platform where this profile comes from.
`profiles.source_id` | string | true | The customers ids on the social/crm platform id i.e. Facebook id (71485571121)
`profiles.link` | string | false | A link to the customers social profile
`traits` | object | false | An object of traits.


### Traits
Traits are pieces of information you know about a user that are included in an identify call. These could be demographics like age or gender, or loyalty information.

### Reserved Traits
We've reserved some traits that have meanings for users and they are handled in special ways.

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
`first_name` | string | true | First name of the customer
`last_name` | string | true | Last name of the customer
`email` | string | false | The email of the customer
`marketing_consent` | boolean | false | Whether the customer gives consent to receive marketing material
`birthdate` | string | false | The birthdate of the customer in the format YYYY-MM-DD
`gender` | string | false | The gender of the customer
`avatar_image` | string | false | An image representing the customer
`bio` | string | false | A brief description of the customer
`hometown` | string | false | The home town of the customer
`link` | string | false | A link to the original customer
`website` | string | false | The customers website
`phone` | string | false | The customers phone number
`room` | string | false | The customers room number

<aside class="notice">
Only use reserved traits for their intended purpose.
</aside>

# Geo-Location

The geo-location endpoint uses the device wi-fi position to automatically create and end visits in a customer profile.

### HTTP Request

`POST /v1/geolocation`

> Example payload:

```json
  {
    "customer_id": "00:14:22:01:23:45",
    "longitude": 151.20919,
    "latitude": -33.88668,
    "seen_at": "2017-11-29T08:09:57Z"
  }
```

> Or Bulk Example payload:

```json
  [{
    "customer_id": "00:14:22:01:23:45",
    "longitude": 151.20919,
    "latitude": -33.88668,
    "seen_at": "2017-11-29T08:09:57Z"
  },{
    "customer_id": "00:13:00:AE:44:12",
    "longitude": 151.20980,
    "latitude": -33.8899,
    "seen_at": "2017-11-29T08:09:57Z"
  },{
    "customer_id": "00:14:BC:00:22:11",
    "longitude": 151.20965,
    "latitude": -33.88211,
    "seen_at": "2017-11-29T08:09:57Z"
  }]
```

> On success of this request it will return a 200 and JSON structured like this:

```json
  {
    "status": "ok",
    "code":200
  }
```

### Frequency

We recommend sending location events regularly, every minute if possible.

When the customer geo-location is seen for the first time or after a previous exit we create a visit.
If we don't receive any location events for a device for a 30 minute period we will mark the customer visit as exited.

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
`customer_id` | string | true | A customer identifier of customer. If you dont have one you can use the persons device mac or email address instead.
`longitude` | float | true | Longitude of the identified device
`latitude` | float | true | Latitude of the identified device
`seen_at` | string | true | A datetime when the device was last seen. In the format of a RFC 3339 datetime ( 2017-11-29T08:09:57Z )

<aside class="notice">
Remember - To use this endpoint you will need to be authenticated and provide an existing customer identity using the identify endpoint first.
</aside>

# Geo-Venue

The geo-venue endpoint uses the device venue or zone to automatically create and end visits in a customer profile.

### HTTP Request

`POST /v1/geovenue`

> Example payload:

```json
  {
    "customer_id": "00:14:22:01:23:45",
    "venue_id": "FJHKL334",
    "name": "Level 1",
    "address": "3 Drewberry Lane",
    "seen_at": "2017-11-29T08:09:57Z"
  }
```

> Or Bulk Example payload:

```json
  [{
    "customer_id": "00:14:22:01:23:45",
    "venue_id": "FJHKL334",
    "name": "Level 1",
    "address": "3 Drewberry Lane",
    "seen_at": "2017-11-29T08:09:57Z"
  },{
    "customer_id": "00:13:00:AE:44:12",
    "venue_id": "FJHKL336",
    "name": "Level 3",
    "address": "3 Drewberry Lane",
    "seen_at": "2017-11-29T08:09:57Z"
  },{
    "customer_id": "00:14:BC:00:22:11",
    "venue_id": "FJHKL338",
    "name": "Level 5",
    "address": "3 Drewberry Lane",
    "seen_at": "2017-11-29T08:09:57Z"
  }]
```

> On success of this request it will return a 200 and JSON structured like this:

```json
  {
    "status": "ok",
    "code":200
  }
```

### Frequency

We recommend sending venue events regularly, every minute if possible.

When the customer geo-venue is seen for the first time or after a previous exit we create a visit.
If we don't receive any venue events for a device for a 30 minute period we will mark the customer visit as exited.

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
`customer_id` | string | true | A customer identifier of customer. If you dont have one you can use the persons device mac or email address instead.
`venue_id` | string | true | A venue identifier.
`name` | string | true | The name of the venue or zone.
`address` | string | false | The address of the venue or zone.
`seen_at` | string | true | A datetime when the device was last seen. In the format of a RFC 3339 datetime ( 2017-11-29T08:09:57Z )

<aside class="notice">
Remember - To use this endpoint you will need to be authenticated and provide an existing customer identity using the identify endpoint first.
</aside>


# Export

The `export` endpoint provides two discrete endpoints:

* `export/identities` will return a summary of Customer Profiles submitted through the `/v1/identify` endpoint or created through our [Pulse](https://www.localmeasurepulse.com) product
* `export/pulse` will return [Pulse](https://www.localmeasurepulse.com) responses.

Both support SON and Comma-Separated Values (CSV) as output formats, defaulting to JSON. You can specify the format in two ways:

1. Appending the format to the endpoint e.g., `GET /v2/identities.csv`
2. Using the `Accept` header, set to either `application/json` or `text/csv`

## Identities

### HTTP Request

`GET /v2/export/identities`

```shell
curl -X GET \
  'https://public-api.getlocalmeasure.com/v2/export/identities?type=wifi' \
  -H 'Authorization: YOUR_API_KEY'
```

> Example Response

```json
[
  {
    "email": "joe@example.com",
    "first_name": "Joe",
    "last_name": "Smith",
    "age_range": "21+",
    "device_category": "Mobile",
    "device_family": "iPhone",
    "location_visited": "Joe's Coffee Shop",
    "first_visited_at": "2018-10-03T03:51:47Z",
    "last_visited_at": "2018-10-03T03:51:47Z",
    "number_of_devices": 2,
    "number_of_connections": 1,
    "wifi_use_duration": 1800,
    "marketing_consent": true,
    "marketing_consented_at": "2018-10-03"
  }
]
```

### Parameters

Parameter | Description
----------|------------
`from` | (Inclusive) date e.g., `2018-10-02` to restrict identities to those seen on or after this date. Defaults to the first of the current month.
`to` | (Exclusive) date e.g., `2018-10-04` to restrict identities to those seen before this date. Defaults to tomorrow.
`type` | Limits identities to those that have the required `type`. Can be `wifi`, `pulse`, `note` or `all`. Can be specified multiple times. `all` will cause `from`, `to`, and all other `type`s to be ignored. Defaults to `wifi` & `pulse`.

## Pulse

### HTTP Request

`GET /v2/export/pulse`

```shell
curl -X GET \
  'https://public-api.getlocalmeasure.com/v2/export/pulse?type=wifi' \
  -H 'Authorization: YOUR_API_KEY'
```

> Example Response

```json
[
    {
        "place_name": "The Overlook Hotel",
        "timestamp": "2019-01-15T16:00:42.398Z",
        "full_name": "Jack Torrence",
        "email_address": "jack@overlook.hotel",
        "room_number": "237",
        "rating": 3,
        "comment": "Less work and more play."
    }
]
```

### Parameters

Parameter | Description
----------|------------
`from` | (Inclusive) date e.g., `2018-10-02` to restrict identities to those seen on or after this date. Defaults to the first of the current month.
`to` | (Exclusive) date e.g., `2018-10-04` to restrict identities to those seen before this date. Defaults to tomorrow.
`rating` | Limits responses to those that match the required `rating` e.g., `1`, `2`, `3`, `4` or `5`. Can be specified multiple times.

# Profiles

The `profiles` endpoint can be used in three ways:

- to retrieve many Customer Profiles
- to retrieve an individual Customer Profile
- to retrieve events for an individual Customer Profile

## Many

Retrieve many Customers Profiles that have been submitted through the `/v1/identify` endpoint or created through our [Pulse](https://www.localmeasurepulse.com) product.

### HTTP Request

`GET /v2/profiles`

```shell
curl -X GET \
  'https://public-api.getlocalmeasure.com/v2/profiles' \
  -H 'Authorization: YOUR_API_KEY'
```

> Example Response

```json
[
  {
    "customer_ids": [
        "00:14:22:01:23:45"
    ],
    "devices": [],
    "profiles": [
        {
            "source": "salesforce",
            "source_id": "37461828371",
            "link": "https://salesforce.com/customer/abc123"
        }
    ],
    "first_name": "John",
    "last_name": "Smith",
    "emails": [
        "john.smith@gmail.com"
    ],
    "marketing_consent": true,
    "marketing_consented_at": "2019-03-05T21:19:19.696Z",
    "avatar_image": "https://cdn1.iconfinder.com/data/icons/user-pictures/100/male3-512.png",
    "bio": "An all round great guy, enjoys long walks on the beach.",
    "hometown": "New York, New York",
    "location": "Sydney, Australia",
    "link": "https://salesforce.com/customer/abc123",
    "website": "https://google.com",
    "custom_traits": {
        "favourite_food": "Pizza",
        "loyalty_level": "Elite Plus",
        "loyalty_number": "AU8759342",
        "loyalty_status": "Platinum",
        "scorpio": "Beorn is scorpio"
    },
    "updated_at": "2019-03-05T21:19:19.696Z"
  }
]
```

### Parameters

Parameter | Description
----------|------------
`skip` | Defaults to `0`; used for paginating results
`limit` | Defaults to `10`; used for paginating results

## One

Retrieve an individual Customer Profile that has been submitted through the `/v1/identify` endpoint or created through our [Pulse](https://www.localmeasurepulse.com) product.

### HTTP Request

`GET /v2/profiles/<customer_id>`

```shell
curl -X GET \
  'https://public-api.getlocalmeasure.com/v2/profiles/00:14:22:01:23:45' \
  -H 'Authorization: YOUR_API_KEY'
```

> Example Response

```json
{
  "customer_ids": [
      "00:14:22:01:23:45"
  ],
  "devices": [],
  "profiles": [
      {
          "source": "salesforce",
          "source_id": "37461828371",
          "link": "https://salesforce.com/customer/abc123"
      }
  ],
  "first_name": "John",
  "last_name": "Smith",
  "emails": [
      "john.smith@gmail.com"
  ],
  "marketing_consent": true,
  "marketing_consented_at": "2019-03-05T21:19:19.696Z",
  "avatar_image": "https://cdn1.iconfinder.com/data/icons/user-pictures/100/male3-512.png",
  "bio": "An all round great guy, enjoys long walks on the beach.",
  "hometown": "New York, New York",
  "location": "Sydney, Australia",
  "link": "https://salesforce.com/customer/abc123",
  "website": "https://google.com",
  "custom_traits": {
      "favourite_food": "Pizza",
      "loyalty_level": "Elite Plus",
      "loyalty_number": "AU8759342",
      "loyalty_status": "Platinum",
      "scorpio": "Beorn is scorpio"
  },
  "updated_at": "2019-03-05T21:19:19.696Z"
}
```

## Events

This endpoint will return a list of events associated with a Customer Profile. These include visits submitted through the `/v1/geolocation`, `/v2/geovenue` endpoints and our [Pulse](https://www.localmeasurepulse.com) product.

### HTTP Request

`GET /v2/profiles/<customer_id>/events`

```shell
curl -X GET \
  'https://public-api.getlocalmeasure.com/v2/profiles/00:14:22:01:23:45/events' \
  -H 'Authorization: YOUR_API_KEY'
```

> Example Response

```json
[
    {
        "type": "wifi_visit",
        "place": "Level 1",
        "start": "2019-03-05T21:45:13Z",
        "end": "0001-01-01T00:00:00Z",
        "context": {
            "on_site": true
        }
    },
    {
        "type": "pulse_response",
        "place": "The Overlook Hotel",
        "start": "2019-01-15T16:00:42.398Z",
        "end": "2019-01-15T16:00:42.398Z",
        "context": {
            "pulse": "/v2/pulse/5ad8b96d5a1763d7f2474a64",
            "response": "/v2/pulse/5ad8b96d5a1763d7f2474a64/responses/5c3e03aa11533fef7d482c62"
        }
    },
]
```

### Parameters

Parameter | Description
----------|------------
`skip` | Defaults to `0`; used for paginating results
`limit` | Defaults to `10`; used for paginating results

# Pulse

The `pulse` endpoints provide access to:

- responses to all your Pulsepoints – `/v2/pulse/responses`
- responses to a specific Pulsepoint – `/v2/pulse/{pulsepoint_id}/responses`

Both support the same parameters, and return data in the same format, but the examples use the first endpoint.

### HTTP Request

`GET /v2/pulse/responses`

```shell
curl -X GET \
  'https://public-api.getlocalmeasure.com/v2/pulse/responses' \
  -H 'Authorization: YOUR_API_KEY'
```

> Example Response

```json
[
    {
        "id": "5c3e03aa11533fef7d482c62",
        "actioned_on": "0001-01-01T00:00:00Z",
        "actioned": false,
        "answers": [
            {
                "question_text": "How could we make your experience better?",
                "answer": "Less work and more play."
            }
        ],
        "email": "jack@overlook.hotel",
        "name": "Jack Torrence",
        "rating": 3,
        "room": "237",
        "ip_address": "",
        "profile": "/v2/profiles/857c161d6f6adfe499f2864c18e0884a",
        "pulse": "/v2/pulse/5ad8b96d5a1763d7f2474a64",
        "timestamp": "2019-01-15T16:00:42.398Z"
    }
]
```

### Parameters

Parameter | Description
----------|------------
`skip` | Defaults to `0`; used for paginating results
`limit` | Defaults to `10`; used for paginating results
`from` | (Inclusive) date e.g., `2018-10-02T00:00:00Z` to restrict responses to those seen on or after this date.
`to` | (Exclusive) date e.g., `2018-10-04T23:59:59Z` to restrict responses to those seen before this date.
`rating` | Limits responses to those that match the required `rating` e.g., `1`, `2`, `3`, `4` or `5`. Can be specified multiple times.
