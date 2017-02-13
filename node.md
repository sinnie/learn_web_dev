# Node.js

[Node Docs](https://nodejs.org/en/)

[CORS](./node/cors.md)

[Socket.IO](./node/socketio.md)

## Introduction to Node
Node.js was created in 2009 by Ryan Dahl as an open-source, cross-platform JavaScript runtime environment for developing server tools and applications. Node uses Chrome's V8 engine to create an event-driven, _single-threaded_, _non-blocking_ I/O model that makes it lightweight and efficient. Node excels in real-time applications that run across distributed devices, and is useful for I/O based programs that need to be fast and/or handle lots of connections. Another benefit of Node is that it allows developers to use JavaScript, a language most web developers already know, to write programs (servers) that run directly on an operating system. Although Node has some powerful features, it must be noted, however, that Node.js is _not_ good for CPU intensive applications.

### Definition
#### So what exactly does _event-driven_, _non-blocking_, and _single-threaded_ mean? And what is I/O?
* __Non-Blocking__ - non-blocking code refers to operations that do not block further execution until that operation finishes, which means that your program will not hang on a process that has to complete. Instead, commands execute in parallel and use callbacks to signal completion or failure.
* __Single-Threaded__ - A thread of execution is the smallest sequence of programmed instructions that can be managed independently by a scheduler (a part of the OS). It's a kind of lightweight process that shares memory with every other thread within the same process. Threads were created as an ad hoc extension of the former model to accommodate concurrency [(Teixeira)](#Resources) In a single-threaded system, one command is processed at a time. Node.js' performance is improved by being single-threaded, which bypasses thread context switching. However, Node is not _truly_ single-threaded. It uses a library called libuv to handle multiple threads that are concerned with operating system tasks in the background.
  * A downside to this approach is that Node.j is not able to easily scale by increasing the number of CPU cores without using an additional module.   
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
The Node.js core modules are the heart of Node.js, and the Ndoe.js Bindings, which are written in C++, enable these technologies to communicate. The Node.js API consists of about 27 core modules, and are most concerned with operating system tasks. Therefore, Node.js core modules have access to the following functions:

```javascript
* fs.readFile();
* fs.writeFile();
* path.join();
* http.createServer();
* server.listen();
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

  > In Node.js, there are actually two separate kinds of events. There are __system events__, which are lower-level events that are handed by libuv, and __custom events__, which are handled by the JavaScript core (event emitter)

* JavaScript code sometimes wraps calls to the C++ side of Node. Often, when an event occurs in libuv, it generates a custom event to make it easier to manage our code and decide what code should run when that event happens. This makes it seem as though the system events and the custom events are the same thing. However,  they are not.  

Node.js implements an event-driven approach by attaching listeners to events. When those events fire, the listener executes the provided callback. Whenever you call `setTimeout`, `http.get`, or `fs.readFile`, Node.js sends these operations to a different thread, allowing V8 to keep executing the code. Node executes the callback when the counter has run down or the I/O operation/http operation has finished. Therefore, you can read a file while processing a request in your server, and then make an http call based on the read contents without blocking other requests from being handled.

Node.js only provides one thread and one call stack, so when another request is being served as a file is read, its callback will need to wait for the stack to become empty. The __task queue (event queue, or message queue)__ is the place where callbacks are waiting to be executed. Callbacks are called in an infinite loop whenever the main thread has finished its previous task.

To understand how this works, you must understand the __event loop__ and the __task queue__.

### The Event Loop
An event loop is a construct that performs two functions in a continuous loop: __event detection__ and __event handler triggering__. The event loop detects which events just happened as well as determining which event callback to invoke once an event has happened. The event loop is responsible for scheduling asynchronous operations and facilitates the event-driven programming paradigm in which the flow of the program is determined by events. In other words, it means that applications act on events. Node implements this by having a central mechanism, the `EventEmitter`, that listens for events and invokes a callback function once an event has been detected (i.e. state has changed).

#### The EventEmitter (JavaScript Events)
We're going to build our own. (Albeit a simple version)

```javascript

  function Emitter() {
    this.events = {}
  }

  Emitter.prototype.on = (type, listener) => {
    this.events[type] = this.events[type] || [];
    this.events[type].push(listener);
  }

  Emitter.prototype.emit = (type) => {
    if (this.events[type]) {
      this.events[type].forEach((listener) => {
        listener();
      });
    }
  }

```

Now, to use the Emitter:

```javascript

  const Emitter = require('./emitter');

  const emtr = new Emitter();

  emtr.on('greet', () => {
    console.log('Someone said hello');
  });

  emtr.on('greet', () => {
    console.log('A greeting occurred');
  });

  console.log('hello');
  emtr.emit('greet');

```


#### Event Loop Pseudocode
```
while there are still events to process:
    e = get the next event
    if there is a callback associated with e:
        call the callback
```


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


##### Remember:
* There is at most one event handler running at any given time.
* Any event handler will run to completion without being interrupted.



## Resources

[Haverbeke, Marijn. "Eloquent JavaScript: A Modern Introduction to Programming." _No Starch Press._](http://eloquentjavascript.net/20_node.html)

["maxogden." The Art of Node. "github.com/maxogden/art-of-node"](https://github.com/maxogden/art-of-node)

[Nodejs.org](https://nodejs.org/en/)

[Teixeira, Pedro. "Professional Node.js: Building JavaScript Based Scalable Software." _Safari Books_ ](https://www.amazon.com/Professional-Node-js-Building-Javascript-Scalable/dp/1118185463/ref=sr_1_1?ie=UTF8&qid=1486686418&sr=8-1&keywords=%5BProfessional+Node.js%3A+Building+JavaScript+Based+Scalable+Software%5D%28%29)

[Halliday, "substack" James. "The Stream Handbook" _github.com/substack/stream-handbook_](https://github.com/substack/stream-handbook#introduction)

[Understanding the Node.js Event Loop](https://nodesource.com/blog/understanding-the-nodejs-event-loop/)

[Raoof, Abdel. 'Introduction to Node.js.' _abdelraoof.com_](http://abdelraoof.com/blog/2015/10/19/introduction-to-nodejs/)

[Alicea, Anthony. _Udemy_. Learn and Understand NodeJS](https://www.udemy.com/understand-nodejs/learn/v4/overview)

[_Wikipedia_. 'Node.js'](http://www.wikiwand.com/en/Node.js)

Need to Summarize:

Thread pool - The main thread call functions post tasks to the shared task queue that threads in the thread pool pull and execute. Inherently non-blocking system functions like networking translates to kernel-side non-blocking sockets, while inherently blocking system functions like file I/O run in a blocking way on its own thread. When a thread in the thread pool completes a task, it informs the main thread of this that in turn wakes up and execute the registered callback. Since callbacks are handled in serial on the main thread, long lasting computations and other CPU-bound tasks will freeze the entire event-loop until completion.


Node.js registers itself with the operating system so that it is notified when a connection is made, and the operating system will issue a callback. Within the Node.js runtime, each connection is a small heap allocation. Traditionally, relatively heavyweight OS processes or threads handled each connection. Node.js uses an event loop for scalability, instead of processes or threads.[70] In contrast to other event-driven servers, Node.js's event loop does not need to be called explicitly. Instead callbacks are defined, and the server automatically enters the event loop at the end of the callback definition. Node.js exits the event loop when there are no further callbacks to be performed.
