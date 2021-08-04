---
layout: default
title: The start endpoint
nav_order: 4
permalink: /start-endpoint
---

# Using JVerify's start endpoint
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

The first step of the verification process, send a `POST` request to `https://jverify.us/start` with your API key, the user's name, and the vendor name and the user's phone number as raw json data:

```json
{
    "key":"T3h5N3RyUPNT4NkQ73cuUtdh_cpNsXHG...", // Get a new key from your JVerify account
    "name":"John Smith", // Name of the user. This will be included in the text message
    "vendor":"Company Name", // Name of your project, product or company. This will be included in the message
    "to":2025550106 // Phone number of your user.
}
```

The message that is sent to your user is:

```
Hello John Smith! Your PIN for Company Name is 123456. Do not share it with anyone, and it will expire in 3-6 minutes.
```

### HTTP Signature

```http
POST /start HTTP/1.1
Host: jverify.us
Content-Type: application/json
Content-Length: 121

{
    "key":"T3h5N3RyUPNT4NkQ73cuUtdh_cpNsXHG...",
    "name":"John Smith",
    "vendor":"Company Name",
    "to":2025550106
}
```

### cURL sample

```
curl --location --request POST 'https://jverify.us/start' \
--header 'Content-Type: application/json' \
--data-raw '{
  "key":"T3h5N3RyUPNT4NkQ73cuUtdh_cpNsXHG...",
  "name":"John Smith",
  "vendor":"Company Name",
  "to":2025550106
}'
```

### JavaScript sample

```javascript
var request = require('request');
var options = {
  'method': 'POST',
  'url': 'https://jverify.us/start',
  'headers': {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    "key":"T3h5N3RyUPNT4NkQ73cuUtdh_cpNsXHG...",
    "name":"John Smith",
    "vendor":"Company Name",
    "to":2025550106
  })

};
request(options, function (error, response) {
  if (error) throw new Error(error);
  console.log(response.body);
});
```

## Status Codes

JVerify will return one of many status codes. See what they mean here.

| Code  | Official Name                   | JVerify Meaning                                              |
| ----- | ------------------------------- | ------------------------------------------------------------ |
| `200` | `OK`                            | Everything is functioning as intended                        |
| `203` | `Non-Authoritative Information` | The account was not charged for the message, but everything went through fine. You should only see this if JVerify has taken you on as a pro-bono client. Contact us for troubleshooting support if you see this or if you should be seeing this. |
| `401` | `Unauthorized`                  | The token is incorrect.  If this occurs, double-check your key and if it appears correct, request a new one from the dashboard. If that fails, contact us. |
| `402` | `Payment Required`              | Something went wrong verifying your account exists and is in good standing. If this occurs, double-check your key and if it appears correct, request a new one from the dashboard. If that fails, contact us. |
| `403` | `Forbidden`                     | The request was made from a domain you have not authorized in the dashboard. To fix this, change your allowed domain in the dashboard or remove the restriction. |
| `406` | `Not Acceptable`                | The token you submitted equals *T3h5N3RyUPNT4NkQ73cuUtdh_cpNsXHG...* This token is the one used in our documentation and cannot be used for real requests. Request your token (key) from the dashboard. |
| `422` | `Unprocessable Entity`          | Your request either had missing body content or the body content was empty. |
| `429` | `Too Many Requests`             | **JVerify does not rate limit**. We use the 429 header to inform you that you have gone over the maximum number of messages authorized in the dashboard and the message was not delivered. To fix this, wait for the **1st day of the next billing period** or increase the number of authorized messages in the dashboard |
| `500` | `Internal Server Error`         | Something went wrong on our end. Please contact us and we will get it resolved. |

