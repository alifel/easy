# CommonJS modules

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
- Files with a `.js` extension or without an extension, when the nearest parent `package.json` file doesn't contain a top-level field "type" or there is no `package.json` in any parent folder; unless the file contains syntax that errors unless it is evaluated as an ES module. Package authors should include the "`type`" field, even in packages where all sources are CommonJS. Being explicit about the type of the package will make things easier for build tools and loaders to determine how the files in the package should be interpreted.
  以`.js`为扩展名或者没有扩展名的文件，当最近的父级`package.json`中不包含顶级域“`type`”或没有`package.json`文件在任意父级文件夹时；unless the file contains syntax that errors unless it is evaluated as an ES module ***(:pill::pill::pill:这句不知道如何理解，2个unless让我很迷茫)***。包的作者应该包括“`type`”域，即使包中所有的资源都是CommonJS。精确的指出包的类型会让事情（对于构建工具和加载器决定如何解释包中的文件）更容易。
- Files with an extension that is not `.mjs`, `.cjs`, `.json`, `.node`, or `.js` (when the nearest parent `package.json` file contains a top-level field "type" with a value of "module", those files will be recognized as CommonJS modules only if they are being included via `require()`, not when used as the command-line entry point of the program).
  文件的扩展名不是`.mjs`、`.cjs`、`.json`、`.node`或者`.js`（当最近的父级`package.json`文件包含顶级域“`type`”，并且它有一个值“`module`”时，仅当它们被通过`require()`包含，而不是用作命令行的入口程序时，这个文件被识别为CommonJS模块）

