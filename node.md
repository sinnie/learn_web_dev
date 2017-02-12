# Node.js

[Node Docs](https://nodejs.org/en/)

[CORS](./node/cors.md)

[Socket.IO](./node/socketio.md)

## Introduction to Node
Node.js was created in 2009 by Ryan Dahl as an open-source, cross-platform JavaScript runtime environment for developing a variety of server tools and applications. Node uses Chrome's V8 engine to create an event-driven, _single-threaded_, _non-blocking_ I/O model that makes it lightweight and efficient. Node excels in real-time applications that run across distributed devices, and is useful for I/O based programs that need to be fast and/or handle lots of connections. Another benefit of Node is that it allows developers to use JavaScript, a language they already know, to write programs that run directly on an operating system. Although Node has some powerful features, it must be noted, however, that Node.js is _not_ good for CPU intensive applications.

### Definition
#### So what exactly does _event-driven_, _non-blocking_, and _single-threaded_ mean? And what is I/O?
* __Non-Blocking__ - non-blocking code refers to operations that do not block further execution until that operation finishes, which means that your program will not hang on a process that has to complete.
* __Single-Threaded__ - A thread of execution is the smallest sequence of programmed instructions that can be managed independently by a scheduler (a part of the OS). In a single-threaded system, this means that one command is processed at a time. In the case of Node.js, the engine runs on a single thread; however, it uses a library called libuv to handle multiple threads that are concerned with operating system tasks in the background.
* __I/O__ - I/O is short for input/output and describes any program operation or device that transfers data to or from a peripheral device. Inputs are the signals or data received by a system and outputs are the signals or data sent from it.  
* You'll often hear __Asynchronous__ as well. Asynchronous means, in the case of Node.js, that the server can respond to multiple requests at a time. It will not stop or block any API requests and will respond to all when the response is ready to send accordingly.

> From a developer's point of view, Node.js is single-threaded, but under the hood, Node uses __libuv__ to handle __threading, file system events, implements the event loop, features thread pooling__ etc. In most cases, you won't interact with libuv directly, but you should be aware of it.

---

### Node Architecture
```
      +-------------------------------------------------------+
      |                                                       |
      |                       Node.js API                     |
      |                                                       |
      +------------------------------------+------------------+
      |                                    |                  |
      |         Node.js Bindings           |   C/C++ Addons   |
      |                                    |                  |
      +--------+--------+--------+---------+---------+--------+
      |        |        |        |   http  |   Open  |        |
      |  V8    | LibUv  | c-ares |  parser |   SSL   | zlib   |
      |        |        |        |         |         |        |
      +--------+--------+--------+---------+---------+--------+
```
* __V8__ is Google's open source JavaScript engine built for Google Chrome. It's written in C++ and can run either standalone or embedded into any C++ application.
* [libuv](http://nikhilm.github.io/uvbook/) is a multi-platform C library that provides support for asynchronous I/O based on event loops. It is used to abstract non-blocking I/O operations to a consistent interface across all supported platforms by providing mechanisms to handle file system, DNS, network, child processes, pipes, signal handling, polling and streaming. It also includes a thread pool for offloading work for some things that can't be done asynchronously at the operating system level. It supports epoll(4), kqueue(2), Windows IOCP, and Solaris event ports. And although It is primarily designed for use in Node.js, it is also used by other software projects.
  * It was originally an abstraction around libev or Microsoft IOCP, as libev doesn't support Windows. In node-v0.9.0's version of libuv, the dependency on libev was removed
  * __Features:__
    * Full-featured event loop backed by epoll, kqueue, IOCP, event ports.
    * Asynchronous TCP and UDP sockets
    * Asynchronous DNS resolution
    * Asynchronous file and file system operations
    * File system events
    * ANSI escape code controlled TTY
    * IPC with socket sharing, using Unix domain sockets or named pipes (Windows)
    * Child processes
    * Thread pool
    * Signal handling
    * High resolution clock
    * Threading and synchronization primitives
* __c-ares__ - a C library for async DNS request, including name resolves. It is intended for applications that need to perform DNS queries without blocking, or need to perform multiple DNS queries in parallel.
* __http_parser__ - This is a parser for HTTP messages written in C. It parses both requests and responses, and is designed to be used in performance HTTP applications. It does not make any syscalls nor allocations, it does not buffer data, it can be interrupted at anytime.
* __OpenSSL__: Is an open source implementation of Secure Sockets Layer (SSL v2/v3) and Transport Layer Security (TLS v1) protocols as well as a full-strength general purpose cryptography library. It is based on SSLeay library and built using C. It provides all the necessary cryptography methods like hash, hmac, cipher, decipher, sign and verify methods.
* __Zlib__: Is a general purpose data compression library written in C (creates and using zip files).

---

### Node Core
The Node.js API consists of about 27 core modules, which are concerned with operating system tasks, and, therefore, has access to the following functions:

```javascript
* fs.readFile()
* fs.writeFile()
* path.join()
* http.createServer()
* server.listen()
```

Tasks like `readFile` and `writeFile` are called _blocking_ because they take time to complete. They are much slower than operations that use a CPU. For example, during a hard disk operation that takes 10ms to perform, a 1 GHz CPU would have performed ten million instruction-processing cycles.

### Asynchronous Node
All API's of Node.js are _asynchronous or non-blocking_. This means that callbacks and promises are at the core of asynchronous Node.js. A simple definition of a callback is a function passed as an argument to another function.

Error-first callbacks are widely used in Node by the core modules as well as most of the modules found on [npm](https://www.npmjs.com/).

##### Note
* __error-handling__: instead of a `try-catch` block you have to check for
errors in the callback
* __no return value__: async functions do not return values; however, values will be passed to the callbacks

Async actions are completed through callbacks and __The Event Loop__.

### Event Driven Programming
__Event-driven programming__ is a programming paradigm in which the flow of the program is dictated by events (changes in state), including user actions, sensor outputs, or messages from other programs or threads. In this paradigm, every command is event based, which means that the program revolves around a loop that polls for input or data (or state change). When an event occurs, a callback is invoked.

Although V8 is single-threaded, the underlying C++ API of Node is not, which means that whenever we call something that is a non-blocking operation, Node will call __libuv__ to run code concurrently with our javascript code under the hood. Once this thread (form _libuv_) receives the value, it awaits for or throws an error, and then the provided callback is called with the necessary parameters.

  > In Node.js, there are actually two separate kinds of events:

    >> System events - libuv (lower-level close to the machine)

    >> Custom events - JavaScript Core  - Event Emitter

* JavaScript code sometimes wraps calls to the C++ side of Node. Often, when an event occurs in libuv, it generates a custom event to make it easier to manage our code and decide what code should run when that event happens. This makes it seem as though the system events and the custom events are the same thing. They are not.  

To understand how this works, you must understand the event loop and the task queue.

Node.js implements an event-driven approach by attaching listeners to events. When those events fire, the listener executes the provided callback. Whenever you call `setTimeout`, `http.get`, or `fs.readFile`, Node.js sends these operations to a different thread, allowing V8 to keep executing the code. Node executes the callback when the counter has run down or the I/O operation/http operation has finished. Therefore, you can read a file while processing a request in your server, and then make an http call based on the read contents without blocking other requests from being handled.

Node.js only provides one thread and one call stack, so when another request is being served as a file is read, its callback will need to wait for the stack to become empty. The __task queue (event queue, or message queue)__ is the place where callbacks are waiting to be executed. Callbacks are called in an infinite loop whenever the main thread has finished its previous task.

#### Microtasks and Macrotasks
Node actually has more than one task queue. It has one for microtasks and one for macrotasks.

Microtasks:
* `process.nextTick`
* `promises`
* `Object.observe`
Macrotasks:
* `setTimeout`
* `setInterval`
* `setImmediate`
* `I/O`

### The Event Loop
An event loop is a construct that performs two functions in a continuous loop: event detection and event handler triggering. The event loop detects which events just happened as well as determining which event callback to invoke once an event has happened. The event loop is responsible for scheduling asynchronous operations and facilitates the event-driven programming paradigm in which the flow of the program is determined by events such as user actions (mouse clicks, key presses), sensor outputs, or messages from other programs/threads. In other words, it means that applications act on events. Node implements this by having a central mechanism, the `EventEmitter`, that listens for events and calls a callback function once an event has been detected (i.e. state has changed).

#### Event Loop Pseudocode
```
while there are still events to process:
    e = get the next event
    if there is a callback associated with e:
        call the callback
```

##### Remember:
* There is at most one event handler running at any given time.
* Any event handler will run to completion without being interrupted.



## Resources

[Eloquent JavaScript](http://eloquentjavascript.net/20_node.html)

[The Art of Node](https://github.com/maxogden/art-of-node)

[Nodejs.org](https://nodejs.org/en/)

[Professional Node.js: Building JavaScript Based Scalable Software](https://www.amazon.com/Professional-Node-js-Building-Javascript-Scalable/dp/1118185463/ref=sr_1_1?ie=UTF8&qid=1486686418&sr=8-1&keywords=%5BProfessional+Node.js%3A+Building+JavaScript+Based+Scalable+Software%5D%28%29)

[The Stream Handbook](https://github.com/substack/stream-handbook#introduction)

[Understanding the Node.js Event Loop](https://nodesource.com/blog/understanding-the-nodejs-event-loop/)

[Introduction to Node.js by Abdel Raoof](http://abdelraoof.com/blog/2015/10/19/introduction-to-nodejs/)

[Alicea, Anthony. _Udemy_. Learn and Understand NodeJS](https://www.udemy.com/understand-nodejs/learn/v4/overview)
