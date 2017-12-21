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

`Authorization: your-api-key`

### Parameter
Include the `access_token` parameter in your HTTP uri with your API key as the value

`https://public-api.getlocalmeasure.com/v1/endpoint?access_token=your-api-key`

You can register a new Local Measure API key in our [plugin portal](http://dashboard.getlocalmeasure.com/account/?tab=plugins).

# Identify

Identify a customer to Local Measure by calling this endpoint. A customer profile will be created or updated based on the fields provided.

Customer profiles are updated when a matching profile is found based on the following precedence criteria:
- `unique_id`
- `email`

### HTTP Request

`POST /v1/identify`

> Example payload:

```json
  {
    "unique_id": "00:14:22:01:23:45",
    "email": "john.smith@gmail.com",
    "marketing_consent": true,
    "first_name": "John",
    "last_name": "Smith",
    "birthdate":"1970-05-02",
    "gender":"male",
    "avatar_image":"https://cdn1.iconfinder.com/data/icons/user-pictures/100/male3-512.png",
    "social": [{
        "source": "facebook",
        "source_id": "87235872",
        "link": "https://facebook.com/john.smith.478653",
        "bio": "An all round great guy, enjoys long walks on the beach.",
        "verified": true,
        "hometown": "New York, New York",
        "location": "Sydney, Australia",
        "avatar_image":"https://fbcdn.com/12398562498652498/me.png",
    },{
        "source": "instagram",
        "source_id": "245986569842",
        "link": "https://instagram.com/johnsmith/",
        "handle": "johnsmith",
        "followers_count": 500,
        "following_count": 200,
        "website": "https://foodblogger.com",
        "bio":"An all round great guy, enjoys long walks on the beach.",
        "avatar_image":"https://cdn1.iconfinder.com/data/icons/user-pictures/100/male3-512.png",
    },{
        "source": "twitter",
        "source_id": "72735729779824",
        "link": "https://twitter.com/johnnysmiddy/",
        "handle": "johnnysmiddy",
        "followers_count": 10000,
    }],
    "custom": [{
        "key":"Loyalty Level",
        "value":"Elite Plus"
    },{
        "key":"Loyalty Number",
        "value":"AU8759342"
    }]
  }
```

> On success of this request it will return a 200 and JSON structured like this:

```json
  {
    "status": "ok",
  }
```

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
unique_id | string | true | A unique identifier of customer. If you dont have one you can use their device mac or email address instead.
email | string | false | The email of the customer
mac | string | false | The device mac address of the customer
marketing_consent | string | false | Whether the customer gives consent to receive marketing material
first_name | string | true | First name of the customer
last_name | string | true | Last name of the customer
birthdate | string | false | The birthdate of the customer in the format YYYY-MM-DD
gender | string | false | The gender of the customer
avatar_image | string | false | An image representing the customer
social | array | false | An array of social identity data to link the profile with
social.source | string | false | The social network where this data is coming from. Valid sources are: facebook, instagram, twitter, weibo
social.source_id | string | false | The original social network id i.e. Facebook id (71485571121)
social.link | string | false | A link to the customers social profile
social.handle | string | false | The handle of the profile if applicable
social.bio | string | false | A brief description of the customer
social.verified | boolean | false | If the customer has been verified by the source identity provider
social.hometown | string | false | The hometown of the customer
social.location | string | false | The location the customer resides
social.followers_count | number | false | The current amount of followers
social.following_count | number | false | The current amount of users that they are following
social.website | string | false | The website of the user
social.avatar_image | string | false | An image representing the user
custom | array | false | An array of custom fields with key and value.
custom.key | string | false | The key for the custom field example "hotel_code"
custom.value | string | false | The value for the field example "abc123"

<aside class="success">
Remember — you need to have your request authenticated. If you don't you will receive a 403 request error.
</aside>

# Signed In

> Example payload:

```json
  {
    "unique_id": "00:14:22:01:23:45",
    "source": "facebook",
    "source_id": "42543634574",
    "seen_at": "2017-11-29T08:09:57Z",
    "ssid": "Hotel Conference Centre",
    "user_agent_string": "Mozilla/5.0 (Linux; Android 7.0; Pixel C Build/NRD90M; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/52.0.2743.98 Safari/537.36"
  }
```

> On success of this request it will return a 200 and JSON structured like this:

```json
  {
    "status": "ok",
  }
```

Provide sign-in events from Wi-Fi splash pages, captive portals or websites to document the customer journey in Local Measure.

### HTTP Request

`POST /v1/signedin`

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
unique_id | string | true | A unique identifier of customer. If you dont have one you can use the persons device mac or email address instead.
source | string | true | The social network where this data is coming from. Valid sources are: email, phone, facebook, instagram, twitter, weibo
source_id | string | true | The social network id i.e. Facebook id (71485571121), handle, email address or phone
seen_at | string | false | A datetime when the login occured, will default to now. In the format of a RFC 3339 datetime ( 2017-11-29T08:09:57Z )
ssid | string | false | If the customer signed in through a Wi-Fi network provide the SSID if available.
user_agent_string | string | false | The user agent string is used to detect if customer is using a Desktop or Mobile device.

<aside class="notice">
Remember - To use this endpoint you will need to be authenticated and provide an existing customer identity using the identify endpoint first.
</aside>

# Geo-Location

> Example payload:

```json
  {
    "unique_id": "00:14:22:01:23:45",
    "longitude": 151.20919,
    "latitude": -33.88668,
    "seen_at": "2017-11-29T08:09:57Z",
  }
```

> Or Bulk Example payload:

```json
  [{
    "unique_id": "00:14:22:01:23:45",
    "longitude": 151.20919,
    "latitude": -33.88668,
    "seen_at": "2017-11-29T08:09:57Z",
  },{
    "unique_id": "00:13:00:AE:44:12",
    "longitude": 151.20980,
    "latitude": -33.8899,
    "seen_at": "2017-11-29T08:09:57Z",
  },{
    "unique_id": "00:14:BC:00:22:11",
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

The geo-location endpoint uses the device wi-fi position to automatically create and end visits in a customer profile.

### HTTP Request

`POST /v1/geolocation`

### Frequency

We recommend sending location events regularly, every minute if possible.

When the customer geo-location is seen for the first time or after a previous exit we create a visit.
If we don't receive any location events for a device for a 30 minute period we will mark the customer visit as exited.

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | ------- | -----------
unique_id | string | true | A unique identifier of customer. If you dont have one you can use the persons device mac or email address instead.
longitude | float | true | Longitude of the identified device
latitude | float | true | Latitude of the identified device
seen_at | string | true | A datetime when the device was last seen. In the format of a RFC 3339 datetime ( 2017-11-29T08:09:57Z )

<aside class="notice">
Remember - To use this endpoint you will need to be authenticated and provide an existing customer identity using the identify endpoint first.
</aside>
