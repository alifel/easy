# module

(==来源，官方文档<https://www.typescriptlang.org/docs/handbook/modules/reference.html#export--and-import--require>==)

## `export =` and `import = require()`

When emitting CommonJS modules, TypeScript files can use a direct analog of `module.exports = ...` and `const mod = require("...")` JavaScript syntax:

当发出 CommonJS 模块时，TypeScript 文件可以使用与 JavaScript 语法中 `module.exports = ...` 和 `const mod = require("...")` 直接类似的语法：

```typescript
// @Filename: main.ts
import fs = require("fs");
export = fs.readFileSync("...");
// @Filename: main.js
"use strict";
const fs = require("fs");
module.exports = fs.readFileSync("...");
```

This syntax was used over its JavaScript counterparts since variable declarations and property assignments could not refer to TypeScript types, whereas special TypeScript syntax could:

使用这种语法而不是其 JavaScript 对应语法，是因为变量声明和属性赋值不能引用 TypeScript 类型，而特殊的 TypeScript 语法可以：

```typescript
// @Filename: a.ts
interface Options { /* ... */ }
module.exports = Options; // Error: 'Options' only refers to a type, but is being used as a value here.
export = Options; // Ok
// @Filename: b.ts
const Options = require("./a");
const options: Options = { /* ... */ }; // Error: 'Options' refers to a value, but is being used as a type here.
// @Filename: c.ts
import Options = require("./a");
const options: Options = { /* ... */ }; // Ok
```
