# APIs with Express \(axios\)

### Objectives

* Describe the purpose of using an API on the backend.
* Create an application that uses an API and the `axios` module.

In order to get around issues such as [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS) and API access control, we can communicate with other servers through our server. In this case, our server acts as both a client and a server. We'll be doing some requests using the `axios` Node module.

To use the module, install it using npm.

```bash
npm i axios
```

Here's an example from NPM's homepage for the `axios` module. Let's take a look at it.

```javascript
const axios = require('axios');

// Make a request for a user with a given ID
axios.get('/user?ID=12345')
  .then(function (response) {
    // handle success
    console.log(response);
  })
  .catch(function (error) {
    // handle error
    console.log(error);
  })
  .finally(function () {
    // always executed
  });
```

Identify what's necessary for making a server-side request.

* axios module import
* axios.get function
  * URL
  * any other data or headers that need to be passed \(optional\)
  * `then` promise \(the code that runs once the request finishes\)
    * handle a success
    * catch any raised exceptions or errors \(optional\)
    * run something at the end regardless \(optional\)

### Incorporating `axios` into Express

In order to incorporate the `axios` module into Express, we can set up a basic Express application and place the request code inside a route.

This can be done by creating a new directory, running `npm init`, then installing the correct dependencies \(refer back to the notes if you forgot\). Here's an example app.

#### Example

**server.js**

```javascript
const express = require('express')
const axios = require('axios')
const app = express()

app.get('/', (req, res)=>{
  axios.get('http://www.google.com')
    .then(response=>{
      // handle success
      console.log(response)
    })
})

app.listen(3000)
```

Similar to the `fetch` object that the `fetch` gave us back in our front-end API calls, axios also gives us a back a `response` object that is actually a wrapper containing the api data among other things. According to the [axios docs](https://www.npmjs.com/package/axios#response-schema), the return data from the API will be in the `response.data` object. Try console logging this instead of just `response`.

Note that this app sends out the HTML for [http://www.google.com](http://www.google.com), minus the images due to the images having links **relative** to [http://localhost:3000](http://localhost:3000)

Let's use a more useful source of data that we can parse, like OMDB \(Open Movie Database\)

**API Keys**

Notice that OMDB API has a key requirement for their API. That's okay, it just means we'll need to register for a key real quick before running the example. Don't worry - it's free and only takes a few minutes. Lots of APIs will require keys, so let's get into the habit!

### Fetching JSON data

Let's modify the example above to make a request to OMDB's API. [OMDB Link](http://www.omdbapi.com/)

**We'll be using this endpoint:** [http://www.omdbapi.com/?s=star+wars&apikey=yourkey123](http://www.omdbapi.com/?s=star+wars&apikey=yourkey123)

#### Modified Example

**server.js**

```javascript
const express = require('express')
const axios = require('axios')
const app = express()

app.get('/', (req, res)=>{
  axios.get('http://www.omdbapi.com/?apikey=6827ed10&s=star+wars')
    .then((response)=>{
        res.send(response.data)
    })
})

app.listen(3000)
```

It's very important to call `res.send` in the correct place \(the axios 'then' promise\). Try putting `res.send` outside of the 'then'. You'll get an error!

## Environment variables

**Environment variables** are variables that aren't visible in any setting where your code is made available for reading (dev tools, github, etc.) These are values that may be specific to a particular developer or development environment, OR variables that have values we want to stay hidden for security reasons. 
* Frequently, we'll have variables that are unique to a particular computer. An example is the PORT.
* We use variables to avoid committing values that are either sensitive or vary from machine to machine.
* When we use API keys that are meant to be private/secret, this is a case where we DO NOT WANT TO COMMIT THE VALUES. These values can vary, but if a malicious user gets ahold of them, they can cause disastrous results, especially if the values access an account that costs money or resources.

Let's make our API key an environment variable:

1. `npm i dotenv`
2. touch `.env`
3. add your api key to the .env file like so: `API_KEY=5555555` but with your real API key
4. configure things by adding `require('dontenv').config()` to the top of your entry point file
4. replace your api key in your axios call with `process.env.API_KEY` (hint: use es6 string interpolation)

## Additional Topics

* [API Information](../../12-resources/apis.md)
* [Using APIs with Client/Secret Keys \(Foreman\)](../../00-config-deployment/deploy-rails/foreman.md)

