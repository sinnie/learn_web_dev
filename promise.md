# Promises

## Definition:

* According to MDN, a __promise__ is an object used for asynchronous computations and represents a value that may be available now, in the future, or never. Essentially, a promise is an object that stores information about whether asynchronous events have happened yet or what their outcome is.

## Syntax

* ES6 Promises are instances of the built-in `Promise` and are created by calling `new Promise` with a single function as an argument.

```javascript
  new Promise( /* executor */ function(resolve, reject) { . . .} );
```

### Parameters

* executor
  * A function with the arguments __resolve__ and __reject__. This function is immediately invoked by the Promise implementation, passing __resolve__ and __reject__ functions.
  * The executor starts an asynchronous operation.
* `resolve` and `reject` are callable functions that take an argument which represents the events details. Calling either `resolve` or `reject` will mark the promise as resolved and cause any handlers to be run.


### Basic Promise

____________
| Promise  |
____________ -------> Success: `.then(content)`
            |
             --------> Failure: `.catch(err)`

***

## Promise's States:
* Promises are created inside of async functions and then returned. Success and failure handlers are attached to the promise.
* A Promise has three possible states:
  1. __Pending__ - not fulfilled or rejected (unresolved)
  2. __Fulfilled__ - the operation completed successfully (resolved)
  3. __Rejected__ - the operation has failed (rejected)
* A promise can only represent one event and it can only be in one state at a time. Each function (`reject(), resolve(), fulfill()`) permanently changes the state of the promise. Once a promise is resolved, it's state can not be reverted.
* A new Promise is initially in a Pending state. It can either be _fulfilled_ with a value or _rejected_ with an error. When this happens, the associated handlers queued up by a promise's _then_ method.
  * race conditions are avoided because handlers attached to a promise in a fulfilled or rejected state will also be called.

## Handling Success or Failure
* A promise is resolved when the asynchronous operation has completed. To access the result of the callback, use the `then()` method to register a handler. These callbacks will be invoked with the result of the executor when promise is fulfilled.
* If a promise is rejected, you can access the error by using the  `catch()` method to register a callback.
  * you can also add a rejection handler as a second parameter in the `then()` callback.
* `.then()` will always return a promise. Thus, you can chain them and still have "flat" code.

## Chaining Promises

* You can chain promises by using consecutive`then()` methods. Return values will be provided as an argument to the next `then()` when it is chained.

```javascript
invokePromise()
  .then((promiseValue) => {
    return 'I am a return value'
  })
  .then((returnValueStr) => {
    console.log(returnValueStr);  // I am a return value
  })
  .catch((err) => {
    console.error(err);
  });
```
* If the promise's `then()` method returns another promise, the next `then()` method is called once the promise resolves. If the promise is rejected, the `catch()` method is invoked.

```javascript
invokePromise()
  .then((promiseValue) => {
    return invokeAnotherPromise()
  })
  .then((anotherPromiseValue) => {
    console.log(anotherPromiseValue);
  })
  .catch((err) => {
    console.error(err);
  });
```
* If an error is thrown in a promise, the the next `catch()` method (or the next `then()` with a rejection handler) handles the error.

```javascript
invokePromise()
  .then((promiseValue) => {
    throw new Error('Boo')
  })
  .then((anotherPromiseValue) => {
    // Never called
  })
  .catch((err) => {
    console.error(err);  // Boo
  });
```
*  `return` goes to the next `.then()`, and `.throw()` goes to the next `.catch()`.
  * A good pattern is to put a `.catch()` on the end of every `.then()` chain.

## Promise.all([promise1, promise2, ...])
* `Promise.all` returns a promise that resolves when _all_ of it's arguments (promises) have resolved, or is rejected when _any_ of it's arguments are rejected.
  * The returned promise is an array containing the results of every promise.
* Use this for running animations concurrently or multiple DB requests concurrently.

## Promise.resolve(value)
* `Promise.resolve(value)` returns a promise that is resolved with the given value. If the value has a `.then()` method, the returned promise will "follow" that thenable, adopting its eventual state. Otherwise, the returned promise will be fulfilled with the value.
* Useful when you need a promise and don't have one - when you're building an array of promises to pass to `Promise.all`, or if the argument to a function is a promise or a value.

### Syntax

```javascript
Promise.resolve(value);
Promise.resolve(promise);
Promise.resolve(thenable);
```

## Promise.reject(value)
* The `Promise.reject(reason)` method returns a `Promise` Object that is rejected with the given reason. It's useful to make `reason` an instance of `Error`.
* Useful when you want to process error objects in a `catch` handler, but don't want to return a _successful_ promise afterwards.

## Advantages of a Promise

  1. Separate success handling logic from the error handling logic
  2. Avoid callbacks and callback hell
  3. Concise way to work with asynchronous JavaScript code.


## Resources:
[jamesknelson.com](http://jamesknelson.com/grokking-es6-promises-the-four-functions-you-need-to-avoid-callback-hell/)
[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
[Promises - In Wicked Detail](http://www.mattgreer.org/articles/promises-in-wicked-detail/)
