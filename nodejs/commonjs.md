# commonjs

CommonJS modules are the original way to package JavaScript code for Node.js. Node.js also supports the ECMAScript modules standard used by browsers and other JavaScript runtimes.

CommonJS 模块是Node.js最初打包Javascript代码的方法。Node.js也支持ECMAScript modules标准（被浏览器和其他javascript运行时使用的）。

In Node.js, each file is treated as a separate module. For example, consider a file named foo.js:

在Node.js中，每一个文件被作为一个单独的模块，例如，考虑一个`foo.js`的文件:

```js
    const circle = require('./circle.js');
    console.log(`The area of a circle of radius 4 is ${circle.area(4)}`);
```

On the first line, `foo.js` loads the module `circle.js` that is in the same directory as `foo.js`.

在第一行，`foo.js`加载了模块`circle.js`（它和`foo.js`在同一个目录中）。

Here are the contents of `circle.js`:

这儿时`circle.js`的内容：

```js
    const { PI } = Math;

    exports.area = (r) => PI * r ** 2;

    exports.circumference = (r) => 2 * PI * r;
```

The module `circle.js` has exported the functions `area()` and `circumference()`. Functions and objects are added to the root of a module by specifying additional properties on the special `exports` object.

模块`circlr.js`已经导出了方法`area()` 和 `circumference()`。方法和对象通过在指定的`exports`对象上指定额外的property来把方法和对象添加到模块的根部。

Variables local to the module will be private, because the module is wrapped in a function by Node.js ([see module wrapper](#module-wrapper)). In this example, the variable `PI` is private to `circle.js`.

局部的变量相对于模块时私有的，因为模块被Node.js ([see module wrapper](#module-wrapper))包裹进了一个方法中。在这个例子中，变量`PI`是私有的，对于`circle.js`来说。

The `module.exports` property can be assigned a new value (such as a function or object).

`module.exports` property也可以被赋予一个新值。

Below, `bar.js` makes use of the `square` module, which exports a Square class:

下面，`bar.js`使用了`square`模块，它导出了Square class。

```js
    const Square = require('./square.js');
    const mySquare = new Square(2);
    console.log(`The area of mySquare is ${mySquare.area()}`); 
```

The square module is defined in `square.js`:

square模块被定义在`square.js`中：

```js
    // Assigning to exports will not modify module, must use module.exports
    module.exports = class Square {
    constructor(width) {
        this.width = width;
    }

    area() {
        return this.width ** 2;
    }
    }; 
```

The CommonJS module system is implemented in the module core module.

CommonJS模块系统在模块的核心模块中被实现。

## Enabling

Node.js has two module systems: CommonJS modules and ECMAScript modules.

Node.js 有2个模块系统：CommonJS模块和ECMAScript模块。

By default, Node.js will treat the following as CommonJS modules:

默认情况，Node.js将把下面的情况作为CommonJS模块：

- Files with a `.cjs` extension;
  文件扩展名为`.cjs`；
- Files with a `.js` extension when the nearest parent `package.json` file contains a top-level field "`type`" with a value of "`commonjs`".
  以`.js`为扩展的文件，当最近的父级`package.json`文件包含一个顶级域“`type`”的值为“`commonjs`”时；
- Files with a `.js` extension or without an extension, when the nearest parent `package.json` file doesn't contain a top-level field "type" or there is no `package.json` in any parent folder; unless the file contains syntax that errors unless it is evaluated as an ES module. Package authors should include the "type" field, even in packages where all sources are CommonJS. Being explicit about the type of the package will make things easier for build tools and loaders to determine how the files in the package should be interpreted.
  以`.js`为扩展名或者没有扩展名的文件，当最近的父级`package.json`中不包含顶级域“`type`”或没有`package.json`文件在任意父级文件夹时；除非文件包含语法
- Files with an extension that is not .mjs, .cjs, .json, .node, or .js (when the nearest parent package.json file contains a top-level field "type" with a value of "module", those files will be recognized as CommonJS modules only if they are being included via require(), not when used as the command-line entry point of the program).

## The module wrapper {#module-wrapper}

Before a module's code is executed, Node.js will wrap it with a function wrapper that looks like the following:

在一个模块的代码被执行之前，Node.js将会用一个方法像下面的内容一样包裹它：

```js
    (function(exports, require, module, __filename, __dirname) {
    // Module code actually lives in here
    }); 
```

By doing this, Node.js achieves a few things:

通过做这些，Node.js获得了一些东西：

- It keeps top-level variables (defined with var, const, or let) scoped to the module rather than the global object.
  它维持了顶级变量（通过var、const或let定义的）域针对模块而不是全局对象。
- It helps to provide some global-looking variables that are actually specific to the module, such as:
  它
  - The `module` and `exports` objects that the implementor can use to export values from the module.
    实现者可以用`module`和`exports`对象从模块中导出值。
  - The convenience variables `__filename` and `__dirname`, containing the module's absolute filename and directory path.
    方便的变量`__filename` 和 `__dirname`，包含了模块的绝对文件路径和绝对目录路径。
