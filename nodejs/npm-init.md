# npm init

Create a `package.json` file

创建一个 `package.json` 文件

## Synopsis

```shell
npm init <package-spec> (same as `npx <package-spec>`)
npm init <@scope> (same as `npx <@scope>/create`)

aliases: create, innit
```

## Description

`npm init <initializer>` can be used to set up a new or existing npm package.

`npm init <initializer>` 可以用来设置一个新的或者存在的 npm 包。

`initializer` in this case is an npm package named `create-<initializer>`, which will be installed by `npm-exec`, and then have its main `bin` executed -- presumably creating or updating package.json and running any other initialization-related operations.

上面例子的`initializer`是一个被命名为`create-<initializer>`的包，它将被`npm-exec`安装，然后执行它的`bin`，通常会创建或更新`package.json`，并运行其他初始化相关的操作。

The init command is transformed to a corresponding `npm exec` operation as follows:

init命令被转换成对应的`npm exec`操作：

- `npm init foo` -> `npm exec create-foo`
- `npm init @usr/foo` -> `npm exec @usr/create-foo`
- `npm init @usr` -> `npm exec @usr/create`
- `npm init @usr@2.0.0` -> `npm exec @usr/create@2.0.0`
- `npm init @usr/foo@2.0.0` -> `npm exec @usr/create-foo@2.0.0`

If the initializer is omitted (by just calling `npm init`), init will fall back to legacy init behavior. It will ask you a bunch of questions, and then write a `package.json` for you. It will attempt to make reasonable guesses based on existing fields, dependencies, and options selected. It is strictly additive, so it will keep any fields and values that were already set. You can also use `-y/--yes` to skip the questionnaire altogether. If you pass `--scope`, it will create a scoped package.

如果初始化器被忽略（仅仅通过`npm init`调用），init将回退到老的init行为。它将问你一堆问题，然后写一个`package.json`给你。它将尝试合理的基于存在的fields，dependencies和options selected进行猜测。它是严格增加的，因此它会保持我们已经设置的任何fileds和values。你也可以使用`--yes/-y`来跳过所有问题。如果你送入`--scope`，它将创建一个scoped包。

Note: if a user already has the `create-<initializer>` package globally installed, that will be what npm init uses. If you want npm to use the latest version, or another specific version you must specify it:

注意：如果用户已经有一个`create-<initializer>`包全局安装，npm init将使用它。如果您希望npm使用最新版本或另一个特定版本，则必须指定它：

- `npm init foo@latest` # fetches and runs the latest `create-foo` from the registry
- `npm init foo@1.2.3` # runs `create-foo@1.2.3` specifically

### Forwarding additional options

Any additional options will be passed directly to the command, so `npm init foo -- --hello` will map to `npm exec -- create-foo --hello`.

任意的附加选项将被直接送入命令，因此，`npm init foo -- --hello` 将映射为 `npm exec -- create-foo --hello`。

To better illustrate how options are forwarded, here's a more evolved example showing options passed to both the npm cli and a create package, both following commands are equivalent:

为了更好的阐述如何转发选项，这里有一个更复杂的例子，展示了选项被传递给 npm cli 和 create 包，这两个命令是等价的：

- `npm init foo -y --registry=<url> -- --hello -a`
- `npm exec -y --registry=<url> -- create-foo --hello -a`

## Examples

Create a new React-based project using `create-react-app`:

使用`create-react-app`创建一个新的react项目

```shell
npm init react-app ./my-react-app
```

Create a new esm-compatible package using `create-esm`:
使用`create-esm`创建一个新的esm兼容的包

```shell
mkdir my-esm-lib && cd my-esm-lib
npm init esm --yes
```

Generate a plain old package.json using legacy init:

使用legacy init生成一个普通的package.json

```shell
mkdir my-npm-pkg && cd my-npm-pkg
git init
npm init
```

Generate it without having it ask any questions:

生成一个没有询问任何问题的package.json

```shell
$ npm init -y
```

## Workspaces support

当前工作不涉及workspace，所以该部分忽略

## Configuration

该部分忽略

***以上是官方文档的所有内容***
