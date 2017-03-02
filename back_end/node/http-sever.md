## HTTP Webserver

### TCP / IP

#### Terms:

* __protocol__ - standards for communication. Both the client and the server are programmed to understand and use that particular set of rules. It's similar to two people from different countries agreeing on a standard language.
* __TCP__ - Protocol that defines how to sent that information. Transmission Control Protocol. Whatever format it is in, TCP splits information in packets through the socket from computer to computer. The information that it is sending may be in a protocol, but TCP is only concerned with sending that information in packets.
  * Similar to a stream. Thus, node treats these packets as a stream.
* __IP__ - Internet Protocol - agreed upon sequence of numbers that identifies computers that communicate with each other.
* __HTTP__ - Hypertext transfer protocol. Format for data being transferred via TCP/IP. core of sending information on the web.
* __Socket__ -
* __Port__: Once a computer receives a packet, how it knows what program to send it to. When a program is setup on the OS to receive packets from a particular port, it is said that the program is listening to that port.
* __MIME type__ - Multi purpose internet mail extensions. It is a standard for specifying the type of Data being sent. (application/json, text/html, img/jpeg, etc.)

### Request Response Cycle: Client Server Model
```bash
+-------------------+       Standard Format           +-------------------+
|     IP            |+------------------------------> |     IP            |
| 209.85.128.0      |           Request               | 74.125.224.72     |
|                   |                                 |                   |
|     Browser       |  <-- Socket --|http, ftp...|--->|  Server/Node.js   |
|     Ask for       |                                 |     perform       |
|     services      |                                 |     services      |
|                   |       Standard Format           |                   |
|                   |<------------------------------+ |  Node.js: port:80 | <-- port number/socket
+-------------------+          Response               +-------------------+

```

## An HTTP request is composed of the following parts:

1. A method (or verb)
2. A path
3. An HTTP version
4. Key-value headers
5. And an optional body

### An example of what an HTTP request:
```
GET / HTTP/1.1
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Host: localhost:8000
User-Agent: HTTPie/0.9.3
```

__An HTTP response is composed of the following parts:__

1. An HTTP version
2. A status code
3. Key-value Headers
4. And an optional Body

### An example of what an HTTP response:
```
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 11
Content-Type: text/plain
Date: Mon, 13 Jun 2016 04:28:36 GMT
Hello world
```

## Initializing a server:
A Node server is created with one callback. For each HTTP request that arrives, the callback is invoked with two arguments: res, req
* The callback’s first req argument will contain the incoming HTTP request as an `http.IncomngMessage` object
* the callbacks second argument will contain an empty outgoing http response as an `http.ServerResponse` obj
* The goal of the callback is to correctly fill in the res obj based on the information in req object

```javascript
const http = require('http');
const port = process.env.PORT || 8000;

const server = http.createServer((req, res) => { //<— on every request/response interaction with the server will so something
  res.setHeader('Content-Type', 'text/plain'); // <— defines how we are sending data to the client
  res.end('Hello world'); // <— end fn ends the implements the stream; grab the buffer data and send data
});

server.listen(port, () => {
  console.log(`Listening on port ${port}`);
});
```

```js
fs.readFile() //<— open the file that is on the path the server receives.

'use strict';

const fs = require('fs');

fs.readFile('/etc/paths', 'utf8', (err, data) => { // <— callback (error, file contents)
      if (err) throw err; // <— if the file doesn’t exist
      console.log(data);
});

console.log(1 + 2);
```

__How do you create a Node.js HTTP server with the http module?__

  ```js
    'use strict';

    const http = require('http');
    const port = process.env.PORT || 8000;

    const server = http.createServer((req, res) => {
    const guests = ['Mary', 'Don'];
    const guestsJSON = JSON.stringify(guests);

      res.setHeader('Content-Type', 'application/json');
      res.end(guestsJSON);
    });

    server.listen(port, () => {
      console.log(`Listening on port ${port}`);
    });
  ```

A Node.js HTTP server is created with one callback.
* For each HTTP request that arrives, the callback is invoked with two arguments —- request and response.
    * The callback's first req argument will contain the incoming HTTP request as an `http.IncomingMessage` object.
    *  The callback's second response argument will contain an empty outgoing HTTP response as an http.ServerResponse object. The goal of the callback is to correctly fill in the res object based on the information in req object.

```js
    'use strict';

    const fs = require('fs');
    const path = require('path');
    const guestsPath = path.join(__dirname, 'guests.json');

    const http = require('http');
    const port = process.env.PORT || 8000;

    const server = http.createServer((req, res) => {
      if (req.method === 'GET' && req.url === '/guests') {
        fs.readFile(guestsPath, 'utf8', (err, guestsJSON) => {
          if (err) {
            console.error(err.stack);
            res.statusCode = 500;
            res.setHeader('Content-Type', 'text/plain');
            return res.end('Internal Server Error');
          }

          res.setHeader('Content-Type', 'application/json');
          res.end(guestsJSON);
        });
      } else if (req.method === 'GET' && req.url === '/guests/0') {
        fs.readFile(guestsPath, 'utf8', (err, guestsJSON) => {
          if (err) {
            console.error(err.stack);
            res.statusCode = 500;
            res.setHeader('Content-Type', 'text/plain');
            return res.end('Internal Server Error');
          }

          const guests = JSON.parse(guestsJSON);
          const guestJSON = JSON.stringify(guests[0]);

          res.setHeader('Content-Type', 'application/json');
          res.end(guestJSON);
        });
      } else if (req.method === 'GET' && req.url === '/guests/1') {
        fs.readFile(guestsPath, 'utf8', (err, guestsJSON) => {
          if (err) {
            console.error(err.stack);
            res.statusCode = 500;
            res.setHeader('Content-Type', 'text/plain');
            return res.end('Internal Server Error');
          }

          const guests = JSON.parse(guestsJSON);
          const guestJSON = JSON.stringify(guests[1]);

          res.setHeader('Content-Type', 'application/json');
          res.end(guestJSON);
        });
      } else {
        res.statusCode = 404;
        res.setHeader('Content-Type', 'text/plain');
        res.end('Not found');
      }
    });

    server.listen(port, () => {
      console.log(`Listening on port ${port}`);
    });
```
