# File System and Path Modules

[Node.js fs Docs](https://nodejs.org/api/fs.html)

[Back to Modules](./module.md)

[Back to Node Intro](./node.md)

[Back to Learn Web Development](../../README.md)

## Intro
In this section, we are going to explores the __File System__. While working with the filesystem, we will use some of the concepts that we've seen before, including asynchronous JavaScript, libuv, buffers and callbacks. Before we get into some examples, however, there are a few bases to cover. We will briefly discuss the Path Module, Buffers, and the global Process Module. And I'll try to describe the relevance of every section.

## File System
File I/O is provided by wrappers around standard POSIX (Portable Operating System Interface - a standards specified by IEEE for maintaining compatibility between operation systems) functions. All Node.js file system methods have asynchronous and synchronous forms. The difference between these two methods is delimited with the addition of the word "sync" in the method name for, as you might guess, the synchronous versions. Any method with the name "sync" in the name will return the value directly and prevent Node.js from executing any other code. It will block.

To use the file system module, import the fs module into your file with the following function: `const fs = require('fs');` As we've seen before, this will return the fs object that has many useful methods that allow us to work with the lower-level operating system. One such method is `fs.readFileSync()`.

### __Asynchronous File System Method:__

Let's take a look at how to use the synchronous method to read a file:

```js
const fs = require('fs');

// arguments: path (same directory) + filename + encoding
const myFile = fs.readFileSync(___dirname + '/myfile.txt', 'utf8');
```

> Note:
  > __dirname refers to the directory where the file being executed resides.

---

### Syntax:

`fs.readFileSync(file[, options]);`

#### Parameters:
* `file`: String | Buffer | Integer
  - filename or file descriptor
* `options`: Object | String
  - `encoding`: String | Null (the default = 'utf8')
  - `flag`: String | default = `'r'`

#### Return Value:
If the encoding option is specified, the return value is a __string.__ Otherwise, the return value will be a __buffer.__

---

> ### Note:
Here we see the Buffer when the fs.readSync method is called, it accepts a buffer as an argument. It loads the contents of the file into the buffer because the buffer can manage binary data. Because this is the synchronous version of the method, the program will wait while the buffer is being filled and it returns the contents before moving on. This could be useful if you were trying to load some configuration file.

>### Regarding Buffers:
* Before 2015, JavaScript did not have the ability to deal with bytes, which if you are attempting to work with the file system, you will need to do.
* Instead, it handles binary-handling tasks with a binary buffer implementation, which is exposed as a JS API under the buffer pseudo-class
* [More information about buffers can be found here](./buffers.md)

---

It bears repeating that in most cases, you will not want to use the synchronous version of the `readfile` method. It is useful, however, to see that the synchronous operation is a blocking operation.

Let's take a look at the synchronous form.

The asynchronous form takes a completion callback as its last argument. The arguments passed to the completion callbacks depend on the particular method used. The first argument to any method is always reserved for an exception. If the operation was completed successfully, then the first argument will be `null` or `undefined`.

If you are using the synchronous form, any exceptions will immediately be thrown. Use `try/catch` to handle exceptions or allow them to bubble up.


__Synchronous File System Method:__
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
* `...paths` String <-- A sequence of Path Segments
  * If any of the path segments are not a string, a  `TypeError` is thrown.

__Return Value:__
* `String`

The `path.join()` method joins all given `path` segments using the platform specific separator as a delimiter before normalizing the resulting path. A common use of `join` is to manipulate paths when serving urls.

Zero-length __path__ segments are ignored. If the joined path string is a zero-length string, then `'.'` will be returned.

#### Example:
  ```javascript
  const path = require('path');

  path.join('/a/.', './//b/', 'd/../c/')

   // => a/b/c
  ```

## Process Module
Each Node.js process has built-in functionality that can be accessed through the global `process` module. This `process` module does not have to be required because it is a 'wrapper' around the currently executing process, and many of the methods it exposes are actually wrappers around calls into some of Node.js' core C libraries.

__There are several methods made available through the process object, including:__

1. exit
2. beforeExit
3. uncaughtException
4. Signal Events

## process.argv
* The `process.argv` property returns an `<Array>` containing the command line arguments passed when a Node.js process was launched.
  * First element = `process.execPath`
  * Second element = path to the JS file being executed
  * Any remaining elements = any additional command line arguments

For Example, assuming the following script for process-args.js:
  ```js
  // print process.argv

  process.argv.forEach(val, index) => console.log(`${index}: ${val}`);

  ```
Would generate:
  ```
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

## Resources

[Node Docs: Path](https://nodejs.org/api/path.html)

[Alicea, Anthony. "Learn and Understand NodeJS." _Udemy_. 2017.](https://www.udemy.com/understand-nodejs/learn/v4/overview)

[Mixu's Node book](http://book.mixu.net/node/ch11.html)
