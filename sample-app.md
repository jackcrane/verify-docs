---
layout: default
title: Sample App
nav_order: 7
permalink: /sample-app
---

# Get started with a pre-made integration
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Download

If you want to skip the explanations and copy-pasting code, you can download the source code here:

[Download zip](/jverify-sample-nodejs.zip){: .btn .btn-blue }

## File layout

This project is based on node.js express. The file structure is as follows:

```
.
├── app.js
├── package.json
└── views
    └── index.ejs
```

## `package.json`

In your `package.json` file, enter the following code:

```json
{
  "name": "jverify-sample-node",
  "version": "1.0.0",
  "description": "A node.js integration of the JVerify sample code",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "author": "JVerify",
  "dependencies": {
    "body-parser": "^1.19.0",
    "ejs": "^3.1.6",
    "express": "^4.17.1"
  }
}
```

Of course you can name, describe, and version your app however you wish, but everything else should be the same.

Once the code above is pasted into `package.json`, run this in your command line:

```
npm install
```

This will install all our dependencies that we described in `package.json`.

## The Back End (`app.js`)

In our `app.js` file, we need to

1. Create an express app
2. Parse the request body into the necessary routes
3. Handle interactions with JVerify
4. Send information to our frontend

Copy-paste the following code into your `app.js` file

```javascript
// Import our dependencies
let request = require('request');
const express = require('express');
const bodyParser = require('body-parser');
const ejs = require('ejs');

const JVERIFY_API_KEY = 'T3h5N3RyUPNT4NkQ73cuUtdh_cpNsXHG...' // Your JVerify API key. Get a key from jverify.us/dashboard

const port = 3000;

// Set up our Express engine and connect to EJS
const app = express();
app.set('view engine', 'ejs');

// Set up our first UI endpoint
app.get('/', (req, res) => {
  res.render('index.ejs', {})
})

// Create the API endpoint for our frontend to call to request the pin
app.post('/send-verification', bodyParser.json(), async (req, res) => {
  // Define request options
  let options = {
    'method': 'POST', // Needs to be a POST request
    'url': 'https://jverify.us/start',
    'headers': {
      'Content-Type': 'application/json' // We are sending JSON data. This is all that JVerify accepts right now.
    },
    body: JSON.stringify({ // Body of the request
      "key":JVERIFY_API_KEY,
      "name":"John Smith", // Name of the customer
      "vendor":"JVerify", // Name of your company
      "to":req.body.phone_num // Customer's phone number
    })
  };

  const JVerify_response = await request(options, (error, response) => {
    if (error) throw new Error(error); // Throw error if something went wrong
    const response_object = JSON.parse(response.body) // Decode response JSON string to JS Object
    if(response_object.success) { // Verify the message was sent successfully
      res.send(JSON.stringify({hash:response_object.hash})) // Send the hash to the client
    } else {
      // Handle if JVerify responded properly but something else went wrong
      throw new Error('Verification issue.');
    }
  });
})

// Create the API endpoint for our frontend to call to verify the pin
app.post('/check-verification', bodyParser.json(), async (req, res) => {
  let options = {
    'method': 'POST', // Needs to be a POST request
    'url': 'https://jverify.us/verify',
    'headers': {
      'Content-Type': 'application/json' // We are sending JSON data. This is all that JVerify accepts right now.
    },
    body: JSON.stringify({
      key:JVERIFY_API_KEY,
      hash:req.body.hash, // Hash returned from start API request. This can also be saved in session storage if you don't want to send to the client.
      pin:req.body.pin // Value from the pin input
    })
  };
  request(options, function (error, response) { // Send the request
    if (error) throw new Error(error); // Throw errors if something went wrong
    if(JSON.parse(response.body).correct) {
      // Backend logic if the pin was correct
      res.send(JSON.stringify({correct:true})) // Respond to the client
    } else {
      // Backend logic if the pin was incorrect
      res.send(JSON.stringify({correct:false})) // Respond to the client
    }
  });
})

// Start Express server
app.listen(port, () => {
  console.log(`Sample app listening at http://localhost:${port}`)
})
```

Make sure to replace the JVerify API key with your key.

## The Front End (`views/index.ejs`)

On our frontend, we need to 

1. Display a form for the user to enter their information
2. Process & send that information to our backend
3. Change the form to handle a user entering their pin
4. Send the pin to the backend
5. Display a confirmation if the pin was correct.

Copy-paste the following code into `views/index.ejs`.

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title></title>
    <style media="screen">
      #verify {
        display:none;
      }
    </style>
  </head>
  <body>
    <div id="start">
      <input type="tel" placeholder="Your phone" id="phone_num">
      <input type="submit" name="" value="Submit" id="submit_request">
    </div>
    <div id="verify">
      <input type="number" size="6" id="pin" placeholder="Enter the pin texted to you">
      <input type="submit" name="" value="Submit" id="submit_verify">
    </div>
  </body>
  <script type="text/javascript">
    let hash; // Create a variable to hold the hash of the PIN. Notice it is scoped above both request blocks

    document.querySelector('#submit_request').addEventListener('click', e => {
      fetch('/send-verification', { // Send the verification number
        method:'POST',
        headers:{'Content-Type':'application/json'},
        body:JSON.stringify({
          phone_num:document.querySelector('#phone_num').value // Send the user's phone number to the server
        })
      })
        .then(response => response.json()) // Parse response data
        .then(data => {
          hash = data.hash // Save the hash to the global variable
          document.querySelector('#start').style.display = 'none'; // Hide the phone number input
          document.querySelector('#verify').style.display = 'block'; // Show the pin number input
        })
    })

    document.querySelector('#submit_verify').addEventListener('click', e => {
      fetch('/check-verification', { // Check the pin number
        method:'POST',
        headers:{'Content-Type':'application/json'},
        body:JSON.stringify({
          pin:document.querySelector('#pin').value, // Send the pin to the server
          hash:hash // Send the hash to the server. This program does not save to sessions, but this can also be saved in session storage or as a cookie.
        })
      })
        .then(response => response.json()) // Parse the response data
        .then(data => {
          if(data.correct) { // If the submitted PIN is correct, hide the pin input box and alert the user the pin was correct.
            document.querySelector('#verify').style.display = 'none';
            alert('Correct');
            // Handle correct pin submission on your frontend here
          } else {
            alert('Incorrect');
            // Handle incorrect pin submission on your frontend here
          }
        })
    })
  </script>
</html>
```

## Running your code

Because this application is written on node.js, you will use `npm` to run the app. In your command line, enter the following:

```
npm start
```

And your application should be online!