See [Determining module system](https://nodejs.org/docs/latest/api/packages.html#determining-module-system) for more details.

Calling `require()` always use the CommonJS module loader. Calling `import()` always use the ECMAScript module loader.

调用`require()`一直使用CommonJS模块加载器。调用`import()`一直使用ECMAScript模块加载器。

## Accessing the main module

When a file is run directly from Node.js, `require.main` is set to its `module`. That means that it is possible to determine whether a file has been run directly by testing `require.main === module`.

当一个文件直接在Node.js中运行时，`require.main`被设置为它的`module`。这意味着可以通过`require.main === module`去测试一个文件是否时直接被执行的。

For a file `foo.js`, this will be `true` if run via `node foo.js`, but `false` if run by `require('./foo')`.

对于`foo.js`，如果是它是通过`node foo.js`执行的，那么为`true`，如果通过`require('./foo')`

When the entry point is not a CommonJS module, `require.main` is `undefined`, and the main module is out of reach.

当入口程序不是CommonJS模块，`require.main`是`undefined`，主模块无法访问。

## Package manager tips

The semantics of the Node.js `require()` function were designed to be general enough to support reasonable directory structures. Package manager programs such as `dpkg`, `rpm`, and `npm` will hopefully find it possible to build native packages from Node.js modules without modification.

Node.js `require()`方法的语义被设计的足够通用，以支持合理的目录结构。包管理程序例如，`dpkg`、`rpm`和`npm`希望直接通过Node.js的模块（不需要修改）就能构建本地包。

Below we give a suggested directory structure that could work:

下面我们给出一个可行的目录结构的建议：

Let's say that we wanted to have the folder at `/usr/lib/node/<some-package>/<some-version>` hold the contents of a specific version of a package.

让我们来看看我们想有一个文件夹在`/usr/lib/node/<some-package>/<some-version>`，持有1个包指定版本的内容。

Packages can depend on one another. In order to install package `foo`, it may be necessary to install a specific version of package `bar`. The `bar` package may itself have dependencies, and in some cases, these may even collide or form cyclic dependencies.

包可以依赖其他包。为了安装包`foo`，它也可能需要安装指定版本的`bar`包。`bar`包也可能有自己的依赖，在许多例子中，它们甚至冲突或者循环依赖。

Because Node.js looks up the `realpath` of any modules it loads (that is, it resolves symlinks) and then [looks for their dependencies in node_modules folders](#loading-from-node_modules-folders), this situation can be resolved with the following architecture:

- /usr/lib/node/foo/1.2.3/: Contents of the foo package, version 1.2.3.
- /usr/lib/node/bar/4.3.2/: Contents of the bar package that foo depends on.
- /usr/lib/node/foo/1.2.3/node_modules/bar: Symbolic link to /usr/lib/node/bar/4.3.2/.
- /usr/lib/node/bar/4.3.2/node_modules/*: Symbolic links to the packages that bar depends on.

Thus, even if a cycle is encountered, or if there are dependency conflicts, every module will be able to get a version of its dependency that it can use.

When the code in the foo package does require('bar'), it will get the version that is symlinked into /usr/lib/node/foo/1.2.3/node_modules/bar. Then, when the code in the bar package calls require('quux'), it'll get the version that is symlinked into /usr/lib/node/bar/4.3.2/node_modules/quux.

Furthermore, to make the module lookup process even more optimal, rather than putting packages directly in /usr/lib/node, we could put them in `/usr/lib/node_modules/<name>/<version>`. Then Node.js will not bother looking for missing dependencies in /usr/node_modules or /node_modules.

In order to make modules available to the Node.js REPL, it might be useful to also add the /usr/lib/node_modules folder to the $NODE_PATH environment variable. Since the module lookups using node_modules folders are all relative, and based on the real path of the files making the calls to require(), the packages themselves can be anywhere.

## Core modules {#core-modules}

Node.js has several modules compiled into the binary. These modules are described in greater detail elsewhere in this documentation.

Node.js有一些模块编译进了二进制包中。这些模块在文档的其他地方有更详细的描述。

The core modules are defined within the Node.js source and are located in the `lib/` folder.

核心模块被定义在Node.js的源码中，它位于`lib/`文件夹下。

Core modules can be identified using the `node:` prefix, in which case it bypasses the require cache. For instance, `require('node:http')` will always return the built in HTTP module, even if there is `require.cache` entry by that name.

核心模块可以被`node:`前缀标识，在这种情况下它会绕过require的缓存。例如`require('node:http')`会一直返回内置的HTTP模块，尽管通过名字有一个`require.cache`的入口。

Some core modules are always preferentially loaded if their identifier is passed to `require()`. For instance, `require('http')` will always return the built-in HTTP module, even if there is a file by that name. The list of core modules that can be loaded without using the `node:` prefix is exposed as [module.builtinModules](https://nodejs.org/docs/latest/api/module.html#modulebuiltinmodules).

一些核心模块会一直优先加载，如果它的标识符被送入`require()`。例如：`require('http')`将会加载内置的HTTP模块，即使有一个同名的文件。不使用`node:`前缀就能被加载的核心模块列表可以被[module.builtinModules](https://nodejs.org/docs/latest/api/module.html#modulebuiltinmodules)导出。

## File modules

If the exact filename is not found, then Node.js will attempt to load the required filename with the added extensions: `.js`, `.json`, and finally `.node`. When loading a file that has a different extension (e.g. `.cjs`), its full name must be passed to `require()`, including its file extension (e.g. `require('./file.cjs')`).

如果准确的文件名没有被找到，Node.js将会尝试给被要求的文件依次增加`.js`, `.json`，`.node`的扩展名。当加载的文件有不同的扩展名，它的全名（包括它的扩展名，例如 `require('./file.cjs')`）必须送入`require()`。

.json files are parsed as JSON text files, .node files are interpreted as compiled addon modules loaded with process.dlopen(). Files using any other extension (or no extension at all) are parsed as JavaScript text files. Refer to the Determining module system section to understand what parse goal will be used.

A required module prefixed with '/' is an absolute path to the file. For example, require('/home/marco/foo.js') will load the file at /home/marco/foo.js.

A required module prefixed with './' is relative to the file calling require(). That is, circle.js must be in the same directory as foo.js for require('./circle') to find it.

Without a leading '/', './', or '../' to indicate a file, the module must either be a core module or is loaded from a node_modules folder.

If the given path does not exist, require() will throw a MODULE_NOT_FOUND error.

## Folders as modules

There are three ways in which a folder may be passed to `require()` as an argument.

有三种方法可以把一个文件夹送入`require()`作为参数。

The first is to create a `package.json` file in the root of the folder, which specifies a main module. An example `package.json` file might look like this:

第一种是创建一个`package.json`文件在文件夹的根目录下，指定一个主模块。就像下面的`package.json`一样。

```json
    { 
        "name" : "some-library",
        "main" : "./lib/some-library.js" 
    } 
```

If this was in a folder at `./some-library`, then `require('./some-library')` would attempt to load `./some-library/lib/some-library.js`.

如果文件夹位于`./some-library`，`require('./some-library')`将尝试加载`./some-library/lib/some-library.js`。

If there is no `package.json`file present in the directory, or if the "`main`" entry is missing or cannot be resolved, then Node.js will attempt to load an `index.js` or `index.node` file out of that directory. For example, if there was no `package.json` file in the previous example, then `require('./some-library')` would attempt to load:

如果没有`package.json`文件出现在目录中，或者没有“`main`”入口，或者“`main`”没有被解析，则Node.js会尝试在该目录加载`index.js`或者`index.node`文件。在上面的例子中如果没有`package.json`文件，则`require('./some-library')`会尝试加载：

- `./some-library/index.js`
- `./some-library/index.node`

If these attempts fail, then Node.js will report the entire module as missing with the default error:

如果这些常识失败了，Node.js会报告一个模块丢失的默认错误。

```shell
    Error: Cannot find module 'some-library'
```

In all three above cases, an `import('./some-library')` call would result in a `ERR_UNSUPPORTED_DIR_IMPORT` error. Using package `subpath exports` or `subpath imports` can provide the same containment organization benefits as folders as modules, and work for both `require` and `import`.

在上面三种情况中，`import('./some-library')`会导致一个`ERR_UNSUPPORTED_DIR_IMPORT`的错误。使用包的`subpath exports`或者`subpath imports`可以提供与文件夹模块相同的组织优势，并且`require`和`import`都可以工作。

## Loading from node_modules folders {#loading-from-node_modules-folders}

If the module identifier passed to `require()` is not a [core](#core-modules) module, and does not begin with `'/'`, `'../'`, or `'./'`, then Node.js starts at the directory of the current module, and adds `/node_modules`, and attempts to load the module from that location. Node.js will not append `node_modules` to a path already ending in `node_modules`.

如果被送入`require()`的模块标识符不是核心模块，并且没有以`'/'`、`'../'`或`'./'`开始，Node.js则开始在当前模块的目录上，增加`/node_modules`，然后尝试从这个位置加载模块。Node.js不会增加`node_modules`去一个已经以`node_modules`结尾的路径。

If it is not found there, then it moves to the parent directory, and so on, until the root of the file system is reached.

如果没有找到，他会移动到父目录继续，知道文件系统的根目录。

For example, if the file at '`/home/ry/projects/foo.js`' called `require('bar.js')`, then Node.js would look in the following locations, in this order:

例如，如果在'`/home/ry/projects/foo.js`'的文件调用`require('bar.js')`，Node.js将会按下面的顺序进行查找：

- `/home/ry/projects/node_modules/bar.js`
- `/home/ry/node_modules/bar.js`
- `/home/node_modules/bar.js`
- `/node_modules/bar.js`

This allows programs to localize their dependencies, so that they do not clash.

这会允许程序本地化依赖，以便不产生冲突。

It is possible to require specific files or sub modules distributed with a module by including a path suffix after the module name. For instance `require('example-module/path/to/file')` would resolve `path/to/file` relative to where `example-module` is located. The suffixed path follows the same module resolution semantics.

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
