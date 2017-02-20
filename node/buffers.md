#Buffers

## Terms
* __Data Buffer__ - A data buffer is a region of physical memory storage that is used to _temporarily_ store data while it is being moved from one place to another. The buffer stores data as it is retrieved from an input device or immediately before it is sent to an output device. It can also be used when data is to be moved between processes within a computer.


Prior to the introduction of the `TypedArray` in ES6, JavaScript did not have a way to read or manipulate streams of binary data. The `Buffer` class was introduced as part of the Node.js API to allow JavaScript to interact with octet streams in the context of TCP streams and file system operations.

* The `Buffer` class is a global within Node.js and handles binary-handling tasks with a binary buffer implementation, which is exposed as a JS API under the buffer pseudo-class.
