# Modules and the File System

[Modules Docs](https://nodejs.org/api/modules.html)

[File System Docs](https://nodejs.org/api/fs.html)

## Node.js Module
Node.js supports modular code in the form of Core modules, File modules, and NPM modules. This allows for more maintainable code and a robust ecosystem of open source software that developers can import via NPM. A __module__ is simply a file that encapsulates related JavaScript code. This code can then be reused in different parts of a program. In Node.js, the `module`  __is a global variable with an `exports` property that references an empty object by default.__ You can export objects or functions using the `module.export` object and require them into another section of you code with the `require()` function, which accepts a module id as an argument. When invoking `require()`, assign the required object to a variable to access its properties.

> In each module, the `module` free variable is a reference to the object representing the current module. For convenience,  `module.exports` is also accessible via the `exports` module-global. `module` is not actually a global, but local to each module.

#### There are three kinds of Modules:
1. Core Modules
1. NPM Modules
1. File Modules

### Core Modules
Core modules are built-in to Node.js that are compiled into binary. These modules include `fs`, `http`, and `path` and are required by their name only as the argument to `require`. The core modules are defined within Node.js' source and are located in the lib/ folder. Core modules are preferentially loaded if their identifier is passed to `require()`. Meaning, `require('http')` will always return the built-in HTTP, even if there is another file by that name.

### NPM Modules
NPM modules include code that can be found in the NPM registry. Developers can download NPM modules with the `npm install` command. These modules are required in an identical fashion as the core modules.

### File Modules
File modules are modules that a developer has created as a part of his/her own program. If the exact filename of a core module is not found when imported into the program, Node.js will attempt to load the required filename with the added extensions: `.js` (JavaScript text files), `.json` (JSON text files), and `.node` (compiled add-on modules loaded with `dlopen`).

> Again: without a leading '/', './', or '../' to indicate a file, the module must either be a core module or loaded from the `node_modules` folder (NPM package).

## Export a function
Functions can be exported by assigning the function to the `module.exports` object. Exported functions can be invoked immediately.

```javascript
  module.exports = () => console.log('You can export a function');
```

## Export an Object
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
exports.anotherFunk = () => console.log('Kool & The Gang');
```

3. Because exports is a shorthand for module.exports, the third way is to assign a value directly to one of its properties

```javascript
exports.myOtherFunc = () => console.log('Kool & The Gang');
```

---

## Node.js node_modules Folder
The node_modules folder in a node.js applicaton usually contains the modules used by that application. A module in this directory can also have a node_modules folder containing modules which are required by that module.


## Node.js Module Caching
Modules are cached after the first time they are loaded, which results in typical behavior. In other words, every call to `require(obj)` will return the same object. An object is returned when a module is required for the first time. This object is cached and is returned whenever a `requre` function resolves to the same path.

## `module.exports` vs `exports`
Initially, `module.exports` and `exports` are references to the same (empty) object. However, only `module.exports` will be returned by `require()`.

Therefore, when you do something like this:
```javascript
exports.a = function() {
    console.log("a");
}
exports.b = function() {
    console.log("b");
}
```

You are adding two functions, 'a' and 'b', to the object that module.exports points to. Here, you have an `object : { a: [Function], b: [Function] }`. And you will get the same result if you use `module.exports` instead of simply `exports`. Here, the `module.exports` acts like a container of exported values. But what if you were trying to export a constructor function?

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
3.



```
function Module(id, parent) {
    this.id = id;
    this.exports = {};
    this.parent = parent;
    if (parent && parent.children) {
        parent.children.push(this);
    }

    this.filename = null;
    this.loaded = false;
    this.children = [];
}
```
