# Modules and the File System

[Modules Docs](https://nodejs.org/api/modules.html)

[File System Docs](https://nodejs.org/api/fs.html)

## Node.js Module
Node.js puts little functionality in the global scope because it is organized into __modules__. Modules are a collection of functions that can be imported into a file using the `require()` function, which allows developers to load built-in modules, dowloaded libraries, or files that are a part of one's program. Node.js supports modular code in the form of _Core modules_, _File modules_, and _NPM modules_. This allows for more maintainable code and a robust ecosystem of open source software that developers can import via NPM. To reiterate, a __module__ is simply a file that encapsulates related JavaScript code. This code can then be reused in different parts of a program.

In Node.js, the `module`  __is a global variable with an `exports` property that references an empty object by default.__ You can export objects or functions using the `module.export` object and require them into another section of you code with the `require()` function, which accepts a module id as an argument. When invoking `require()`, assign the required object to a variable to access its properties. `require()` returns a reference to the value of `module.exports`.

__Pseudo-code example of how a module is defined:__

    ```
      const theModule = {
        exports: {}
      };

      (function(module, exports, require) {

        // module code here

        })(theModule, theModule.exports, theRequireFunction);
    ```

> In each module, the `module` free variable is a reference to the object representing the current module. For convenience,  `module.exports` is also accessible via the `exports` module-global. `module` is not actually a global, but local to each module.

When `require()` is called, Node has to resolve the given string to an actual file to load. Therefore, local modules must begin with a relative or absolute path. In contrast, built-in modules or libraries installed in a `node_modules` library can be referred to by module name. For example, `const fs = require('fs')`, will give you Node's file system module. Whereas `const johnnyFive = require(./path/to/module/'my-module')` will include a local module. In either case, it is safe to omit the file extension.

#### There are three kinds of Modules:
1. Core Modules
1. NPM Modules
1. File Modules

## Core Modules
Core modules are built-in to Node.js and represent the public API. These modules include `fs`, `http`, and `path` and are required by their name only as the argument to `require`. The core modules are defined within Node.js' source and are located in the lib/ folder. These core modules enable the creation of programs that can quickly communicate with filesystems or networks. It is useful to know that core modules are preferentially loaded if their identifier is passed to `require()`. Meaning, `require('http')` will always return the built-in HTTP, even if there is another file by that name.

## NPM Modules
NPM modules include code that can be found in the NPM registry. Developers can download NPM modules with the `npm install` command. These modules are required in an identical fashion as the core modules, meaning they can be referred to by id, without an extension or path.

## File Modules
File modules are modules that a developer has created as a part of his/her own program. If the exact filename of a core module is not found when imported into the program, Node.js will attempt to load the required filename with the added extensions: `.js` (JavaScript text files), `.json` (JSON text files), and `.node` (compiled add-on modules loaded with `dlopen`).

> Again: without a leading '` / `', '` ./ `', or '` ../ `' to indicate a file, the module must either be a core module or loaded from the `node_modules` folder (an NPM package).

## Patterns for exporting modules:
1. Export an __Anonymous or Named Function__
2. Export an __Anonymous or Named Object__
3. Export an __Anonymous or Named Prototype__

#### Pros and cons
* __Named Exports__ - Since you have one module, you can have many exported methods/functionality
* __Anonymous Exports__ - An Anonymous export provides a simplified client interface

## Exporting a function
Functions can be exported by assigning the function to the `module.exports` object. Exported functions can be invoked immediately.

```javascript
  module.exports = () => console.log('You can export a function');
```

## Exporting an Object
You can assign the object to the `module.exports` object.

## Three Ways to Export Objects:
1. __Assign a new object to the module.exports property__

  ```javascript
    module.exports = {
      bestFunk: () => console.log('Earth, Wind & Fire');
    }
  ```

2. Because module.exports is an object by default, the second way is to assign a value directly to one of its properties.

  ```javascript
  module.exports.anotherFunk = () => console.log('Kool & The Gang');
  ```

3. Because exports is a shorthand for module.exports, the third way is to assign a value directly to one of its properties

  ```javascript
  exports.yetAnotherFunk = () => console.log('Parliament-Funkadelic');
  ```

---

## Node.js node_modules Folder
The node_modules folder in a Node.js application usually contains the modules used by that application. A module in this directory can also have a node_modules folder containing modules which are required by that module.

## Node.js Module Caching
Modules are cached after the first time they are loaded, which results in typical behavior. In other words, every call to `require(obj)` will return the same object. An object is returned when a module is required for the first time. This object is cached and is returned whenever a `require()` function resolves to the same path.

## Clarity on module.exports vs exports
Initially, `module.exports` and `exports` are references to the same (empty) object. However, only `module.exports` will be returned by `require()`.

Therefore, when you do something like this,
  ```javascript
  exports.a = function() {
      console.log("a");
  }
  exports.b = function() {
      console.log("b");
  }
  ```

You are adding two functions, 'a' and 'b', to the object that module.exports points to. Here, you have an `object : { a: [Function], b: [Function] }`. And you will get the same result if you use `module.exports` instead of simply `exports`. Here, the `module.exports` acts like a container of exported values. But

__what if you were trying to export a constructor function?__
    ```javascript
    module.exports = Something() => {
        console.log('I do something.');
    }
    ```

This can now be immediately invoked and `typeof` returns `'function'` because this overwrites the return result to be a function.

#### Remember:
1. `require()` returns an object that references the value of module.exports
  * In other words, `module.exports` is the object that is actually returned as the result of `require`.
  * Initially, the `exports` variable is set to that object. Thus:
2. Avoid re-assigning `exports` to anything other than it's default value as it will overwrite that default (`module.exports`). This happens because the reference will not point to the object that `module.exports` points.

# fs Module

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
