# Errors

Our API uses the following error codes:

Error Code | Meaning
---------- | -------
`400` | `Bad Request` – your request has fields which have invalid data in them.
`401` | `Unauthorized` – your API key is not correct.
`403` | `Forbidden` – your API key does not have access to this endpoint.
`404` | `Not Found` – the specified API could not be found.
`405` | `Method Not Allowed` – you tried to access an endpoint with an invalid method.
`406` | `Not Acceptable` – your `Accept` header specified a format unsupported by the endpoint. Unless stated otherwise, endpoints only support JSON.
`422` | `Unprocessable Entity` – your request was invalid. This usually means some of the required parameters were missing. Information about the specific errors will be returned in the response. 
`429` | `Too Many Requests` – you're requesting our API too many times! Slow down!
`500` | `Internal Server Error` – we had a problem with our server. Try again later.
`503` | `Service Unavailable` – we're temporarily offline for maintenance. Please try again later.
