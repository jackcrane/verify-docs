---
layout: default
title: The verify method
nav_order: 2
parent: Javascript Client Library
---

# The Verify Method
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Function signature

The `.verify()` method accepts an object with the following schema:

```json
{
  "hash":"hash-from-start-method",
  "pin":"pin from frontend" // Needs to be an integer
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
    "correct": true | false
  }
}
```

## Code Sample

### Using the `Promise` API

```javascript
JVerify.verify({
  hash:'hash-from-start-method', // This should be the hash variable we got in the start method
  pin:393545 // The pin the user entered
}).then(r => {
  if(r.code > 199 && r.code < 300) { // Make sure the request returned a code in the 200s
    if(r.body.correct) {
    	// The user entered the correct pin. 
    	// Implement server-side logic and tell the frontend the pin was entered correctly
    } else {
      // The user has entered the incorrect pin
      // Tell the frontend the pin was wrong and potentially ask them if they want to send another
    }
	} else {
    // Something has gone wrong. Lets throw an error and check it out in the console
    // Tell the frontend something has gone wrong
    throw new Error(`JVerify threw error ${r.code} (${r.status}): ${r.message}`);
  }
})
```

### Using the `async`/`await` API

```javascript
let verify = await JVerify.verify({
  "hash": "hash-from-start-method",
  "pin": 652265 // Pin from your user
}).catch(e => {throw new Error(`JVerify threw error ${e.code} (${e.status}): ${e.message}`);})
if(verify.body.correct) {
  // Pin was correct, continue with your application
} else {
  // Pin was incorrect, have them retry
}
```