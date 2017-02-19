# File System and Path Modules

## Intro
In this section, we are going to explores the File System. Before we get into some examples, however, there are a few bases to cover. We'll briefly discuss the File System, the Path Module, Buffers, and the global Process Module. And I'll try to describe the relevance of every section.

## File System
File I/O is provided by wrappers around standard POSIX (Portable Operating System Interface - a standards specified by IEEE for maintaining compatibility between OSs) functions. All methods have asynchronous and synchronous forms. The asynchronous form takes a completion callback as its last argument. The arguments passed to the completion callbacks depend on the particular method used. The first argument to any method is always reserved for an exception. If the operation was completed successfully, then the first argument will be `null` or `undefined`.

If you are using the synchronous form, any exceptions will immediately be thrown. Use try/catch to handle exceptions or allow them to bubble up.

__Asynchronous FS Method:__
```javascript
const fs = require('fs');

fs.unlink('/tmp/hello', (err) => {
  if (err) throw err;

  console.log('successfully deleted /tmp/hello');
});
```

__Synchronous FS Method:__
```javascript
const fs = require('fs');

fs.unlink('/tmp/hello');
console.log('successfully deleted /tmp/hello');
```

> There is no guaranteed ordering with asynchronous methods. Therefore, when completion order matters, chain callbacks. In addition, it is important to use asynchronous methods so that your program will not block.  

## Path Module:
A collection of utilities that allow developers to work with file and directory paths. The path module does not perform any I/O operations. i.e. it doesn’t consult the filesystem to see whether or not the path is valid. This module contains several helper functions to make path manipulations easier.
* The default operation of the path module varies based on the operating system on which a Node.js application is running. In other words, when Node.js is on a Windows machine, the `path` module assumes that Windows-style paths are being used. This is indispensable when building cross-platform applications.

### path.join([...paths])
This function takes a variable number of arguments, joins them together, and normalizes the path.

__Arguments:__
* `...paths` `<String>` <-- A sequence of Path Segments
  * If any of the path segments are not a string, a  `TypeError` is thrown.

__Return Value:__
* `<String>`

The `path.join()` method joins all given `path` segments using the platform specific separator as a delimiter before normalizing the resulting path. A common use of `join` is to manipulate paths when serving urls.

Zero-length __path__ segments are ignored. If the joined path string is a zero-length string, then `'.'` will be returned.

#### Example:
  ```javascript
  const path = require('path');

  path.join('/a/.', './//b/', 'd/../c/')

   // => a/b/c
  ```
## Process Module
Each Node.js process has built-in functionality that can be accessed through the global `process` module. This `process` module does not have to be required because it is a 'wrapper' around the currently executing process, and many of the methods it exposes are actually wrappers around calls into some of Node.js' core C libraries. There are several methods made available through the process object, including:
1. exit
2. beforeExit
3. uncaughtException
4. Signal Events

## process.argv
* property that returns an `<Array>` containing the command line arguments passed when the Node.js process was launched.
    * first element = `process.execPath`
    * second element = path to the JS file being executed
    * remaining elements = any additional command line arguments
For Example, assuming the following script for `process-args.js`:
  ```javascript
  // print process.argv

  process.argv.forEach((val, index) => console.log(`${index}: ${val}`);
  ``
Would generate:
  ```javascript
  0: /usr/local/bin/node
  1: /Users/mjr/work/node/process-2.js
  2: one
  3: two=three
  4: four
  ```
## File I/0
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

## Buffers:
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
      if (err) throw err; // <— if the file doesn’t exist
      console.log(data);
});

console.log(1 + 2);
```
* `fs.writeFile()`
* `path.join()`
* `http.createServer()`
* `server.listen()`



### Here's an example of what an HTTP request looks like.
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
3. Key-value headers
4. And an optional body

### Here's an example of what an HTTP response looks like.
```
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 11
Content-Type: text/plain
Date: Mon, 13 Jun 2016 04:28:36 GMT
Hello world
```
__How do you create a Node.js HTTP server with the http module?__
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
* For each HTTP request that arrives, the callback is invoked with two arguments —- request and response.
    * The callback's first req argument will contain the incoming HTTP request as an `http.IncomingMessage` object.
    *  The callback's second response argument will contain an empty outgoing HTTP response as an http.ServerResponse object. The goal of the callback is to correctly fill in the res object based on the information in req object.

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

[Node Docs: Path](https://nodejs.org/api/path.html)
