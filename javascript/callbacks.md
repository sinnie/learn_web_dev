# Closures and Callbacks

[More JavaScript Articles](./javaScript)

[Back to Learn Web Development](../README.md)

---

If you've done any sort of JavaScript Programming

## Terms:
* __Closure__ - Closures are functions that have references to independent (free) variables. These are functions that have reference and access to variables in the outer environment.
  -  Using closures, you can have a function that returns a function.
  - every execution context has a space in memory where variables and functions created inside of it live. When the execution context goes away, the memory is still there before garbage collection takes care of it. Any Functions invoked afterwards have access to the outer lexical environment reference. Even though the execution context may be gone (it has popped off the stack), any inner function still has reference to the out function, the outer execution context's memory though the scope chain - even if it is removed from the stack.
  - Closures are a feature of the JavaScript programming language, and it does not matter _when_ a function is invoked. The javaScript engine assures that the scope is intact.
* __Free Variables__ - variables that are used locally but defined in an enclosing scope.
* __Callbacks__ -
* __First-Class Functions__ - A programming language that supports the functional programming style. Languages that support first-class functions (or function literals) treat functions as first-class objects. Functions are __Objects.__ This means that the language supports:
  - constructing new functions during the execution of a program
  - storing functions in data structures
  - passing functions as arguments to other functions
  - adding properties to functions
  - and returning functions as the values of other functions.
  - First-Class functions are Objects with a type and behavior. First-Class functions can be dynamically built, passed around like any other object, and also invoked. Everything you can do with other types, you can do with functions (assign them to variables, pass them around, and create them 'on-the-fly').
* __Function Expression__ - An __expression__ is a unit of code that results in a value (does not have to be saved  to a variable), then a __function expression__ is a function that returns a value. A function expression is a component of a larger expression syntax, which is usually a variable assignment (think named function expressions) but can, however, remain anonymous. A good way to spot a function expression is that it will not be defined with the keyword `function`. Because JavaScript functions are objects, you can have both function expressions and function declarations.
  - function expressions are not hoisted because variable declarations are initially set to undefined.
  - Functional programing and function expressions
* __Lexical environment__ - Where something is in the code.
* __Closure__ -


### Let's build a fn.
```js
function sayHiLater() {
  const greeting = 'Hi';

  setTimeout(() => {
      console.log(greeting);
    }, 3000);
}

sayHiLater;
```

This uses function expressions and closures and callbacks! You are passing a function as a parameter. Since you are creating the function inside of the argument, you are taking advantage of a function expression.




## Resources:

[Alicia, Anthony. "Understanding the Weird Parts" _Udemy_](https://www.udemy.com/understand-javascript/learn/v4/t/lecture/2258228?start=0)

["Closures." _MDN_.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)
