# Future of Asynchronous JavaScript
# Intorators

## Generators & yield
* JavaScript Generators are a part of the ES6 Specification.
* Generators allow you to postpone the execution of a function, complete other computations, and then return to complete the execution.

#### Syntax

```javascript
function *gen() {
  yield 1;
  yield 2;
  yield 3;
}

var g = gen(); // "Generator { }"
```
* Uses a _generator_ function that, when called, returns an _iterator object_.
  * When a generator function is called, it doesn't run. You must iterate through it manually.
  * With the iterator object, one can iterate through the function.
  * Call the `next()` method of the iterator object to continue the execution of the function. When `next()` is called, the function continues where it left off and executes until it hits a pause. Additionally, an object that contains information about the state of the generator is returned.
    * This object has a `value` and a `done` property.
    * The `value` property is the current iteration value, which can be anything. The `done` property is a boolean, which indicates that the generator is finished running.
  * If you specify a `return` value in a generator, it will be returned in the last _iterator object_.
  * Every iteration through the generator _function_ __yields__ a value where paused.
  * One can pause a generator functioon with the `yield` keyword.

#### Yield
##### Syntax
```javascript
yield[[expression]]
```
* Calling `next()` starts the generator, and it runs until it hits a `yield`. It returns the object with `value` and `done`, where `value` has the __expression__ value.
* The yielded value will be returned in the generator and it continues. It's also possible to receive a value from the iterator object in a generator (next(val)), then this will be returned in the generator when it continues.
* `yield` is meant for lazy sequences and iterators not specifically for asynchronous programming.

### Async / await
* ES7 introduces Async functions, which are currently only available with a transpiler like babel.
* Use the `async` keyword before the function declaration, which will allow the use of the `await` keyword inside of your newly created async function.

```javascript
async function save(Something) {  
  try {
    await Something.save()
  } catch (ex) {
    //error handling
  }
  console.log('success');
}
```

## Resources

[Understanding JavaScript's async await](https://ponyfoo.com/articles/understanding-javascript-async-await)

[ES6 Generators in Depth](https://ponyfoo.com/articles/es6-generators-in-depth)

[The Evolution of Asynchronous JavaScript](https://blog.risingstack.com/asynchronous-javascript/)

[You Don't Know JS: ES6 & Beyond](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch3.md)
