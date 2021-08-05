---
layout: default
title: Getting started
nav_order: 1
parent: Javascript Client Library
---

# How to use the library
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Install prerequisites

To use this library, you will need the jverify library and requests installed:

```
npm install jverify-js requests
```

Let npm do its thing, you may need to delete `package-lock.json` if you have issues in the next step.

## Import the library and set up authorization

```javascript
const JVerify_lib = require('jverify-js');

const JVerify = new JVerify_lib({
  token:'T3h5N3RyUPNT4NkQ73cuUtdh_cpNsXHG...', // Get a token from the JVerify dashboard (jverify.us/dashboard)
  vendor:'Your Company' // Set this to your company name
});
```

Now the library is set up to send messages (assuming your token is valid)! Notice how the constructor accepts an object with this schema:

```json
{
  "token":"your-token",
  "vendor":"your-company-name"
}
```

## Verify your token is valid

This step is optional, but extremely recommended as it helps you avoid accidental bugs down the road with an invalid token.

### Using the `Promise` API

```javascript
JVerify.checkToken()
  .then(() => {
    // Token is valid, your application is good to continue
  })
  .catch((error) => {
    console.log(error.message)
    // Token is invalid. Check the dashboard or verify the token
  })
```

If the token check failed, this function will return an object:

```json
{
  "message": "What went wrong"
}
```

### Using `async`/`await`

```javascript
await JVerify.checkToken().catch(e => {throw new Error(e.message)})
if(JVerify.getTokenConfirmed()) {
  // Token is valid, your application is good to continue
}
```

Pro tip: If your application is not running in an async function, you can wrap this logic with an async anonymous function:

```javascript
(async function() {
  await JVerify.checkToken().catch(e => {throw new Error(e.message)})
  if(JVerify.getTokenConfirmed()) {
    // Token is valid, your application is good to continue
  }
})()
```

If the token check was successful, the function will not return anything

The message key will be `null` if the token was successfully verified.