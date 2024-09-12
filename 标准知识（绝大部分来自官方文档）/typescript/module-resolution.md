# Module resolution

Let’s return to our first example and review what we’ve learned about it so far:

让我们回到第一个例子，回顾一下我们到目前为止学到的内容：

```ts
import sayHello from "greetings";
sayHello("world");
```

So far, we’ve discussed how the host’s module system and TypeScript’s `module` compiler option might impact this code. We know that the input syntax looks like ESM, but the output format depends on the `module` compiler option, potentially the file extension, and `package.json` `"type"` field. We also know that what `sayHello` gets bound to, and even whether the import is even allowed, may vary depending on the module kinds of this file and the target file. But we haven’t yet discussed how to find the target file.

到目前为止，我们已经讨论了宿主的模块系统和 TypeScript 的 `module` 编译选项可能如何影响这段代码。我们知道输入语法看起来像 ESM，但输出格式取决于 `module` 编译选项，可能还取决于文件扩展名和 `package.json` 中的 `"type"` 字段。我们还知道 `sayHello` 绑定到什么，以及导入是否被允许，可能会因该文件和目标文件的模块类型而有所不同。但我们还没有讨论如何找到目标文件。

## Module resolution is host-defined

While the ECMAScript specification defines how to parse and interpret `import` and `export` statements, it leaves module resolution up to the host. If you’re creating a hot new JavaScript runtime, you’re free to create a module resolution scheme like:

```ts
import monkey from "🐒"; // Looks for './eats/bananas.js'
import cow from "🐄";    // Looks for './eats/grass.js'
import lion from "🦁";   // Looks for './eats/you.js'
```

and still claim to implement “standards-compliant ESM.” Needless to say, TypeScript would have no idea what types to assign to `monkey`, `cow`, and `lion` without built-in knowledge of this runtime’s module resolution algorithm. Just as module informs the compiler about the host’s expected module format, `moduleResolution`, along with a few customization options, specify the algorithm the host uses to resolve module specifiers to files. This also clarifies why TypeScript doesn’t modify import specifiers during emit: the relationship between an import specifier and a file on disk (if one even exists) is host-defined, and TypeScript is not a host.

The available moduleResolution options are:

- classic: TypeScript’s oldest module resolution mode, this is unfortunately the default when module is set to anything other than commonjs, node16, or nodenext. It was probably made to provide best-effort resolution for a wide range of RequireJS configurations. It should not be used for new projects (or even old projects that don’t use RequireJS or another AMD module loader), and is scheduled for deprecation in TypeScript 6.0.
- node10: Formerly known as node, this is the unfortunate default when module is set to commonjs. It’s a pretty good model of Node.js versions older than v12, and sometimes it’s a passable approximation of how most bundlers do module resolution. It supports looking up packages from node_modules, loading directory index.js files, and omitting .js extensions in relative module specifiers. Because Node.js v12 introduced different module resolution rules for ES modules, though, it’s a very bad model of modern versions of Node.js. It should not be used for new projects.
- node16: This is the counterpart of --module node16 and is set by default with that module setting. Node.js v12 and later support both ESM and CJS, each of which uses its own module resolution algorithm. In Node.js, module specifiers in import statements and dynamic import() calls are not allowed to omit file extensions or /index.js suffixes, while module specifiers in require calls are. This module resolution mode understands and enforces this restriction where necessary, as determined by the module format detection rules instated by --module node16. (For node16 and nodenext, module and moduleResolution go hand-in-hand: setting one to node16 or nodenext while setting the other to something else has unsupported behavior and may be an error in the future.)
- nodenext: Currently identical to node16, this is the counterpart of --module nodenext and is set by default with that module setting. It’s intended to be a forward-looking mode that will support new Node.js module resolution features as they’re added.
- bundler: Node.js v12 introduced some new module resolution features for importing npm packages—the "exports" and "imports" fields of package.json—and many bundlers adopted those features without also adopting the stricter rules for ESM imports. This module resolution mode provides a base algorithm for code targeting a bundler. It supports package.json "exports" and "imports" by default, but can be configured to ignore them. It requires setting module to esnext.

## TypeScript imitates the host’s module resolution, but with types
