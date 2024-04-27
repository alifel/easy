# npm run-script

## Synopsis

```shell
    npm run-script <command> [-- <args>]
    aliases: run, rum, urn
```

## Description

This runs an arbitrary command from a package's "`scripts`" object. If no "`command`" is provided, it will list the available scripts.

这个命令可以从package的`scripts`对象中执行任何的命令。如果没有`command`被提供，它将会列出可执行的scripts。

`run[-script]` is used by the test, start, restart, and stop commands, but can be called directly, as well. When the scripts in the package are printed out, they're separated into lifecycle (test, start, restart) and directly-run scripts.

`run[-script]`被test、start、restart、stop命令使用，也可以直接使用。当保重的scripts被打印时，它们被氛围生命周期scripts和可以被直接调用的scripts。

Any positional arguments are passed to the specified script. Use `--` to pass `-` -prefixed flags and options which would otherwise be parsed by npm.

任何位置参数被送入指定的script。使用 `--` 可以用来传递 “-”开头的flag和选项，否则它们将会被送入npm。

For example:

```shell
    npm run test -- --grep="pattern"
```

The arguments will only be passed to the script specified after `npm run` and not to any `pre` or `post` script.

参数将被送入`npm run`之后的script，不会送入任何`pre`或`post`script。

The `env` script is a special built-in command that can be used to list environment variables that will be available to the script at runtime. If an "env" command is defined in your package, it will take precedence over the built-in.
`env` script是一个特殊的内置命令，它可以被用来列出环境变量（能够被script在运行时可以使用的）。如果一个`env`命令定义在了你的包中，它将优先于内置的。

In addition to the shell's pre-existing `PATH`, `npm run` adds `node_modules/.bin` to the `PATH` provided to scripts. Any binaries provided by locally-installed dependencies can be used without the `node_modules/.bin` prefix. For example, if there is a `devDependency` on `tap` in your package, you should write:

除了shell中已经存在的`PATH`，`npm run`会增加 `node_modules/.bin` 到 `PATH`中（提供给scripts的）。任何二进制包被本地安装的依赖提供，可以不使用 `node_modules/.bin` 前缀。例如，如果你的包中`tap`对应这一个`devDependency`，你可以写

```shell
    "scripts": {"test": "tap test/*.js"}
```

instead of
代替

```shell
    "scripts": {"test": "node_modules/.bin/tap test/*.js"}
```

The actual shell your script is run within is platform dependent. By default, on Unix-like systems it is the `/bin/sh` command, on Windows it is `cmd.exe`. The actual shell referred to by `/bin/sh` also depends on the system. You can customize the shell with the [script-shell](https://docs.npmjs.com/cli/v10/using-npm/config#script-shell) config.

实际执行你脚本的shell时平台相关的。默认情况，类unix系统是`bin/sh`命令，winodws系统是`cmd.exe`。实际上被`/bin/sh`引用的shell也依赖系统。你可以自己定制shell用[script-shell](https://docs.npmjs.com/cli/v10/using-npm/config#script-shell) config。

Scripts are run from the root of the package folder, regardless of what the current working directory is when `npm run` is called. If you want your script to use different behavior based on what subdirectory you're in, you can use the `INIT_CWD` environment variable, which holds the full path you were in when you ran `npm run`.

scripts（当通过`npm run`被调用时）从package的根目录开始运行，忽略当前的工作目录。如果你想你的script有不同的行为，让它基于当前所在的子目录，你可以使用`INIT_CWD`环境变量，当你运行`npm run`时，它持有你当前所在的路径的完整路径。

`npm run` sets the `NODE` environment variable to the `node` executable with which `npm` is executed.

If you try to run a script without having a `node_modules` directory and it fails, you will be given a warning to run `npm install`, just in case you've forgotten.

如果你试图执行的脚本没有`node_modules`目录，它将失败，你会收到一个警告，让你运行`npm install`，以防你忘记了。***(:pill::pill::pill: 我测试，不会提示，不知道为什么。我对这句的理解是，如果`node_nodules`目录没有，这个包的`package.json`中的`script`都不能执行)***

## Workspaces support

这部分忽略，目前开发项目都没有涉及workspaces

---

==除了相关配置部分，以上是官方文档的所有内容==
