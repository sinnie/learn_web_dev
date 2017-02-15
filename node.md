# Node.js

[Node Docs](https://nodejs.org/en/)

[Learn Web Dev](./README.md)

[CORS](./node/cors.md)

[Socket.IO](./node/socketio.md)

## Introduction to Node
Node.js was created in 2009 by Ryan Dahl as an open-source, cross-platform JavaScript runtime environment for developing server tools and applications. Node uses Chrome's V8 engine to create an _event-driven_, _single-threaded_ (sorta), _non-blocking_ I/O model that makes it lightweight and efficient. Node excels in real-time applications that run across distributed devices, and is useful for I/O based programs that need to be fast and/or handle lots of connections. Another benefit of Node is that it allows developers to use JavaScript, a language most web developers already know, to write programs (servers) that run directly on operating systems. Although Node has some powerful features, it should be avoided when working with CPU intensive applications.

### Terms
#### So what exactly does _event-driven_, _non-blocking_, and _single-threaded_ mean? And what is I/O?
* __Event-Driven__ - Event driven programming is when the application flow control is determined by events or changes in state. It generally has a central mechanism that listens for events and invokes a callback function once an event has been detected. In the case of Node.js, this is the event loop.
* __Non-Blocking__ - non-blocking code refers to operations that __do not block__ further execution until that operation finishes, which means that your program will not hang on a process that has to complete. Instead, commands execute in parallel and use callbacks to signal completion or failure.
* __Single-Threaded__ - A __thread of execution__ is the smallest sequence of programmed instructions that can be managed independently by a scheduler (a part of the OS). It is a kind of lightweight process (an independent execution unit with its own memory area) that shares memory with every other thread within the same process. Threads were created as an ad hoc extension of the former model to accommodate concurrency [(Teixeira)](#resources). In a single-threaded system, one command is processed at a time. Node.js' performance is attributed to being single-threaded, which bypasses thread context switching. However, Node is not _truly_ single-threaded. In the background, Node.js uses a library called libuv to handle multiple threads that manage I/O tasks related to the operating system.
  * A downside to being single-threaded is that Node.js is not able to easily scale by increasing the number of CPU cores.
* __I/O__ - I/O is short for input/output and describes any program operation or device that transfers data to or from a peripheral device. Inputs are the signals or data received by a system and outputs are the signals or data sent from it.  
* You'll often hear __Asynchronous__ as well. Asynchronous means, in the case of Node.js, that the server can respond to multiple requests at a time. It will not stop or block any API requests and will respond to all when the response is ready to send accordingly. Working asynchronously allows you to start processing data that does not require the result of the communication while the communication goes on.

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
  * libuv has a default thread pool size of 4 and uses a queue to manage access to the thread pool.
    * You can mitigate this by increasing the size of the thread pool through the `UV_THREADPOOL_SIZE` environment variable, as long as the thread pool is required and created: `process.env.UV_THREADPOOL_SIZE = 10`  
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
The Node.js core modules are the heart of Node.js, and the Node.js Bindings, which are written in C++, enable these technologies to communicate with the C/C++ core. The Node.js API consists of about 27 core modules and are most concerned with operating system tasks. Therefore, Node.js core modules have access to the following functions:

```javascript
* fs.readFile();
* fs.writeFile();
* path.join();
* http.createServer();
* server.listen();
```

Tasks like `readFile` and `writeFile` are called _blocking_ because they take time to complete. They are much slower than operations that use a CPU. For example, during a hard disk operation that takes 10ms to perform, a 1 GHz CPU would have performed ten million instruction-processing cycles.

### Asynchronous Node
All APIs of Node.js are _asynchronous or non-blocking_. This means that callbacks and promises are at the core of asynchronous Node.js. A simple definition of a callback is a function passed as an argument to another function, and [more information about promises can be found here](./promises.md).

A common pattern of event-driven programming is succeed or fail. There are two common implementations of that pattern in Node.js. The first is the Error-first callbacks, which are widely used in Node by the core modules as well as most of the modules found on [npm](https://www.npmjs.com/). Although error-first callbacks sound complicated, they are actually quite simple. An error-first callback is simply a callback that accepts an error object as the first argument. If there is no error, the first argument is `null`. The second pattern uses [promises]('./promises.md'), which is an object used for asynchronous computations.

##### Note
* __error-handling__: instead of a `try-catch` block you have to check for errors in the callback
* __no return value__: async functions do not return values; however, values will be passed to the callbacks

Async actions are completed through callbacks and __The Event Loop__.

### Event Driven Programming
__Event-driven programming__ is a programming paradigm in which the flow of the program is dictated by events (changes in state), including user actions, sensor outputs, or messages from other programs or threads. In this paradigm, every command is event based, which means that the program revolves around a loop that polls for input or data (or state change). When an event occurs, a callback is invoked. JavaScript is well suited to this programming style because it has first-class functions and closures.

Node.js employs an event loop, which is a construct that performs two tasks (__event detection__ and __event handler triggering__). The event loop detects which events just happened as well as determining which event callback to invoke once an event has happened. The event loop is responsible for scheduling asynchronous operations and facilitates the event-driven programming paradigm in which the flow of the program is determined by said events [(Norris)](#resourses). In other words, applications act on events, and Node.js implements this through two mechanisms: the event loop and the `EventEmitter` that listens for events and invokes a callback function once an event has been detected (i.e. state has changed) [(maxogden)](#resources).

Although V8 is single-threaded, the underlying C++ API of Node.js is not, which means that whenever we call something that is an I/O operation, Node relies on __libuv__ to run code concurrently with our javascript code. Once this thread (form _libuv_) receives a value, it awaits for data or throws an error, and then the provided callback is called with the necessary parameters.

  > In Node.js, there are actually two separate kinds of events. There are __system events__, which are lower-level events that are handed by libuv, and __custom events__, which are handled by the JavaScript core by the `EventEmitter`. The `EventEmitter` is used by many of Node.js' core modules, including `Server`, `Socket`, and  `http`.

JavaScript code sometimes wraps calls to the C++ side of Node. Often, when an event occurs in libuv, it generates a custom event to make it easier to manage our code and decide what code should run when that event happens. This makes it seem as though system events and custom events are the same. They are not.  

One way Node.js implements an event-driven approach is by attaching listeners to events. When those events fire, the listener executes the provided callback. Whenever you call `setTimeout`, `http.get`, or `fs.readFile`, Node.js uses libuv to send these operations to a different thread. This allows V8 to keep executing code. Node invokes the callback when the counter has run down or the I/O operation/http operation has finished. Therefore, you can read a file while processing a request in your server, and then make an http call based on the read contents without blocking other requests from being handled.

The Node.js JavaScript core modules only provides one thread and one call stack, so when another request is being served as a file is read, its callback will need to wait for the stack to become empty. The __task queue (event queue, or message queue)__ is the place where callbacks are waiting to be executed. All callbacks are invoked whenever the main thread has finished its previous task.

To understand how this works, you must understand the __event loop__ and the __task queue__.

[Philip Roberts provides an excellent explanation of the JavaScript call stack and event loop.](https://youtu.be/8aGhZQkoFbQ)

### The Event Loop In a little more detail
If you look at the libuv library, you can find the Node.js event loop. It looks something like this:

```
while (r != 0 && loop->stopflag == 0) {
  magical incantations here
}
```

The Node.js event loop runs infinitely under a single thread, and the JavaScript program code running in this thread is executed synchronously. Every call that involves an I/O operation requires a callback to be registered. Certain functions and modules, usually written in C/C++, support asynchronous I/O. When asynchronous events are invoked, they are assigned a thread from the thread pool using libuv. This allows the program to perform multiple I/O operations in parallel with the main thread. When the I/O operation completes, its callback is pushed onto the event queue where it will be executed as soon as all other callbacks on that queue are invoked. In other words, every time a system call takes place, that event will be delegated to the event loop along with a callback function, or listener. The main thread is _not_ tied up and keeps serving other requests.

Below is a digram of a Node.js server's event loop.

```      
                            LIBUV (Async I/0)
              +-------------------------------------------+
              |                                           |   
              |  Event Queue                 Thread pool  |                     Operating System
              | +---------+                  +---------+  | Blocking Operation  +--------------+
              | |         |                  | +-----+ <-----------------------+|  Database    |
              | | +-----+ |                  | +-----+ |  |                     +--------------+
              | | |     | |                  |         |  |
  Execute CB  | | +-----+ |                  | +-----+ |  |                     +--------------+
  JS Program  | |         |      XXXX        | +-----+ |  |                     |  File System |
<-------------+ | +-----+ |     XX  XX       |         |  |                     +--------------+
              | | |     | |   XX      X      | +-----+ |  |
              | | +-----+ |   X        |     | +-----+ |  |                     +--------------+
              | |         |   X        V     |         |  |                     |  Network     |
              | |         |    Event Loop    |         |  |                     +--------------+
              | | +-----+ |    ^             |         |  |
              | | |     | |    |        X    |         |  |                     +--------------+
              | | +-----+ |    X        X    |         |  |                     |   Others     |
              | |         |     XX     XX    |         |  |                     +--------------+
              | |         |      XXXXXX      |         |  |                            |
              | +---^-----+                  +---------+  |                            |
              |     ^       Operation Complete            |    Operation Complete      |
              |     +----------<-------------------<------------------<----------------+
              +-------------------------------------------+

```

As you can see, the event loop iterates over the event queue, which can be thought of as a "list" of events and callbacks of completed operations. If a process requires I/O, the event loop delegates the operation to the thread pool. Libuv assigns threads from the thread pool and the I/O operations are executed asynchronously. The event loop will then continue to execute items in the event queue. Once the I/O operation is complete, the callback is queued for processing. The event loop then executes the callback and provides the results.

#### The EventEmitter (JavaScript Custom Events)
According to [Norris](#resources), the `EventEmitter` was created to simplify the interaction with the event loop. The `EventEmitter` was created as a generic wrapper to facilitate creating event-based APIs. To understand how Node handles events, we're going to build our own event emitter. (Albeit a simple version).

In this example, we will create an object called `Emitter` that will have two methods, `on` and `emit`. The `on` method will have two arguments, `type` and `listener`, which will be used to register an event listener. In order to access the values of `events`, the key to the Emitter object will be assigned to the `type`. And the listener will be an array of functions. We can then invoke the listeners by calling the `emit` method. This provides a clean way of controlling logic in the code.  

```javascript
function Emitter() {
  this.events = {};
}

Emitter.prototype.on = function(type, listener) {
  this.events[type] = this.events[type] || [];
  this.events[type].push(listener);
}

Emitter.prototype.emit = function(type) {
  if (this.events[type]) {
    this.events[type].forEach((listener) => {
      listener();
    });
  }
};

module.exports = Emitter;

```

Now, to use the Emitter:

```javascript

const Emitter = require('./emitter');

const emtr = new Emitter();

emtr.on('haiku', function() {
  console.log('Climb Mount Fuji');
});

emtr.on('haiku', function() {
  console.log('But slowly, slowly!');
});

console.log('O snail');
emtr.emit('haiku');

```
This program will produce one of [Kobayashi Issa's haikus](https://www.wikiwand.com/en/Kobayashi_Issa):
```
O Snail
Climb Mount Fuji,
But slowly, slowly!
```

In this example,
 1. The `on` method is invoked with the type `'haiku'` and the listener `function() {console.log('Climb Mount Fuji')}` as arguments. It assigns the type as the property name, and then pushes the listener into an array as a value.
 2. Another `on` method is called with the same type and a similar function is pushed pushed to the array.
 3. `'O snail'` is logged to the console.
 4. Finally, the `emit` method is invoked with the type 'haiku'.
  * The emit method finds the 'haiku' as a property of the event object, and then loops through the array and invokes the listener at every index.

You can easily find the Node.js EventEmitter by looking into the Node core modules in the `lib` directory in the `events.js` file. It is much more robust than the one we just built, but the idea is the same, including an `emit`, and `on` function.

To use node's event emitter:
```javascript
  const Emitter = require('events');
  const emtr = new Emitter();
```
* Just like in our example, the Node module's `on` method takes a string (type) and a function (listener), and the `emit` method is also invoked with the type name.

* When the `EventEmitter` object emits an event, all of the functions attached to that event are invoked _asynchronously_. Any values returned by the called listeners are _ignored_ and will be discarded [(Node)](https://nodejs.org/dist/latest-v6.x/docs/api/events.html).

Although this technique is useful for concisely controlling the logic of our programs, it has one drawback: [Magic Strings](https://www.wikiwand.com/en/Magic_string). A simple definition of a __magic string__ is a string that has some special meaning in the code. This is problematic because it is easy for typos to cause difficult to find bugs.
  * A good workaround is to assign this to a variable in a config file (`./config/evntCnfg.js`)[(Alicea)](#references).

  ```javascript
  module.exports = {
    events: {
      HAIKU: 'haiku',
      FILESAVED: 'filesaved'
    }
  }  
  ```
  ```javascript
  const Emitter = require('./emitter');
  const evntCnfg = require('./config').events;

  const emtr = new Emitter();

  emtr.on(evntCnfg.HAIKU, function() {
    console.log('Climb Mount Fuji');
  });

  emtr.on(evntCnfg.HAIKU, function() {
    console.log('But slowly, slowly!');
  });

  console.log('O snail');
  emtr.emit(evntCnfg.HAIKU);

  ```
  [(Alicea)](#references)

> Note: Event emitters are not asynchronous in nature. It often appears to be asynchronous because it is regularly used to signal the completion of asynchronous operations, but the `EventEmitter` API is synchronous. Meaning, listeners will be executed synchronously in the order that they were added before any execution can continue in statements following the call to emit [Norris](#resources).

---
## The Event Loop

### Mechanical overview of when things run:
```
        +---------------------+
  +---> |       Timers        |
  |     +---------------------+
  |
  |     +---------------------+
  |     |  Pending callbacks  |
  |     +---------------------+         +-----------------------+
  |                                     |                       |
  |     +---------------------+         |       Incoming:       |
  |     |        Poll         | <-------+      Connections,     |
  |     +---------------------+         |       data, etc.      |
  |                                     |                       |
  |     +---------------------+         +-----------------------+
  +-----+    setImmediate     |
        +---------------------+

```
  In addition to the diagram:
    * callbacks scheduled via `process.nextTick()` are run at the end of a phase of the event loop before transitioning to the next phase. This creates the potential to unintentionally starve the event loop with recursive calls to `process.nextTick()`.
    * "Pending Callbacks" is where callbacks are queued to run that are not handled by any other phase (e.g. a callback passed to `fs.write()`).
[(Norris)](#resources)


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

[Teixeira, Pedro. "Professional Node.js: Building JavaScript Based Scalable Software." _Wrox_.  2012. ](https://www.amazon.com/Professional-Node-js-Building-Javascript-Scalable/dp/1118185463/ref=sr_1_1?ie=UTF8&qid=1486686418&sr=8-1&keywords=%5BProfessional+Node.js%3A+Building+JavaScript+Based+Scalable+Software%5D%28%29)

[Halliday, "substack" James. "The Stream Handbook" _github.com/substack/stream-handbook_. 2015.](https://github.com/substack/stream-handbook#introduction)

[Norris, Trevor. "Understanding the Node.js Event Loop." _The odesource Blog_. 2015.](https://nodesource.com/blog/understanding-the-nodejs-event-loop/)

[Raoof, Abdel. 'Introduction to Node.js.' _abdelraoof.com_. 2015.](http://abdelraoof.com/blog/2015/10/19/introduction-to-nodejs/)

[Alicea, Anthony. "Learn and Understand NodeJS." _Udemy_. 2017.](https://www.udemy.com/understand-nodejs/learn/v4/overview)

["Node.js." _Wikipedia_. 2017.](http://www.wikiwand.com/en/Node.js)

["Observer Pattern." _Wikipedia_. 2017.](https://www.wikiwand.com/en/Observer_pattern)

<!-- Need to Summarize:

Thread pool - The main thread call functions post tasks to the shared task queue that threads in the thread pool pull and execute. Inherently non-blocking system functions like networking translates to kernel-side non-blocking sockets, while inherently blocking system functions like file I/O run in a blocking way on its own thread. When a thread in the thread pool completes a task, it informs the main thread of this that in turn wakes up and execute the registered callback. Since callbacks are handled in serial on the main thread, long lasting computations and other CPU-bound tasks will freeze the entire event-loop until completion.


Node.js registers itself with the operating system so that it is notified when a connection is made, and the operating system will issue a callback. Within the Node.js runtime, each connection is a small heap allocation. Traditionally, relatively heavyweight OS processes or threads handled each connection. Node.js uses an event loop for scalability, instead of processes or threads.[70] In contrast to other event-driven servers, Node.js's event loop does not need to be called explicitly. Instead callbacks are defined, and the server automatically enters the event loop at the end of the callback definition. Node.js exits the event loop when there are no further callbacks to be performed. -->
