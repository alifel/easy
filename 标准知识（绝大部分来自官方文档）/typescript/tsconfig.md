# tsconfig.json

## Root Dir - `rootDir`

(==*来源，官方文档：<https://www.typescriptlang.org/tsconfig/#rootDir>*==)

Default: The longest common path of all non-declaration input files. If `composite` is set, the default is instead the directory containing the `tsconfig.json` file.

默认值：所有非声明输入文件的最长公共路径。如果设置了 `composite`，默认值则是包含 `tsconfig.json` 文件的目录。

When TypeScript compiles files, it keeps the same directory structure in the output directory as exists in the input directory.

当 TypeScript 编译文件时，它在输出目录中保持与输入目录相同的目录结构。

For example, let’s say you have some input files:

例如，假设你有以下输入文件：

```structure
MyProj
├── tsconfig.json
├── core
│   ├── a.ts
│   ├── b.ts
│   ├── sub
│   │   ├── c.ts
├── types.d.ts
```

The inferred value for `rootDir` is the longest common path of all non-declaration input files, which in this case is `core/`.

`rootDir` 的推断值是所有非声明输入文件的最长公共路径，在这个例子中是 `core/`。(==注意:pill:：看下面的结果可知，输出目录的结构是`core/`内部的结构，而不是把`core/`这一级也放进去==)

If your `outDir` was `dist`, TypeScript would write this tree:

如果你的 `outDir` 是 `dist`，TypeScript 会生成这样的目录树：

```structure
MyProj
├── dist
│   ├── a.js
│   ├── b.js
│   ├── sub
│   │   ├── c.js
```

However, you may have intended for `core` to be part of the output directory structure. By setting rootDir: `"."` in `tsconfig.json`, TypeScript would write this tree:

然而，你可能希望 `core` 成为输出目录结构的一部分。通过在 `tsconfig.json` 中设置 `rootDir: "."`，TypeScript 会生成这样的目录树：

```structure
MyProj
├── dist
│   ├── core
│   │   ├── a.js
│   │   ├── b.js
│   │   ├── sub
│   │   │   ├── c.js
```

Importantly, `rootDir` does not affect which files become part of the compilation. It has no interaction with the `include`, `exclude`, or `files` tsconfig.json settings.

重要的是，`rootDir` 不影响哪些文件成为编译的一部分。它与 `tsconfig.json` 中的 `include`、`exclude` 或 `files` 设置没有交互。(==注意:pill:：这里非常重要==)

Note that TypeScript will never write an output file to a directory outside of `outDir`, and will never skip emitting a file. For this reason, `rootDir` also enforces that all files which need to be emitted are underneath the `rootDir` path.

注意，TypeScript 永远不会将输出文件写入 `outDir` 之外的目录，也不会跳过任何文件的编译。因此，`rootDir` 还强制要求所有需要编译的文件都位于 `rootDir` 路径下。

For example, let’s say you had this tree:

例如，假设你有这样的目录树：

```structure
MyProj
├── tsconfig.json
├── core
│   ├── a.ts
│   ├── b.ts
├── helpers.ts
```

It would be an error to specify `rootDir` as `core` and `include` as `*` because it creates a file (helpers.ts) that would need to be emitted outside the outDir (i.e. `../helpers.js`).

将 `rootDir` 指定为 `core` 并将 `include` 指定为`*`将会导致错误，因为它创建了一个需要在 outDir 之外发出的文件（helpers.ts，即 `../helpers.js`）。

## Strict - `strict`

(==*来源，官方文档：<https://www.typescriptlang.org/tsconfig/#strict>*==)

The strict flag enables a wide range of type checking behavior that results in stronger guarantees of program correctness. Turning this on is equivalent to enabling all of the strict mode family options, which are outlined below. You can then turn off individual strict mode family checks as needed.

strict 标志启用了广泛的类型检查行为，从而为程序的正确性提供更强有力的保证。开启这个选项相当于启用了所有严格模式系列的选项，这些选项在下面列出。你可以根据需要关闭个别的严格模式系列检查。

Future versions of TypeScript may introduce additional stricter checking under this flag, so upgrades of TypeScript might result in new type errors in your program. When appropriate and possible, a corresponding flag will be added to disable that behavior.

TypeScript 的未来版本可能会在这个标志下引入额外的更严格的检查，因此 TypeScript 的升级可能会在你的程序中产生新的类型错误。在适当且可能的情况下，会添加相应的标志来禁用该行为。

## Skip Lib Check - `skipLibCheck`

(==*来源，官方文档：<https://www.typescriptlang.org/tsconfig/#skipLibCheck>*==)

Skip type checking of declaration files.

跳过声明文件的类型检查。

This can save time during compilation at the expense of type-system accuracy. For example, two libraries could define two copies of the same type in an inconsistent way. Rather than doing a full check of all d.ts files, TypeScript will type check the code you specifically refer to in your app’s source code.

这可以在编译过程中节省时间，但代价是牺牲类型系统的准确性。例如，两个库可能以不一致的方式定义了同一类型的两个副本。TypeScript 不会对所有的 d.ts 文件进行完整检查，而是只对你在应用程序源代码中特别引用的代码进行类型检查。

A common case where you might think to use skipLibCheck is when there are two copies of a library’s types in your node_modules. In these cases, you should consider using a feature like yarn’s resolutions to ensure there is only one copy of that dependency in your tree or investigate how to ensure there is only one copy by understanding the dependency resolution to fix the issue without additional tooling.

一个你可能会考虑使用 skipLibCheck 的常见情况是，当你的 node_modules 中存在一个库的两个副本类型定义时。在这些情况下，你应该考虑使用像 yarn 的 resolutions 这样的功能来确保依赖树中只有一个该依赖的副本，或者通过理解依赖解析来调查如何确保只有一个副本，从而在不使用额外工具的情况下解决问题。

Another possibility is when you are migrating between TypeScript releases and the changes cause breakages in node_modules and the JS standard libraries which you do not want to deal with during the TypeScript update.

另一种可能性是当你在 TypeScript 版本之间迁移时，这些变化可能导致 node_modules 和 JS 标准库中出现你不想在 TypeScript 更新过程中处理的问题。

Note, that if these issues come from the TypeScript standard library you can replace the library using [TypeScript 4.5’s lib replacement](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-5.html#supporting-lib-from-node_modules) technique.

注意，如果这些问题来自 TypeScript 标准库，你可以使用 [TypeScript 4.5’s lib replacement](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-5.html#supporting-lib-from-node_modules)技术。

## Out Dir - `outDir`

(==*来源，官方文档：<https://www.typescriptlang.org/tsconfig/#outDir>*==)

If specified, `.js` (as well as `.d.ts`, `.js.map`, etc.) files will be emitted into this directory. The directory structure of the original source files is preserved; see `rootDir` if the computed root is not what you intended.

如果指定了 `outDir`，`.js`（以及 `.d.ts`、`.js.map` 等）文件将被输出到这个目录中。原始源文件的目录结构会被保留；如果计算得到的根目录不是你想要的，请参考 `rootDir` 选项。

If not specified, `.js` files will be emitted in the same directory as the `.ts` files they were generated from:

如果未指定 `outDir`，`.js` 文件将被输出到与生成它们的 `.ts` 文件相同的目录中：

```shell
$ tsc
example
├── index.js
└── index.ts
```

With a `tsconfig.json` like this:

使用这样的 `tsconfig.json`：

```json
{
  "compilerOptions": {
    "outDir": "./dist"
  }
}
```

Running `tsc` with these settings moves the files into the specified `dist` folder:

使用这些设置运行 `tsc` 会将文件移动到指定的 `dist` 文件夹中：

```shell
$ tsc
example
├── dist
│   └── index.js
├── index.ts
└── tsconfig.json
```

## Out File - `outFile`

(==*来源，官方文档：<https://www.typescriptlang.org/tsconfig/#outFile>*==)

If specified, all global (non-module) files will be concatenated into the single output file specified.

如果指定了 outFile，所有全局（non-module）文件将被合并到指定的单个输出文件中。

If `module` is `system` or `amd`, all module files will also be concatenated into this file after all global content.

如果 `module` 设置为 `system` 或 `amd`，所有模块文件也会在所有全局内容之后被合并到这个文件中。

Note: `outFile` cannot be used unless `module` is `None`, `System`, or `AMD`. This option cannot be used to bundle CommonJS or ES6 modules.

注意：除非 `module` 设置为 `None`、`System` 或 `AMD`，否则不能使用 `outFile`。这个选项不能用于打包 CommonJS 或 ES6 模块。

## Out - `out`

(==*来源，官方文档：<https://www.typescriptlang.org/tsconfig/#out>*==)

Use `outFile` instead.

请使用 `outFile` 替代。

The `out` option computes the final file location in a way that is not predictable or consistent. This option is retained for backward compatibility only and is deprecated.

`out` 选项计算最终文件位置的方式是不可预测且不一致的。这个选项仅为了向后兼容而保留，并且已被废弃。

## Include - `include`

(==*来源，官方文档：<https://www.typescriptlang.org/tsconfig/#include>*==)

Default:`[]` if `files` is specified; `**/*` otherwise.

如果指定了 `files`，默认值为 `[]`；否则为 `**/*`。

Specifies an array of filenames or patterns to include in the program. These filenames are resolved relative to the directory containing the `tsconfig.json` file.

指定一个文件名数组或patterns，用于包含在程序中。这些文件名是相对于包含 tsconfig.json 文件的目录来解析的。

```json
{
  "include": ["src/**/*", "tests/**/*"]
}
```

Which would include:

这将包括：

```shell
.
├── scripts                ⨯
│   ├── lint.ts            ⨯
│   ├── update_deps.ts     ⨯
│   └── utils.ts           ⨯
├── src                    ✓
│   ├── client             ✓
│   │    ├── index.ts      ✓
│   │    └── utils.ts      ✓
│   ├── server             ✓
│   │    └── index.ts      ✓
├── tests                  ✓
│   ├── app.test.ts        ✓
│   ├── utils.ts           ✓
│   └── tests.d.ts         ✓
├── package.json
├── tsconfig.json
└── yarn.lock
```

`include` and `exclude` support wildcard characters to make glob patterns:

- `*` matches zero or more characters (excluding directory separators)
- `?` matches any one character (excluding directory separators)
- `**/` matches any directory nested to any level

`include` 和 `exclude` 支持通配符来创建 `glob patterns`：

- `*` 匹配零个或多个字符（不包括目录分隔符）
- `?` 匹配任意一个字符（不包括目录分隔符）
- `**/` 匹配任意嵌套深度的任何目录

If the last path segment in a pattern does not contain a file extension or wildcard character, then it is treated as a directory, and files with supported extensions inside that directory are included (e.g. `.ts`, `.tsx`, and `.d.ts` by default, with `.js` and `.jsx` if [allowJs](https://www.typescriptlang.org/tsconfig/#allowJs) is set to true).

如果模式中的最后一个路径段不包含文件扩展名或通配符，则它被视为一个目录，该目录内具有支持的扩展名的文件将被包含（例如，默认情况下为 `.ts`、`.tsx` 和 `.d.ts`，如果设置了 [allowJs](https://www.typescriptlang.org/tsconfig/#allowJs) 为 true，则还包括 `.js` 和 `.jsx`）。

## Exclude - `exclude`

(==*来源，官方文档：<https://www.typescriptlang.org/tsconfig/#exclude>*==)

Default: `node_modules` `bower_components` `jspm_packages` `outDir`

Specifies an array of filenames or patterns that should be skipped when resolving `include`.

指定一个文件名或patterns的数组，这些文件或模式在解析 `include` 时应被跳过。

Important: `exclude` only changes which files are included as a result of the `include` setting. A file specified by `exclude` can still become part of your codebase due to an `import` statement in your code, a `types` inclusion, a `/// <reference directive`, or being specified in the `files` list.

重要提示：`exclude` 只改变由 `include` 设置包含的文件。被 `exclude` 指定的文件仍可能因为你代码中的 `import` 语句、`types` 包含、`/// <reference` 指令，或被列在 `files` 列表中而成为你代码库的一部分。

It is not a mechanism that prevents a file from being included in the codebase - it simply changes what the `include` setting finds.

这不是一个防止文件被包含在代码库中的机制 - 它只是改变了 `include` 设置所找到的内容。
