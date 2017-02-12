# CORS

## Same-Origin Policy security model

All of the major web browsers implement the Web Application Security Model outlined by the [w3c](http://www.w3.org) for [Same Origin Policy](http://www.w3.org/Security/wiki/Same_Origin_Policy). This policy is a security concept implemented by web browsers to prevent JavaScript code from making queries against a different origin. In other words, the same-origin policy prevents a web application from calling an external API. The browser only considers resources to be of the same origin if they use the same protocol (http/https), the same port, and the same domain -- even different subdomains will be blocked.

The purpose of this policy is to protect against malicious scripting attacks. Unfortunately, it also prevents non-malicious web applications from accessing resources to improve UI/UX.

To overcome the limitations of the same-origin policy, JSON-P (kind of a hack) and CORS (a new HTML5 feature) can be  implemented. With the adoption of CORS, developers can now leverage cross-origin images, stylesheets, scripts, iframes, web fonts, AJAX API calls, videos, scripts, etc to improve web applications.

### Getting Around the Same-Origin Policy

The HTML `<script>` tag is able to execute content retrieved from foreign origins. However, services replying with pure JSON data are not allowed to share data across domains, and any attempt to use this data from another domain will result in a JavaScript error. The browser downloads the `<script>` file, evaluate the contents, misinterprets the raw JSON data as a block, and then throws a syntax error. Even if the JSON were interpreted as a JavaScript object literal, the JSON cannot be accessed by the JavaScript running in the browser because there is no variable assignment on the object literal.

#### JSON-P

In the JSONP usage pattern, the URL request pointed to by the `src` attribute in the `<script>` tag returns JSON data, with JavaScript code (usually a function call) wrapped around it. This "wrapped payload" is then interpreted by the browser, and the function that is already defined in the JavaScript environment can manipulate the JSON data. The function invocation to `parseResponse()` is the "P" (the padding) around the JSON.

For JSONP to work, a server must reply with a response that includes the JSONP function. The JSONP function invocation that gets sent back, and the payload that the function receives, must be agreed-upon by the client and server.

```HTML
<script type="application/javascript"
        src="http://server.example.com/Users/1234?callback=parseResponse">
</script>
```

JSONP works only for GET requests and is not effective for POSTs, PUTs, and DELETEs of a RESTful server.

JSON-P is an early and limited solution to cross-origin sharing. Fortunately, we have better options now. There are still APIs that use JSON-P, but it's being phased out by the industry in favor of __CORS__.

## CORS Security Model
CORS is a technique for relaxing the same-origin policy, which allows Javascript on the remote web application to consume a REST API served from a different origin.

* The CORS specification defines two distinct use cases:
  * __Simple requests__ = This use case applies if we use `HTTP` `GET`, `HEAD` and `POST` methods. In the case of `POST` methods, only content types with the following values are supported: `text/plain`, `application/x-www-form-urlencoded`, and `multipart/form-data`.
  * __Preflighted requests__ - When the ‘simple requests’ use case doesn’t apply, a first request (with the HTTP `OPTIONS` method) is made to check what can be done in the context of cross-domain requests.

If you add authentication to that request using the `Authentication` header, simple requests automatically become preflighted ones.

The client and server exchange headers to specify behavior regarding cross-domain requests. The following is a list of the specification on the header:
* `Origin`: this header is used by the client to specify which domain the request is executed from. The server uses this hint to authorize, or not, the cross-domain request.
* `Access-Control-Request-Method`: In the context of preflighted requests, the `OPTIONS` request sends this header to check if the target method is allowed in the context of cross-domain requests.
* `Access-Control-Request-Headers`: within the context of preflighted requests, the `OPTIONS` request sends this header to check if headers are allowed for the target method in the context of cross-domain requests.
* `Access-Control-Allow-Credentials`: this specifies if credentials are supported for cross-domain requests.
* `Access-Control-Allow-Methods`: the server uses this header to tell which headers are authorized in the context of the request. This is typically used in the context of preflighted requests.
* `Access-Control-Allow-Origin`: the server uses this header to tell which domains are authorized for the request.
* `Access-Control-Allow-Headers`: the server uses this header to tell which headers are authorized in the context of the request. This is typically used in the context of preflighted requests.

#### Simple request
In a simple request, the request is executed against the other domain. If the remote resource supports cross domains, the response is accepted. Otherwise, an error occurs

```
+--------------+                           +-------------+
|              |       GET (for example)   |             |
|    Client    +-------------------------> |    Server   |
|              |       Origin header       |             |
|              |                           |             |
|  Different   |                           |             |
|  domain from |                           |   Will      |
|  the server  |                           |   support   |
|              |                           |   CORS      |
|              |                           |             |
|              |       CORS headers        |             |
+--------------+ <-------------------------+-------------+
                         Response
```
Example Request:
```
GET /someData/ HTTP/1.1
Host: someDomain.org
(...)
Referer: http://mydomain.org/example.html
Origin: http://mydomain.org
```

Example Response:
```
HTTP/1.1 200 OK
(...)
Access-Control-Allow-Origin: *
Content-Type: application/json

[JSON Data]
```


#### Preflighted request
In the case of preflighted requests, this is a negotiation between the caller and the Web application based on HTTP headers that consists of two phases:
1. The browser executes an `OPTIONS` request with the same URL as the target request to check that it has the necessary permissions to execute the request.
2. This `OPTIONS` request then returns headers that identify what is possible to do for the URL. If rights/permissions match, the browser executes the request.

```
+---------------------+                   +--------------------+
|                     |   OPTIONS         |                    |
|      Client         +-----------------> |      Server        |
|                     |  Origin header    |                    |
|                     |                   |                    |
|                     |                   |                    |
|     Different       |  CORS headers     +    With support    |
|    domain from      +----------------->        of  CORS      |
|     the server      |    Response       +                    |
|                     |                   |                    |
|                     |                   |                    |
|                     |                   |                    |
|                     |                   |                    |
|                     |                   |                    |
|                     | POST (for example)|                    |
|                     +-----------------> |                    |
|                     |  Origin header    |                    |
|                     |                   |                    |
|                     |                   |                    |
|                     |                   |                    |
|                     |   CORS headers    |                    |
|                     | <-----------------+                    |
|                     |     Response      |                    |
|                     |                   |                    |
+---------------------+                   +--------------------+

```

#### Example exchange
```
OPTION /myresource/ HTTP/1.1
Host: mydomain.org
(...)
Origin: http://test.org
Access-Control-Request-Method: POST
Access-Control-Request-Headers: content-type,accept
```

The corresponding response:

```
HTTP/1.1 200 OK
(...)
Access-Control-Allow-Origin: http://test.org
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: content-type,accept
Access-Control-Max-Age: 1728000
```

Since the `OPTIONS` pre-request succeeds, the browser will then send the actual request:

```
POST /myresource/ HTTP/1.1
Host: mydomain.org
Content-type: application/json
Accept: application/json
(...)
Referer: http://test.org/example.html
Origin: http://test.org

[JSON Data]
```

The corresponding response:

```
HTTP/1.1 200 OK
(...)
Access-Control-Allow-Origin: *
Content-Type: application/json

[JSON Data]

```
[#Templier 2015](http://restlet.com/blog/2015/12/15/understanding-and-using-cors/)



As you can see, to enable cross-origin sharing, permissions set on the server will tell the browser what is allowed, and the browser then enforces those rules.

For Node, that code comes in the form of middleware before the api routes.

```javascript
app.use((req, res, next) => {
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

## Terms
* __CORS__ - _Cross-Origin Resource Sharing_. An HTML5 feature that allows one site to access another site's resources despite having a different domains (origin).
* __JSONP__ - _JSON with Padding_. Used to request data from a server residing in a different domain than the client. This enables sharing of data in spite of the same-origin policy.

## Resources

[Same-Origin Policy: MDN](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)

[Understanding CORS: Spring.io](https://spring.io/understanding/CORS)

[Understanding and using CORS: Restlet Blog](http://restlet.com/blog/2015/12/15/understanding-and-using-cors/)

[Understanding Cross-Origin Resource Sharing (CORS): Adobe Developer Connection](https://www.adobe.com/devnet/archive/html5/articles/understanding-cross-origin-resource-sharing-cors.html)

[Cross-Origin resource sharing: wikipedia](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing#How_CORS_works)

[Same-Origin Policy: Wikipedia](https://en.wikipedia.org/wiki/Same-origin_policy)

[JSONP: Wikipedia](https://en.wikipedia.org/wiki/JSONP)

[Security StackExchange: Why is the same origin policy so important](http://security.stackexchange.com/questions/8264/why-is-the-same-origin-policy-so-important)
