# Node.js

[Node Docs](https://nodejs.org/en/)

## Introduction to Node
Node.js was created in 2009 by Ryan Dahl. It is an open-source, cross-platform JavaScript runtime environment for developing a variety of server tools and applications. Node uses Chrome's V8 engine to create an event-driven, _single-threaded_, _non-blocking_ I/O model that makes it lightweight and efficient. Node excels in data-intensive, real-time applications that run across distributed devices, and is useful for I/O based programs that need to be fast and/or handle lots of connections. In short, Node allows developers to write JavaScript programs that run directly on an operating system. That being said, Node.js is not good for CPU intensive applications.

From a developer's point of view, Node.js is single-threaded, but under the hood, __libuv__ handles __threading, file system events, implements the event loop, features thread pooling__ etc. In most cases, you won't interact with libuv directly, but you should be aware of it.

JavaScript outside of the browser is concerned with operating system tasks, and, therefore, has access to the following functions:

```javascript
* fs.readFile()
* fs.writeFile()
* path.join()
* http.createServer()
* server.listen()
```

Tp specify verison use: `nvm use 4` or `nvm use 5`.

### Asynchronous Node
All API's of Node.js are asynchronous or non-blocking. This means that callbacks are at the core of asynchronous JavaScript and Node.js. A simple definition of a callback is one functions passed as an argument to other functions.

Error-first callbacks are widely used in Node by the core modules as well as most of the modules found on [npm](https://www.npmjs.com/).

##### Note
* error-handling: instead of a `try-catch` block you have to check for
errors in the callback
* no return value: async functions don’t return values, but values will
be passed to the callbacks

Async actions are completed through callbacks and __The Event Loop__.

### Event Driven Programming
Although V8 is single-threaded, the underlying C++ API of Node is not, which means that whenever we call something that is a non-blocking operation, Node will call __libuv__ to run code concurrently with our javascript code under the hood. Once this hiding thread (form _libuv_) receives the value, it awaits for or throws an error, the provided callback will be called with the necessary parameters.

To understand how this works, you must understand the event loop and the task queue.

### The Event Loop
The event loop is responsible for scheduling asynchronous operations and facilitates the event-driven programming paradigm in which the flow of the program is determined by events such as user actions (mouse clicks, key presses), sensor outputs, or messages from other programs/threads. In other words, it means that applications act on events. Node implements this by having a central mechanism, the `EventEmitter`, that listens for events and calls a callback function once an event has been detected (i.e. state has changed).

## Modules
Node.js puts little functionality in the global scope because it is organized into __modules__. Modules are a collection of functions that can be imported into a file using the `require()` function, which allows one to load built-in modules, dowloaded libraries, or files that are a part of one's program.

When `require()` is called, Node has to resolve the given string to an actual file to load. Therefore, local modules must begin with a relative or absolute path. In contrast, built-in modules or libraries installed in a `node_modules` library can be referred to by module name. For example, `const fs = require('fs')`, will give you Node's file system module. Whereas `const johnnyFive = require(./path/to/module/'my-module')` will include a local module. In either case, it is safe to omit the file extension.

* use `require` to include modules and `exports` to make them available elsewhere.

### Patterns for exporting modules
1. Export an anonymous Function
2. Export a named Function
3. Export an anonymous Object
4. Export a named Object
5. Export an anonymous Prototype
6. Export a named Prototype

#### Pros and cons
* Named Exports - one module, many exported things
* Anonymous Exports - Simplified client interface

### Core Modules
Node has a small group of modules that are presented as the public API. These core modules enable the creation of programs that can quickly communicate with filesystems or networks.

#### fs Module

### Path Module:
* A collection of utilities that don’t perform any I/O operations. i.e. it doesn’t consult the filesystem to see whether or not the path is valid.

#### process.argv
* property that returns an array containing the command line arguments passed when the Node.js process was launched.
    * first element = process.execPath
    * second element = path to the JS file being executed
    * remaining elements = any additional command line arguments

#### File I/0
File I/O is provided by simple wrappers around standard POSIX functions.
* `require(‘fs’);`
* Async form takes a completion callback as last arg. The arguments passed to the callback depend on the method, but the first argument is always reserved for an exception. If the operation was completed successfully, then the first argument will be null or undefined.

```javascript
fs.rename('/tmp/hello', '/tmp/world', (err) => {
  if (err) throw err;
  fs.stat('/tmp/world', (err, stats) => {
    if (err) throw err;
    console.log(`stats: ${JSON.stringify(stats)}`);
  });
});
```
* I/O can also perform with devices:
    * files
    * signals
    * pipes
    * sockets

Three Ways to Export Objects:
1. assign a new object to the module.exports property
2. Because module.exports is an object by default, the second way is to assign a value directly to one of its properties
3. Because exports as a shorthand for module.exports, the third way is to assign a value directly to one of its properties

An HTTP request is composed of the following parts.
1. A method (or verb)
2. A path
3. An HTTP version
4. Key-value headers
5. And an optional body

## Initializing a server:
A Node server is created with one callback. For each HTTP request that arrives, the callback is invoked with two args - res, req
* The callback’s first req argument will contain the incoming HTTP request as an http.IncomngMessage object
* the callbacks second argument will contain an empty outgoing http response as an http.ServerResponse obj
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

On Buffers:
* JS deals only in strings. It doesn’t have a way to deal with bytes
* it handles binary-handling tasks with a binary buffer implementation, which is exposed as a JS API under the buffer pseudo-class

Node.js Core Modules:
* const fs = require('fs’); <— Allows a JS programs to access the filesystem
* const url = require(‘url); <— parse, interpret and manipulate urls
* const http = require('http');
* const path = require('path’);

## How do you use the fs module to interact with the filesystem?
* The fs module is the built-in Node.js API for managing a computer's filesystem.
* Each operation has both an asynchronous and synchronous method (e.g.readFile and readFileSync).

Node Programs have access to the following functions:

```javascript
fs.readFile() //<— open the file that is on the path the server receives.

'use strict';

const fs = require('fs');

fs.readFile('/etc/paths', 'utf8', (err, data) => { // <— callback (error, file contents)
      if (err) throw err; <— if the file doesn’t exist
      console.log(data);
});

console.log(1 + 2);
```
* fs.writeFile()
* path.join()
* http.createServer()
* server.listen()



### Here's an example of what an HTTP request looks like.

GET / HTTP/1.1
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Host: localhost:8000
User-Agent: HTTPie/0.9.3

An HTTP response is composed of the following parts.
1. An HTTP version
2. A status code
3. Key-value headers
4. And an optional body

### Here's an example of what an HTTP response looks like.

HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 11
Content-Type: text/plain
Date: Mon, 13 Jun 2016 04:28:36 GMT
Hello world

How do you create a Node.js HTTP server with the http module?
```javascript
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
* For each HTTP request that arrives, the callback is invoked with two arguments—req and res.
    * The callback's first req argument will contain the incoming HTTP request as anhttp.IncomingMessage object.
    *  The callback's second res argument will contain an empty outgoing HTTP response as an http.ServerResponse object. The goal of the callback is to correctly fill in the res object based on the information in req object.

```javascript
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


## Resources

[Eloquent JavaScript](http://eloquentjavascript.net/20_node.html)

[The Art of Node](https://github.com/maxogden/art-of-node)

[Nodejs.org](https://nodejs.org/en/)

[Professional Node.js: Building JavaScript Based Scalable Software](https://www.amazon.com/Professional-Node-js-Building-Javascript-Scalable/dp/1118185463/ref=sr_1_1?ie=UTF8&qid=1486686418&sr=8-1&keywords=%5BProfessional+Node.js%3A+Building+JavaScript+Based+Scalable+Software%5D%28%29)

[The Stream Handbook](https://github.com/substack/stream-handbook#introduction)

[Understanding the Node.js Event Loop](https://nodesource.com/blog/understanding-the-nodejs-event-loop/)


JS
single-threaded
non-blocking
asynchronous
concurrent Languages

JS has:
Call stack <-- a data structure that tracks the execution context
* one thread == one call stack == one thing at a time
* returning things pops them off of the stack


event loop
callback queue
other APIs
and stuff

v8 has
callstack
heap - where memory allocation happens
