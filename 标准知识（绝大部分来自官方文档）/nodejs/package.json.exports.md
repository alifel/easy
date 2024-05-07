# exports {#exports}

```json
    {
    "exports": "./index.js"
    } 
```

The ["`exports`"](#exports) field allows defining the entry points of a package when imported by name loaded either via a node_modules lookup or a [self-reference](#self-referencing) to its own name. It is supported in Node.js 12+ as an alternative to the "main" that can support defining [subpath exports](#subpath-exports) and [conditional exports](#conditional-exports) while encapsulating internal unexported modules.

Conditional Exports can also be used within ["`exports`"](#exports) to define different package entry points per environment, including whether the package is referenced via `require` or via `import`.

All paths defined in the ["`exports`"](#exports) must be relative file URLs starting with `./`.

## Self-referencing a package using its name {#self-referencing}

Within a package, the values defined in the package's `package.json` ["`exports`"](#exports) field can be referenced via the package's name. For example, assuming the `package.json` is:

在包里，在包的`package.json`的["`exports`"](#exports)中定义的值可以被包的名字引用。例如，假设`package.json`是下面这样：

```json
    // package.json
    {
    "name": "a-package",
    "exports": {
        ".": "./index.mjs",
        "./foo.js": "./foo.js"
    }
    } 
```

Then any module in that package can reference an export in the package itself:

```js
    // ./a-module.mjs
    import { something } from 'a-package'; // Imports "something" from ./index.mjs. 
```

Self-referencing is available only if `package.json` has ["`exports`"](#exports), and will allow importing only what that ["`exports`"](#exports) (in the `package.json`) allows. So the code below, given the previous package, will generate a runtime error:

```js
    // ./another-module.mjs

    // Imports "another" from ./m.mjs. Fails because
    // the "package.json" "exports" field
    // does not provide an export named "./m.mjs".
    import { another } from 'a-package/m.mjs'; 
```

Self-referencing is also available when using `require`, both in an ES module, and in a CommonJS one. For example, this code will also work:

```js
    // ./a-module.js
    const { something } = require('a-package/foo.js'); // Loads from ./foo.js.
```

Finally, self-referencing also works with scoped packages. For example, this code will also work:

```json
    // package.json
    {
    "name": "@my/package",
    "exports": "./index.js"
    } 
```

```js
    // ./index.js
    module.exports = 42; 
```

```js
    // ./other.js
    console.log(require('@my/package')); 
```

```shell
    $ node other.js
    42 
```

## Subpath exports {#subpath-exports}

When using the ["`exports`"](#exports) field, custom subpaths can be defined along with the main entry point by treating the main entry point as the "`.`" subpath:

```json
    {
    "exports": {
        ".": "./index.js",
        "./submodule.js": "./src/submodule.js"
    }
    } 
```

Now only the defined subpath in "exports" can be imported by a consumer:

```js
    import submodule from 'es-module-package/submodule.js';
    // Loads ./node_modules/es-module-package/src/submodule.js 
```

While other subpaths will error:

```js
    import submodule from 'es-module-package/private-module.js';
    // Throws ERR_PACKAGE_PATH_NOT_EXPORTED 
```

### Extensions in subpaths

Package authors should provide either extensioned (import 'pkg/subpath.js') or extensionless (import 'pkg/subpath') subpaths in their exports. This ensures that there is only one subpath for each exported module so that all dependents import the same consistent specifier, keeping the package contract clear for consumers and simplifying package subpath completions.

Traditionally, packages tended to use the extensionless style, which has the benefits of readability and of masking the true path of the file within the package.

With import maps now providing a standard for package resolution in browsers and other JavaScript runtimes, using the extensionless style can result in bloated import map definitions. Explicit file extensions can avoid this issue by enabling the import map to utilize a packages folder mapping to map multiple subpaths where possible instead of a separate map entry per package subpath export. This also mirrors the requirement of using the full specifier path in relative and absolute import specifiers.

## Exports sugar

If the "`.`" export is the only export, the ["`exports`"](#exports) field provides sugar for this case being the direct ["`exports`"](#exports) field value.

```json
    {
    "exports": {
        ".": "./index.js"
    }
    } 
```

can be written:

```json
    {
    "exports": "./index.js"
    }
```

## Conditional exports {#conditional-exports}

Conditional exports provide a way to map to different paths depending on certain conditions. They are supported for both CommonJS and ES module imports.

For example, a package that wants to provide different ES module exports for `require()` and `import` can be written:

```json
    // package.json
    {
    "exports": {
        "import": "./index-module.js",
        "require": "./index-require.cjs"
    },
    "type": "module"
    } 
```
