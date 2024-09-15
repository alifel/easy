# 为什么要用`import x = require("...")`

(==来源：tack overflow <https://stackoverflow.com/questions/35706164/typescript-import-as-vs-import-require>==)

These are mostly equivalent, but `import *` has some restrictions that `import ... = require` doesn't.

`import * as` creates an identifier that is a module object, emphasis on object. According to the ES6 spec, this object is never callable or newable - it only has properties. If you're trying to import a function or class, you should use

```typescript
import express = require('express');
```

or (depending on your module loader)

```typescript
import express from 'express';
```

Attempting to use `import * as express` and then invoking `express()` is always illegal according to the ES6 spec. In some runtime+transpilation environments this might happen to work anyway, but it might break at any point in the future without warning, which will make you sad.

(:pill:==**这个仅仅是原因之1，但是这个原因讲的很清晰，还有一个原因，直接用commonjs的语法，无法导出类型和导入类型，[详情](https://www.typescriptlang.org/docs/handbook/modules/reference.html#export--and-import--require)**==)
