# main

The `main` field is a module ID that is the primary entry point to your program. That is, if your package is named `foo`, and a user installs it, and then does `require("foo")`, then your main module's exports object will be returned.

`main` field是一个模块ID（是你程序的入口点）。因此，如果你的包命名为`foo`，用户安装它，然后做`require("foo")`，你的main模块的导出对象将被返回。

This should be a module relative to the root of your package folder.

它也可以是一个模块相对于你包文件夹的根目录。

For most modules, it makes the most sense to have a main script and often not much else.

对于大多数模块来说，它最合适是拥有一个主脚本，并且通常没有其他东西。

If `main` is not set, it defaults to `index.js` in the package's root folder.

如果`main`没有设置，它默认是包根文件夹中`index.js`。

---

==以上是文档全文==

<https://docs.npmjs.com/cli/v10/configuring-npm/package-json#main>

npm version 10.5.2
