# CORS

## Terms:
* __CORS__ - _Cross-Origin Resource Sharing_. An HTML5 feature that allows one site to access another site's resources despite having a different domain names (origin).
* __JSONP__ - _JSON with Padding_. Used to request data from a server residing in a different domain than the client. This enables sharing of data in spite of the same-origin policy.

## Same-Origin Policy security model

All of the major web browsers implement the Web Application Security Model outlined by the [w3c](http://www.w3.org) for [Same Origin Policy](http://www.w3.org/Security/wiki/Same_Origin_Policy). This policy is a security concept implemented by web browsers to prevent JavaScript code from making queries against a different origin. In other words, the same-origin policy prevents a web application from calling an external API. The browser only considers resources to be of the same origin if they use the same protocol (http/https), the same port, and the same domain -- even different subdomains will be blocked.

The purpose of this policy is to protect against malicious scripting attacks. Unfortunately, it also prevents non-malicious web applications from accessing resources to improve UX.

To overcome the limitations of the same-origin policy, JSON-P (kind of a hack) and CORS (a new HTML5 feature) can be  implemented. With the adoption of CORS, developers can now leverage cross-origin images, stylesheets, scripts, iframes, web fonts, AJAX API calls, videos, scripts, etc to improve web applications.

### Getting Around the Same-Origin Policy

The HTML `<script>` tag _is_ allowed to execute content retrieved from foreign origins. However, services replying with pure JSON data are not allowed to share data across domains, and any attempt to use this data from another domain will result in a JavaScript error. The browser downloads the `<script>` file, evaluate the contents, misinterprets the raw JSON data as a block, and then throws a syntax error. Even if the JSON were interpreted as a JavaScript object literal, it cannot be accessed by the JavaScript running in the browser because there is no variable assignment on the object literals.

**JSON-P**

JSONP wraps your JSON requested from the server into a callback function and then executes it with exec.

This is a glorious hack that somehow works, but only for GET requests. This will not save you from POSTs, PUTs, DELETEs, or any other HTTP verb.

JSON-P was introduced as one way to solve this problem, but it was an early solution and we have many more advanced options now. There are still APIs that use this (namely, Google Maps!) but it's being phased out by the industry.

**CORS**

CORS stands for Cross Origin Resource Sharing. It is a standard for allowing browsers to request resources from apis on other domains. This is perfect for us, as this is exactly what we are looking for.


## CORS Security Model

What this means is that the browsers enforce the rules that the servers request. And if the browser doesn't set any rules, then the default behavior is to not allow scripts across different domains.


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

Let's break that down line-by-line.

```javascript
res.header("Access-Control-Allow-Origin", "*");
```

This informs the browser that any other domain can access your api. You may want to change this from a "*" to a fully qualified domain name.

```javascript
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
```

This is telling the browser what headers are allowed to be sent. If you want to add any additional headers, like a token header, you must add it here.

If you need to whitelist multiple domains, then you will need to make the middleware dynamic so that it will automatically choose what headers to send to the client.


What is the same origin policy? Why is it enforced?
what is jsonp
What is CORS? How is it useful?
why is cors better than jsonp
Why is CORS preferred over JSONP? What advantages does it give us over JSONP?

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


## Resources

[Same-Origin Policy: MDN](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)

[Understanding CORS: Spring.io](https://spring.io/understanding/CORS)

[Understanding and using CORS: Restlet Blog](http://restlet.com/blog/2015/12/15/understanding-and-using-cors/)

[Understanding Cross-Origin Resource Sharing (CORS): Adobe Developer Connection](https://www.adobe.com/devnet/archive/html5/articles/understanding-cross-origin-resource-sharing-cors.html)

[Cross-Origin resource sharing: wikipedia](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing#How_CORS_works)

[Same-Origin Policy: Wikipedia](https://en.wikipedia.org/wiki/Same-origin_policy)

[JSONP: Wikipedia](https://en.wikipedia.org/wiki/JSONP)

[Security StackExchange: Why is the same origin policy so important](http://security.stackexchange.com/questions/8264/why-is-the-same-origin-policy-so-important)
