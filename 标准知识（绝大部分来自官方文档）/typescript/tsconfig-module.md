# tsconfig module

## The module output format

(==官方文档：<https://www.typescriptlang.org/docs/handbook/modules/theory.html#the-module-output-format>==)

In any project, the first question about modules we need to answer is what kinds of modules the host expects, so TypeScript can set its output format for each file to match. Sometimes, the host only supports one kind of module—ESM in the browser, or CJS in Node.js v11 and earlier, for example. Node.js v12 and later accepts both CJS and ES modules, but uses file extensions and `package.json` files to determine what format each file should be, and throws an error if the file’s contents don’t match the expected format.

在任何项目中，关于模块我们需要回答的第一个问题是主机期望的模块类型，这样 TypeScript 就能为每个文件设置匹配的输出格式。有时，主机只支持一种模块类型——例如浏览器中的 ESM，或 Node.js v11 及更早版本中的 CJS。Node.js v12 及更高版本既接受 CJS 也接受 ES 模块，但它使用文件扩展名和 `package.json` 文件来确定每个文件应该是什么格式，如果文件内容与预期格式不匹配，就会抛出错误。

The `module` compiler option provides this information to the compiler. Its primary purpose is to control the module format of any JavaScript that gets emitted during compilation, but it also serves to inform the compiler about how the module kind of each file should be detected, how different module kinds are allowed to import each other, and whether features like `import.meta` and top-level `await` are available. So, even if a TypeScript project is using `noEmit`, choosing the right setting for `module` still matters. As we established earlier, the compiler needs an accurate understanding of the module system so it can type check (and provide IntelliSense for) imports. See Choosing compiler options for guidance on choosing the right `module` setting for your project.

`module` 编译器选项向编译器提供这些信息。它的主要目的是控制编译过程中生成的任何 JavaScript 的模块格式，但它也用于告知编译器如何检测每个文件的模块类型，不同模块类型如何相互导入，以及 `import.meta` 和顶级 `await` 等特性是否可用。因此，即使 TypeScript 项目使用 `noEmit`，选择正确的 `module` 设置仍然很重要。正如我们之前确定的，编译器需要准确理解模块系统，以便能够对导入进行类型检查（并提供 IntelliSense）。

The available module settings are

- node16: Reflects the module system of Node.js v16+, which supports ES modules and CJS modules side-by-side with particular interoperability and detection rules.
- nodenext: Currently identical to `node16`, but will be a moving target reflecting the latest Node.js versions as Node.js’s module system evolves.
- es2015: Reflects the ES2015 language specification for JavaScript modules (the version that first introduced `import` and `export` to the language).
- es2020: Adds support for `import.meta` and `export * as ns from "mod"` to `es2015`.
- es2022: Adds support for top-level `await` to `es2020`.
- esnext: Currently identical to `es2022`, but will be a moving target reflecting the latest ECMAScript specifications, as well as module-related Stage 3+ proposals that are expected to be included in upcoming specification versions.
- commonjs, system, amd, and umd: Each emits everything in the module system named, and assumes everything can be successfully imported into that module system. These are no longer recommended for new projects and will not be covered in detail by this documentation.

可用的模块设置有：

- node16：反映 Node.js v16+ 的模块系统，该系统支持 ES 模块和 CJS 模块并存，并具有特定的互操作性和检测规则。
- nodenext：目前与 `node16` 相同，但将是一个反映最新 Node.js 版本的动态目标，随着 Node.js 的模块系统演变而变化。
- es2015：反映 JavaScript 模块的 ES2015 语言规范（首次将 `import` 和 `export` 引入该语言的版本）。
- es2020：在 `es2015` 的基础上增加了对 `import.meta` 和 `export * as ns from "mod"` 的支持。
- es2022：在 `es2020` 的基础上增加了对顶级 `await` 的支持。
- esnext：目前与 `es2022` 相同，但将是一个反映最新 ECMAScript 规范的动态目标，以及预计将包含在即将发布的规范版本中的与模块相关的 Stage 3+ 提案。
- commonjs, system, amd 和 umd：每个都在命名的模块系统中发出所有内容，并假设所有内容都可以成功导入到该模块系统中。这些不再推荐用于新项目，本文档将不详细介绍。

> Node.js’s rules for module format detection and interoperability make it incorrect to specify **module** as **esnext** or **commonjs** for projects that run in Node.js, even if all files emitted by tsc are ESM or CJS, respectively. The only correct module settings for projects that intend to run in Node.js are node16 and nodenext. While the emitted JavaScript for an all-ESM Node.js project might look identical between compilations using esnext and nodenext, the type checking can differ. See the reference section on nodenext for more details.
> Node.js 的模块格式检测和互操作性规则使得为在 Node.js 中运行的项目指定 **module** 为 **esnext** 或 **commonjs** 是不正确的，即使 tsc 输出的所有文件分别都是 ESM 或 CJS。对于打算在 Node.js 中运行的项目，唯一正确的模块设置是 node16 和 nodenext。虽然对于一个全 ESM 的 Node.js 项目，使用 esnext 和 nodenext 编译的输出 JavaScript 可能看起来完全相同，但类型检查可能会有所不同。有关更多详细信息，请参阅 nodenext 的参考部分。

### Module format detection

Node.js understands both ES modules and CJS modules, but the format of each file is determined by its file extension and the `type` field of the first `package.json` file found in a search of the file’s directory and all ancestor directories:

- `.mjs` and `.cjs` files are always interpreted as ES modules and CJS modules, respectively.
- `.js` files are interpreted as ES modules if the nearest `package.json` file contains a `type` field with the value `"module"`. If there is no `package.json` file, or if the `type` field is missing or has any other value, `.js` files are interpreted as CJS modules.

Node.js 能理解 ES 模块和 CJS 模块，但每个文件的格式由其文件扩展名和在文件目录及所有祖先目录搜索中找到的第一个 `package.json` 文件的 `type` 字段决定：

- `.mjs` 和 `.cjs` 文件分别始终被解释为 ES 模块和 CJS 模块。
- 如果最近的 `package.json` 文件包含值为 `"module"` 的 `type` 字段，则 `.js` 文件被解释为 ES 模块。如果没有 `package.json` 文件，或者 `type` 字段缺失或有任何其他值，`.js` 文件被解释为 CJS 模块。

If a file is determined to be an ES `module` by these rules, Node.js will not inject the CommonJS module and `require` objects into the file’s scope during evaluation, so a file that tries to use them will cause a crash. Conversely, if a file is determined to be a CJS module by these rules, `import` and `export` declarations in the file will cause a syntax error crash.

如果根据这些规则确定一个文件是 ES `module`，Node.js 在评估过程中不会将 CommonJS 模块和 `require` 对象注入到文件的作用域中，因此尝试使用它们的文件会导致崩溃。相反，如果根据这些规则确定一个文件是 CJS 模块，文件中的 `import` 和 `export` 声明将导致语法错误崩溃。

When the `module` compiler option is set to `node16` or `nodenext`, TypeScript applies this same algorithm to the project’s input files to determine the module kind of each corresponding output file. Let’s look at how module formats are detected in an example project that uses `--module nodenext`:

当 `module` 编译器选项设置为 `node16` 或 `nodenext` 时，TypeScript 会将这个相同的算法应用于项目的输入文件，以确定每个相应输出文件的模块类型。让我们看看在一个使用 `--module nodenext` 的示例项目中，模块格式是如何被检测的：

|Input file name|Contents|Output file name|Module kind|Reason|
|---|---|---|---|---|
|/package.json|{}||||
|/main.mts||/main.mjs|ESM|File extension|
|/utils.cts||/utils.cjs|CJS|File extension|
|/example.ts||/example.js|CJS|No "type": "module" in package.json|
|/node_modules/pkg/package.json|{ "type": "module" }||||
|/node_modules/pkg/index.d.ts|||ESM|"type": "module" in package.json|
|/node_modules/pkg/index.d.cts|||CJS|File extension|

When the input file extension is `.mts` or `.cts`, TypeScript knows to treat that file as an ES module or CJS module, respectively, because Node.js will treat the output `.mjs` file as an ES module or the output `.cjs` file as a CJS module. When the input file extension is `.ts`, TypeScript has to consult the nearest `package.json` file to determine the module format, because this is what Node.js will do when it encounters the output `.js` file. (Notice that the same rules apply to the `.d.cts` and `.d.ts` declaration files in the `pkg` dependency: though they will not produce an output file as part of this compilation, the presence of a `.d.ts` file implies the existence of a corresponding `.js` file—perhaps created when the author of the `pkg` library ran `tsc` on an input `.ts` file of their own—which Node.js must interpret as an ES module, due to its `.js` extension and the presence of the `"type": "module"` field in `/node_modules/pkg/package.json`. Declaration files are covered in more detail in a [later section](https://www.typescriptlang.org/docs/handbook/modules/theory.html#the-role-of-declaration-files).)

当输入文件扩展名为 `.mts` 或 `.cts` 时，TypeScript 知道将该文件分别视为 ES 模块或 CJS 模块，因为 Node.js 会将输出的 `.mjs` 文件视为 ES 模块或将输出的 `.cjs` 文件视为 CJS 模块。当输入文件扩展名为 `.ts` 时，TypeScript 必须查询最近的 `package.json` 文件来确定模块格式，因为这是 Node.js 在遇到输出的 `.js` 文件时会做的事情。（注意，相同的规则也适用于 `pkg` 依赖中的 `.d.cts` 和 `.d.ts` 声明文件：尽管它们不会作为此编译的一部分生成输出文件，但 `.d.ts` 文件的存在暗示了相应的 `.js` 文件的存在——可能是 `pkg` 库的作者对他们自己的输入 `.ts` 文件运行 `tsc` 时创建的——Node.js 必须将其解释为 ES 模块，这是由于其 `.js` 扩展名和 `/node_modules/pkg/package.json` 中存在 `"type": "module"` 字段。声明文件在[后面的章节](https://www.typescriptlang.org/docs/handbook/modules/theory.html#the-role-of-declaration-files)中有更详细的介绍。）

The detected module format of input files is used by TypeScript to ensure it emits the output syntax that Node.js expects in each output file. If TypeScript were to emit `/example.js` with `import` and `export` statements in it, Node.js would crash when parsing the file. If TypeScript were to emit `/main.mjs` with `require` calls, Node.js would crash during evaluation. Beyond emit, the module format is also used to determine rules for type checking and module resolution, which we’ll discuss in the following sections.

TypeScript 使用检测到的输入文件的模块格式来确保它发出 Node.js 在每个输出文件中期望的输出语法。如果 TypeScript 发出带有 `import` 和 `export` 语句的 `/example.js`，Node.js 在解析文件时会崩溃。如果 TypeScript 发出带有 `require` 调用的 `/main.mjs`，Node.js 在评估时会崩溃。除了发出之外，模块格式还用于确定类型检查和模块解析的规则，我们将在接下来的章节中讨论这些内容。

It’s worth mentioning again that TypeScript’s behavior in `--module node16` and `--module nodenext` is entirely motivated by Node.js’s behavior. Since TypeScript’s goal is to catch potential runtime errors at compile time, it needs a very accurate model of what will happen at runtime. This fairly complex set of rules for module kind detection is necessary for checking code that will run in Node.js, but may be overly strict or just incorrect if applied to non-Node.js hosts.

值得再次提及的是，TypeScript 在 `--module node16` 和 `--module nodenext` 中的行为完全是由 Node.js 的行为驱动的。由于 TypeScript 的目标是在编译时捕获潜在的运行时错误，它需要一个非常准确的运行时行为模型。这套相当复杂的模块类型检测规则对于检查将在 Node.js 中运行的代码是必要的，但如果应用于非 Node.js 主机，可能会过于严格或simply不正确。

### Input module syntax

It’s important to note that the input module syntax seen in input source files is somewhat decoupled from the output module syntax emitted to JS files. That is, a file with an ESM import:

需要注意的是，在输入源文件中看到的输入模块语法与发出到 JS 文件的输出模块语法在某种程度上是解耦的。也就是说，一个带有 ESM 导入的文件：

```typescript
import { sayHello } from "greetings";
sayHello("world");
```

might be emitted in ESM format exactly as-is, or might be emitted as CommonJS:

```javascript
Object.defineProperty(exports, "__esModule", { value: true });
const greetings_1 = require("greetings");
(0, greetings_1.sayHello)("world");
```

depending on the `module` compiler option (and any applicable [module format detection](https://www.typescriptlang.org/docs/handbook/modules/theory.html#module-format-detection) rules, if the module option supports more than one kind of module). In general, this means that looking at the contents of an input file isn’t enough to determine whether it’s an ES module or a CJS module.

这取决于 module 编译器选项（以及任何适用的模块格式检测规则，如果该模块选项支持多种类型的模块）。总的来说，这意味着仅仅查看输入文件的内容是不足以确定它是 ES 模块还是 CJS 模块的。

> Today, most TypeScript files are authored using ESM syntax (import and export statements) regardless of the output format. This is largely a legacy of the long road ESM has taken to widespread support. ECMAScript modules were standardized in 2015, were supported in most browsers by 2017, and landed in Node.js v12 in 2019. During much of this window, it was clear that ESM was the future of JavaScript modules, but very few runtimes could consume it. Tools like Babel made it possible for JavaScript to be authored in ESM and downleveled to another module format that could be used in Node.js or browsers. TypeScript followed suit, adding support for ES module syntax and softly discouraging the use of the original CommonJS-inspired **import fs = require("fs")** syntax in the [1.5 release](https://devblogs.microsoft.com/typescript/announcing-typescript-1-5/).
>
>The upside of this “author ESM, output anything” strategy was that TypeScript could use standard JavaScript syntax, making the authoring experience familiar to newcomers, and (theoretically) making it easy for projects to start targeting ESM outputs in the future. There are three significant downsides, which became fully apparent only after ESM and CJS modules were allowed to coexist and interoperate in Node.js:
>
> 1. Early assumptions about how ESM/CJS interoperability would work in Node.js turned out to be wrong, and today, interoperability rules differ between Node.js and bundlers. Consequently, the configuration space for modules in TypeScript is large.
> 2. When the syntax in input files all looks like ESM, it’s easy for an author or code reviewer to lose track of what kind of module a file is at runtime. And because of Node.js’s interoperability rules, what kind of module each file is became very important.
> 3. When input files are written in ESM, the syntax in type declaration outputs (.d.ts files) looks like ESM too. But because the corresponding JavaScript files could have been emitted in any module format, TypeScript can’t tell what kind of module a file is just by looking at the contents of its type declarations. And again, because of the nature of ESM/CJS interoperability, TypeScript has to know what kind of module everything is in order to provide correct types and prevent imports that will crash.
>
> In TypeScript 5.0, a new compiler option called verbatimModuleSyntax was introduced to help TypeScript authors know exactly how their import and export statements will be emitted. When enabled, the flag requires imports and exports in input files to be written in the form that will undergo the least amount of transformation before emit. So if a file will be emitted as ESM, imports and exports must be written in ESM syntax; if a file will be emitted as CJS, it must be written in the CommonJS-inspired TypeScript syntax (import fs = require("fs") and export = {}). This setting is particularly recommended for Node.js projects that use mostly ESM, but have a select few CJS files. It is not recommended for projects that currently target CJS, but may want to target ESM in the future.

### ESM and CJS interoperability

Can an ES module `import` a CommonJS module? If so, does a default import link to `exports` or `exports.default`? Can a CommonJS module `require` an ES module? CommonJS isn’t part of the ECMAScript specification, so runtimes, bundlers, and transpilers have been free to make up their own answers to these questions since ESM was standardized in 2015, and as such no standard set of interoperability rules exist. Today, most runtimes and bundlers broadly fall into one of three categories:

1. **ESM-only**. Some runtimes, like browser engines, only support what’s actually a part of the language: ECMAScript Modules.
2. **Bundler-like**. Before any major JavaScript engine could run ES modules, Babel allowed developers to write them by transpiling them to CommonJS. The way these ESM-transpiled-to-CJS files interacted with hand-written-CJS files implied a set of permissive interoperability rules that have become the de facto standard for bundlers and transpilers.
3. **Node.js**. In Node.js, CommonJS modules cannot load ES modules synchronously (with `require`); they can only load them asynchronously with dynamic `import()` calls. ES modules can default-import CJS modules, which always binds to `exports`. (This means that a default import of a Babel-like CJS output with `__esModule` behaves differently between Node.js and some bundlers.)

ES 模块可以 `import` CommonJS 模块吗？如果可以，默认导入是链接到 `exports` 还是 `exports.default`？CommonJS 模块可以 `require` ES 模块吗？CommonJS 不是 ECMAScript 规范的一部分，所以自 2015 年 ESM 标准化以来，运行时、打包器和转译器可以自由地为这些问题制定自己的答案，因此不存在标准的互操作性规则集。今天，大多数运行时和打包器大致分为以下三类：

1. **仅 ESM**。一些运行时，如浏览器引擎，只支持实际上是语言一部分的内容：ECMAScript 模块。
2. **类打包器**。在任何主要的 JavaScript 引擎能够运行 ES 模块之前，Babel 允许开发者通过将它们转译为 CommonJS 来编写它们。这些转译为 CJS 的 ESM 文件与手写的 CJS 文件交互的方式暗示了一套宽松的互操作性规则，这已成为打包器和转译器的事实标准。
3. **Node.js**。在 Node.js 中，CommonJS 模块不能同步加载 ES 模块（使用 `require`）；它们只能通过动态 `import()` 调用异步加载它们。ES 模块可以默认导入 CJS 模块，这总是绑定到 `exports`。（这意味着默认导入带有 `__esModule` 的类 Babel 的 CJS 输出在 Node.js 和某些打包器之间的行为不同。）

TypeScript needs to know which of these rule sets to assume in order to provide correct types on (particularly `default`) imports and to error on imports that will crash at runtime. When the `module` compiler option is set to `node16` or `nodenext`, Node.js’s rules are enforced. All other `module` settings, combined with the [esModuleInterop](https://www.typescriptlang.org/docs/handbook/modules/reference.html#esModuleInterop) option, result in bundler-like interop in TypeScript. (While using `--module esnext` does prevent you from writing CommonJS modules, it does not prevent you from importing them as dependencies. There’s currently no TypeScript setting that can guard against an ES module importing a CommonJS module, as would be appropriate for direct-to-browser code.)

TypeScript 需要知道应该假设哪一套规则，以便在（特别是 `default`）导入上提供正确的类型，并对运行时会崩溃的导入报错。当 `module` 编译器选项设置为 `node16` 或 `nodenext` 时，会强制执行 Node.js 的规则。所有其他 `module` 设置，结合 [esModuleInterop](https://www.typescriptlang.org/docs/handbook/modules/reference.html#esModuleInterop) 选项，会在 TypeScript 中产生类打包器的互操作性。（虽然使用 `--module esnext` 确实可以防止你编写 CommonJS 模块，但它不会阻止你将它们作为依赖项导入。目前没有 TypeScript 设置可以防止 ES 模块导入 CommonJS 模块，这对于直接面向浏览器的代码来说是合适的。）

### Module specifiers are not transformed

While the `module` compiler option can transform imports and exports in input files to different module formats in output files, the module specifier (the string `from` which you `import`, or pass to `require`) is always emitted as-written. For example, an input like:

虽然 `module` 编译器选项可以将输入文件中的导入和导出转换为输出文件中的不同模块格式，但模块说明符（你用来 `import` 的字符串 `from`，或传递给 `require` 的字符串）总是按原样发出。例如，对于这样的输入：

```typescript
import { add } from "./math.mjs";
add(1, 2);
```

might be emitted as either:

```javascript
import { add } from "./math.mjs";
add(1, 2);
```

or:

```javascript
const math_1 = require("./math.mjs");
math_1.add(1, 2);
```

depending on the `module` compiler option, but the module specifier will always be `"./math.mjs"`. There is no compiler option that enables transforming, substituting, or rewriting module specifiers. Consequently, module specifiers must be written in a way that works for the code’s target runtime or bundler, and it’s TypeScript’s job to understand those output-relative specifiers. The process of finding the file referenced by a module specifier is called module resolution.

这取决于 `module` 编译器选项，但模块说明符始终会是 `"./math.mjs"`。没有编译器选项可以启用转换、替换或重写模块说明符。因此，模块说明符必须以适用于代码目标运行时或打包器的方式编写，而 TypeScript 的任务是理解这些相对于输出的说明符。通过模块说明符查找所引用文件的过程称为模块解析。（:pill:==**重点：typescript不会对模块描述符进行任何转换**==）



## The module compiler option

(==来源：官方文档<https://www.typescriptlang.org/docs/handbook/modules/reference.html#the-module-compiler-option>==)

This section discusses the details of each `module` compiler option value. See the [Module output format](https://www.typescriptlang.org/docs/handbook/modules/theory.html#the-module-output-format) theory section for more background on what the option is and how it fits into the overall compilation process. In brief, the `module` compiler option was historically only used to control the output module format of emitted JavaScript files. The more recent `node16` and `nodenext` values, however, describe a wide range of characteristics of Node.js’s module system, including what module formats are supported, how the module format of each file is determined, and how different module formats interoperate.

本节讨论每个 `module` 编译器选项值的详细信息。有关该选项的更多背景信息以及它如何适应整个编译过程，请参阅[模块输出格式](https://www.typescriptlang.org/docs/handbook/modules/theory.html#the-module-output-format)理论部分。简而言之，`module` 编译器选项历史上仅用于控制发出的 JavaScript 文件的输出模块格式。然而，最近的 `node16` 和 `nodenext` 值描述了 Node.js 模块系统的广泛特征，包括支持哪些模块格式、如何确定每个文件的模块格式，以及不同模块格式如何互操作。

### `node16`, `nodenext`

Node.js supports both CommonJS and ECMAScript modules, with specific rules for which format each file can be and how the two formats are allowed to interoperate. `node16` and `nodenext` describe the full range of behavior for Node.js’s dual-format module system, and **emit files in either CommonJS or ESM format**. This is different from every other `module` option, which are runtime-agnostic and force all output files into a single format, leaving it to the user to ensure the output is valid for their runtime.

Node.js 支持 CommonJS 和 ECMAScript 模块，对于每个文件可以采用哪种格式以及两种格式如何允许互操作有特定的规则。`node16` 和 `nodenext` 描述了 Node.js 双格式模块系统的全部行为范围，并**以 CommonJS 或 ESM 格式发出文件**。这与其他所有 `module` 选项不同，其他选项都是与运行时无关的，并强制所有输出文件采用单一格式，由用户确保输出对其运行时有效。

> A common misconception is that node16 and nodenext only emit ES modules. In reality, node16 and nodenext describe versions of Node.js that support ES modules, not just projects that use ES modules. Both ESM and CommonJS emit are supported, based on the [detected module format](https://www.typescriptlang.org/docs/handbook/modules/reference.html#module-format-detection) of each file. Because node16 and nodenext are the only module options that reflect the complexities of Node.js’s dual module system, they are the **only correct module options** for all apps and libraries that are intended to run in Node.js v12 or later, whether they use ES modules or not.
> 一个常见的误解是 node16 和 nodenext 只发出 ES 模块。实际上，node16 和 nodenext 描述的是支持 ES 模块的 Node.js 版本，而不仅仅是使用 ES 模块的项目。基于[检测到的每个文件的模块格式](https://www.typescriptlang.org/docs/handbook/modules/reference.html#module-format-detection)，ESM 和 CommonJS 的发出都是支持的。因为 node16 和 nodenext 是唯一反映 Node.js 双模块系统复杂性的模块选项，它们是**所有打算在 Node.js v12 或更高版本中运行的应用程序和库的唯一正确模块选项**，无论它们是否使用 ES 模块。

`node16` and `nodenext` are currently identical, with the exception that they [imply different target option values](https://www.typescriptlang.org/docs/handbook/modules/reference.html#implied-and-enforced-options). If Node.js makes significant changes to its module system in the future, `node16` will be frozen while `nodenext` will be updated to reflect the new behavior.

`node16` 和 `nodenext` 目前是相同的，除了它们[暗示不同的目标选项值](https://www.typescriptlang.org/docs/handbook/modules/reference.html#implied-and-enforced-options)。如果 Node.js 在未来对其模块系统进行重大更改，`node16` 将保持不变，而 `nodenext` 将更新以反映新的行为。

#### **Module format detection**

- `.mts`/`.mjs`/`.d.mts` files are always ES modules.
- `.cts`/`.cjs`/`.d.cts` files are always CommonJS modules.
- `.ts`/`.tsx`/`.js`/`.jsx`/`.d.ts` files are ES modules if the nearest ancestor package.json - file contains `"type": "module"`, otherwise CommonJS modules.

- `.mts`/`.mjs`/`.d.mts` 文件始终是 ES 模块。
- `.cts`/`.cjs`/`.d.cts` 文件始终是 CommonJS 模块。
- `.ts`/`.tsx`/`.js`/`.jsx`/`.d.ts` 文件如果最近的祖先 package.json 文件包含 `"type": "module"`，则为 ES 模块，否则为 CommonJS 模块。

The detected module format of input `.ts`/`.tsx`/`.mts`/`.cts` files determines the module format of the emitted JavaScript files. So, for example, a project consisting entirely of `.ts` files will emit all CommonJS modules by default under `--module nodenext`, and can be made to emit all ES modules by adding `"type": "module"` to the project package.json.

输入的 `.ts`/`.tsx`/`.mts`/`.cts` 文件的检测到的模块格式决定了发出的 JavaScript 文件的模块格式。因此，例如，一个完全由 `.ts` 文件组成的项目在 `--module nodenext` 下默认会发出所有 CommonJS 模块，而通过在项目的 package.json 中添加 `"type": "module"` 可以使其发出所有 ES 模块。

#### Interoperability rules

- When an ES module references a CommonJS module:
  - The `module.exports` of the CommonJS module is available as a default import to the ES module.
  - Properties (other than `default`) of the CommonJS module’s `module.exports` may or may not be available as named imports to the ES module. Node.js attempts to make them available via [static analysis](https://github.com/nodejs/cjs-module-lexer). TypeScript cannot know from a declaration file whether that static analysis will succeed, and optimistically assumes it will. This limits TypeScript’s ability to catch named imports that may crash at runtime. See [#54018](https://github.com/microsoft/TypeScript/issues/54018) for more details.
- When a CommonJS module references an ES module:
  - `require` cannot reference an ES module. For TypeScript, this includes `import` statements in files that are [detected](https://www.typescriptlang.org/docs/handbook/modules/reference.html#module-format-detection) to be CommonJS modules, since those `import` statements will be transformed to `require` calls in the emitted JavaScript.
  - A dynamic `import()` call may be used to import an ES module. It returns a Promise of the module’s Module Namespace Object (what you’d get from `import * as ns from "./module.js"` from another ES module).

- 当 ES 模块引用 CommonJS 模块时：
  - CommonJS 模块的 `module.exports` 可作为 ES 模块的默认导入使用。
  - CommonJS 模块的 `module.exports` 的属性（除了 `default`）可能会或可能不会作为命名导入提供给 ES 模块。Node.js 尝试通过[静态分析](https://github.com/nodejs/cjs-module-lexer)使它们可用。TypeScript 无法从声明文件中知道该静态分析是否会成功，并乐观地假设它会成功。这限制了 TypeScript 捕获可能在运行时崩溃的命名导入的能力。更多详情请参见 [#54018](https://github.com/microsoft/TypeScript/issues/54018)。
- 当 CommonJS 模块引用 ES 模块时：
  - `require` 不能引用 ES 模块。对于 TypeScript 来说，这包括在[检测](https://www.typescriptlang.org/docs/handbook/modules/reference.html#module-format-detection)为 CommonJS 模块的文件中的 `import` 语句，因为这些 `import` 语句在发出的 JavaScript 中会被转换为 `require` 调用。
  - 可以使用动态 `import()` 调用来导入 ES 模块。它返回模块的模块命名空间对象的 Promise（相当于从另一个 ES 模块中使用 `import * as ns from "./module.js"` 得到的结果）。

#### Emit

The emit format of each file is determined by the [detected module format](https://www.typescriptlang.org/docs/handbook/modules/reference.html#module-format-detection) of each file. ESM emit is similar to [--module esnext](https://www.typescriptlang.org/docs/handbook/modules/reference.html#es2015-es2020-es2022-esnext), but has a special transformation for `import x = require("...")`, which is not allowed in `--module esnext`:

每个文件的发出格式由[检测到的每个文件的模块格式](https://www.typescriptlang.org/docs/handbook/modules/reference.html#module-format-detection)决定。ESM 发出类似于 [--module esnext](https://www.typescriptlang.org/docs/handbook/modules/reference.html#es2015-es2020-es2022-esnext)，但对 `import x = require("...")` 有特殊的转换，这在 `--module esnext` 中是不允许的：

```typescript
// @Filename: main.ts
import x = require("mod");
```

```javascript
// @Filename: main.js
import { createRequire as _createRequire } from "module";
const __require = _createRequire(import.meta.url);
const x = __require("mod");
```

CommonJS emit is similar to [--module commonjs](https://www.typescriptlang.org/docs/handbook/modules/reference.html#commonjs), but dynamic `import()` calls are not transformed. Emit here is shown with `esModuleInterop` enabled:

CommonJS 发出类似于 [--module commonjs](https://www.typescriptlang.org/docs/handbook/modules/reference.html#commonjs)，但动态 `import()` 调用不会被转换。这里显示的发出启用了 `esModuleInterop`：

```typescript
// @Filename: main.ts
import fs from "fs"; // transformed
const dynamic = import("mod"); // not transformed
```

```javascript
// @Filename: main.js
"use strict";
var __importDefault = (this && this.__importDefault) || function (mod) {
    return (mod && mod.__esModule) ? mod : { "default": mod };
};
Object.defineProperty(exports, "__esModule", { value: true });
const fs_1 = __importDefault(require("fs")); // transformed
const dynamic = import("mod"); // not transformed
```

#### Implied and enforced options

- `--module nodenext` or `node16` implies and enforces the `moduleResolution` with the same name.
- `--module nodenext` implies `--target esnext`.
- `--module node16` implies `--target es2022`.
- `--module nodenext` or `node16` implies `--esModuleInterop`.

#### 隐含和强制执行的选项

- `--module nodenext` 或 `node16` 隐含并强制执行同名的 `moduleResolution`。
- `--module nodenext` 隐含 `--target esnext`。
- `--module node16` 隐含 `--target es2022`。
- `--module nodenext` 或 `node16` 隐含 `--esModuleInterop`。

#### Summary

- `node16` and `nodenext` are the only correct module options for all apps and libraries that are intended to run in Node.js v12 or later, whether they use ES modules or not.
- `node16` and `nodenext` emit files in either CommonJS or ESM format, based on the [detected module format](https://www.typescriptlang.org/docs/handbook/modules/reference.html#module-format-detection) of each file.
- Node.js’s interoperability rules between ESM and CJS are reflected in type checking.
- ESM emit transforms `import x = require("...")` to a `require` call constructed from a `createRequire` import.
- CommonJS emit leaves dynamic `import()` calls untransformed, so CommonJS modules can asynchronously import ES modules.

#### 总结

- 对于所有打算在 Node.js v12 或更高版本中运行的应用程序和库，无论它们是否使用 ES 模块，`node16` 和 `nodenext` 都是唯一正确的模块选项。
- `node16` 和 `nodenext` 根据[检测到的每个文件的模块格式](https://www.typescriptlang.org/docs/handbook/modules/reference.html#module-format-detection)，以 CommonJS 或 ESM 格式发出文件。
- Node.js 在 ESM 和 CJS 之间的互操作性规则在类型检查中得到反映。
- ESM 发出将 `import x = require("...")` 转换为由 `createRequire` 导入构造的 `require` 调用。
- CommonJS 发出保持动态 `import()` 调用不变，因此 CommonJS 模块可以异步导入 ES 模块。
