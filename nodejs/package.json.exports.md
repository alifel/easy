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

