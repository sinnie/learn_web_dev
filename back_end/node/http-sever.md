## HTTP Webserver

### Terms:

* [__Protocol__](https://www.wikiwand.com/en/Protocol) - a defined set of rules and regulations that determine how data is transmitted in telecommunications and computer networking. Both the client and the server are programmed to understand and use that particular set of rules. It's similar to two people from different countries agreeing on a standard language.
* [__TCP__](https://www.wikiwand.com/en/Transmission_Control_Protocol) - Transmission Control Protocol is one of the main protocols of the internet protocol suite. TCP provides reliable, ordered, and error checked delivery of a stream of octets between applications running on hosts communicating by an IP network. that defines how to sent that information. Transmission Control Protocol accepts data from a data stream, divides it into chunks, and adds a TCP header creating a TCP segment. The TCP segment is then encapsulated into an Internet Protocol (IP) datagram, and exchanged with peers via sockets.
* [__IP__](https://www.wikiwand.com/en/Internet_Protocol) - Internet Protocol is the principal communications protocol in the Internet protocol suite for relaying data across network boundaries. IP delivers packets from the source host to the destination host based on the IP addresses in the packet headers. IP defines packet structures that encapsulate the data to be delivered. It also defines addressing methods that are used to label the data with source and destination information.
* [__HTTP__](https://www.wikiwand.com/en/Hypertext_Transfer_Protocols) - The Hypertext Transfer Protocol is an application protocol for distributed, collaborative, and hypermedia information systems. It is the format for data being transferred via TCP/IP and is the core of sending information on the web. HTTP functions as a request-response protocol in the client-server computing model.
* [__Network Socket__](https://www.wikiwand.com/en/Network_socket) - A network socket is an internal endpoint for sending or receiving data at a single node in a computer network. It is a representation of this endpoint in the networking software (protocol stack), such as an entry in a table (listing communication protocol, destination, status, etc.), and is a form of system resource.
* [__Port__](https://www.wikiwand.com/en/Port_(computer_networking)) - In the internet protocol suite, a port is an endpoint of communication in an operating system. It is a logical construct that identifies a specific process or a type of network service. A port is always associated with an IP address of a host and the protocol type of the communication. Thus, it completes the destination or origination network address of a communication session.
* [__MIME type__](https://www.wikiwand.com/en/Media_type) - Multipurpose Internet Mail Extensions is a two-part identifier for file formats and format contents transmitted on the internet and is a standard for specifying the type of Data being sent. Some examples include :application/json, text/html, img/jpeg, etc..

## Request Response Cycle (Client Server Model)
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

## Request
### An HTTP request is composed of the following parts:
1. A method (or verb)
2. A path
3. An HTTP version
4. Key-value headers
5. And an optional body

#### An example of what an HTTP request:
```
GET / HTTP/1.1
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Host: localhost:8000
User-Agent: HTTPie/0.9.3
```
---

## Response
### An HTTP response is composed of the following parts:
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

---

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
