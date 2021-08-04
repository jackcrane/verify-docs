---
layout: default
title: The verify endpoint
nav_order: 5
permalink: /verify-endpoint
---

# Using JVerify's verify endpoint
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

The second step of the verification process, send a `POST` request to `https://jverify.us/verify` with your API key, the token from the start endpoint, and the pin from your user as raw json data:

```json
{
    "key":"T3h5N3RyUPNT4NkQ73cuUtdh_cpNsXHG...",
    "hash":"cb7de200e1264d9f520d2d...",
    "pin":123456
}
```

### HTTP Signature

```http
POST /verify HTTP/1.1
Host: localhost
Content-Type: application/json
Content-Length: 109

{
    "key":"T3h5N3RyUPNT4NkQ73cuUtdh_cpNsXHG...",
    "hash":"cb7de200e1264d9f520d2d...",
    "pin":13456
}
```

### cURL sample

```
curl --location --request POST 'https://jverify.us/verify' \
--header 'Content-Type: application/json' \
--data-raw '{
    "key":"T3h5N3RyUPNT4NkQ73cuUtdh_cpNsXHG...",
    "hash":"cb7de200e1264d9f520d2d...",
    "pin":123456
}'
```

### JavaScript sample

```javascript
var request = require('request');
var options = {
  'method': 'POST',
  'url': 'https://jverify.us/verify',
  'headers': {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    "key":"T3h5N3RyUPNT4NkQ73cuUtdh_cpNsXHG...",
    "hash":"cb7de200e1264d9f520d2d...",
    "pin":123456})
};
request(options, function (error, response) {
  if (error) throw new Error(error);
  console.log(response.body);
});
```

## Status Codes

JVerify will return one of many status codes. See what they mean here.

| Code  | Official Name           | JVerify Meaning                                              |
| ----- | ----------------------- | ------------------------------------------------------------ |
| `200` | `OK`                    | JVerify properly received and processed the hash and pin. **A 200 response code does not mean the user submitted the correct pin.** |
| `401` | `Unauthorized`          | The token is incorrect.  If this occurs, double-check your key and if it appears correct, request a new one from the dashboard. If that fails, contact us. |
| `406` | `Not Acceptable`        | The token you submitted equals *T3h5N3RyUPNT4NkQ73cuUtdh_cpNsXHG...* This token is the one used in our documentation and cannot be used for real requests. Request your token (key) from the dashboard. |
| `422` | `Unprocessable Entity`  | Your request either had missing body content or the body content was empty. |
| `500` | `Internal Server Error` | Something has gone wrong on our end. Contact us and we will get it resolved. |