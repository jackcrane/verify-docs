---
layout: default
title: The start method
nav_order: 2
parent: Javascript Client Library
---

# The Start Method
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Function signature

The `.start()` method accepts an object with the following schema:

```json
{
  "name": "Customer name",
  "number": 5135555050
}
```

The verification text message will be sent to the number supplied, and we will refer to them as the name supplied.

The function will return a promise that when resolved will supply an object with the following schema:

```json
{
  "code": 200,
  "message": "What the above code means (from the JVerify documentation)",
  "status": "What the code officially means",
  "body": {
    "success": true | false,
    "hash": "SHA-256 hash"
  }
}
```

You will use the `body.hash` string in the verify request.

## Code Sample

### Using the `Promise` API

```javascript
let hash; // Globally initialize a variable to hold the hash. This could be stored in a database or session storage as well. It is non-sensitive so it is safe to send this to your frontend

JVerify.start({
  name:'Customer Name', // Name of your user
  number:2025550106 // Phone number of your user (where the message will be sent)
}).then(r => {
  if(r.code == 200 || r.code == 203) {
    hash = r.body.hash; // Save the hash to the global variable
    // Server-side logic for a properly sent message.
    // Tell the frontend the message was sent properly
  } else {
    // Something has gone wrong, lets throw an error and check it out in the console
    // Tell the frontend something has gone wrong.
    throw new Error(`JVerify threw error ${r.code} (${r.status}): ${r.message}`);
  }
})
```

### Using the `async`/`await` API

```javascript
let hash; // Globally initialize a variable to hold the hash. This could be stored in a database or session storage as well. It is non-sensitive so it is safe to send this to your frontend

let start = await JVerify.start({
  name:'Customer Name',
  number:2025550106
}).catch(e => {throw new Error(e.message)})
if(start.body.success) {
  hash = start.body.hash;
  // Message successfully sent, you can continue your application here.
} else {
  throw new Error(`JVerify threw error ${start.code} (${start.status}): ${start.message}`);
}
```