## Terms
* CORS
* JSONP

## CORS security model

All of the major web browsers have agreed to follow the Web Application Security Model outlined by the [w3c](http://www.w3.org) for [Same Origin Policy](http://www.w3.org/Security/wiki/Same_Origin_Policy).

What this means is that the browsers enforce the rules that the servers request. And if the browser doesn't set any rules, then the default behavior is to not allow scripts across different domains.

### What are the options?

**JSON-P**

You may have heard of JSON-P. What it does is wrap your JSON requested from the server into a callback function and then executes it with exec.

This is a glorious hack that somehow works, but only for GET requests. This will not save you from POSTs, PUTs, DELETEs, or any other HTTP verb.

JSON-P was introduced as one way to solve this problem, but it was an early solution and we have many more advanced options now. There are still APIs that use this (namely, Google Maps!) but it's being phased out by the industry.

**CORS**

CORS stands for Cross Origin Resource Sharing. It is a standard for allowing browsers to request resources from apis on other domains. This is perfect for us, as this is exactly what we are looking for.

But how do we tell the browser that we want it to be allowed to access that api?

The answer is counterintuitive. The server will tell the browser who is allowed, and the browser then enforces those rules. This means that you, the programmer, must write some code in order to allow other origins.

For Node, that code comes in the form of middleware before the api routes.

```javascript
app.use(function(req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
  next();
});
```

Lets go through this code one line at a time.

`res.header("Access-Control-Allow-Origin", "*");`

This is telling the browser that any other domain can access your api. You may want to change this from a '*' to the fully qualified domain name.

`res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");`

This is telling the browser what headers are allowed to be sent. If you want to add any additional headers, like a token header, you must add it here.

If you need to whitelist multiple domains, then you will need to make the middleware dynamic so that it will automatically choose what headers to send to the client.

## Exercise
## Server Setup

You should be getting more fluent with writing server side APIs in Express. Create a brand new API for a resource of your choice. Once that is complete, deploy to heroku and start setting up your client.

Note: For this exercise, you really only need a index route for any given resource, as your client will not perform full CRUD operations.

## Client setup

Now we'll set up a completely separate application in a separate directory with a completely separate github repo. It will include a couple of files:

```
- index.html
- app.js
- axios.js
```

The app can use any XHR library of your choice, but I recommend using axios because it has great built in promise support.

Just cd into your client app and curl the axios library into your api-client directory. `curl https://raw.githubusercontent.com/mzabriskie/axios/master/dist/axios.js > axios.js`

Next, you'll need to add your axios.js and app.js script tags in `index.html` to use them.

Once your directory is setup and axios is loaded, you should be able to use it in app.js like so:

```
axios.get('http://localhost:8008/api/items') // your API domain
  .then(function (response) {
    console.log(response);
  });
```

## An Error

`XMLHttpRequest cannot load http://localhost:8080/api/swords. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost:8081' is therefore not allowed access.`

This doesn't work because the browser forces a strict Same Origin Policy. Your request is being made from one domain to another and the browser just won't have that.

To understand the need for the Same Origin Policy and consequently for CORS, take the time to review all of the following material. After you dig into and digest the following material, you should be able to answer the following questions in your own words:

* Read: https://en.wikipedia.org/wiki/Same-origin_policy
* Read the question and first answer: http://security.stackexchange.com/questions/8264/why-is-the-same-origin-policy-so-important

### !challenge

* type: short-answer
* title: same origin policy
* id: 400

##### !question
What is the same origin policy? Why is it enforced?
##### !end-question

##### !placeholder
Write your answer
##### !end-placeholder

##### !answer
##### !end-answer

##### !explanation
##### !end-explanation

### !end-challenge

### !challenge

* type: short-answer
* title: what is jsonp
* id: 401

##### !question
What is JSONP?
##### !end-question

##### !placeholder
Write your answer
##### !end-placeholder

##### !answer
##### !end-answer

##### !explanation
##### !end-explanation

### !end-challenge

* Watch: https://www.youtube.com/watch?v=rlnhiwN8AnU
* Read: https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS
* Read: https://en.wikipedia.org/wiki/JSONP

### !challenge

* type: short-answer
* title: what is cors
* id: 402

##### !question
What is CORS? How is it useful?
##### !end-question

##### !placeholder
Write your answer
##### !end-placeholder

##### !answer
##### !end-answer

##### !explanation
##### !end-explanation

### !end-challenge

### !challenge

* type: short-answer
* title: why is cors better than jsonp
* id: 403

##### !question
Why is CORS preferred over JSONP? What advantages does it give us over JSONP?
##### !end-question

##### !placeholder
Write your answer
##### !end-placeholder

##### !answer
##### !end-answer

##### !explanation
##### !end-explanation

### !end-challenge

## Rationalize

Phwew! That's a lot to take in. It's a lot to learn! You may be asking yourself why all of this is 100% needed to build apps. After all, you've been building fullstack applications without this information for a while now. Well, it all comes down to the need to separate our client from the server. It all comes from the need to build Single Page Applications (SPAs).

To review some of the benefits and rationalizations for SPAs, take a few minutes to watch this segment (just 10 min or so) of this conference talk on the YouTubes: https://www.youtube.com/watch?v=OrIFaWJ9Glo&feature=youtu.be&t=915

## Configure CORS

Alright, so CORS is needed to let us separate our client and server code.

## Setup

The good news is, the [cors](https://www.npmjs.com/package/cors) node module makes setting up CORS extremely simple. Once installed, it's as simple as including the middleware in your application.

```
var cors = require('cors')
app.use(cors())
```

Make sure that any Express middleware being used (such as CORS, in this case) is configured before the routes are matched.

Once this is installed, axios on the client will be able to communicate with the API as needed. You'll know it's configured correctly when axios is able to get data from the API. If you jump back over to your project making the axios request, the error should be gone and the data should be logged to the console. Success!

## Reflect

Setting that up was actually pretty simple with Express. It's just two lines of code, so we can assume it's doing a lot for us under the hood. Given what you know about CORS, consider the following:

### !challenge

* type: short-answer
* title: what ACAO header
* id: 404

##### !question
What `Access-Control-Allow-Origin:` header value must our application be using, given that we didn't specify any domain names?
##### !end-question

##### !placeholder
Write your answer
##### !end-placeholder

##### !answer
*
##### !end-answer

##### !explanation
* means that the resource can be accessed by any domain in a cross-site manner.
##### !end-explanation

### !end-challenge

### !challenge

* type: short-answer
* title: how to configure CORS specifically
* id: 405

##### !question
Review the npm cors module documentation. How would you configure CORS to only work with the client applications that you've approved of?
##### !end-question

##### !placeholder
Write your answer
##### !end-placeholder

##### !answer
##### !end-answer

##### !explanation
##### !end-explanation

### !end-challenge
