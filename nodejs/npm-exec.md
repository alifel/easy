# npm exec

## Synopsis

```shell
    npm exec -- <pkg>[@<version>] [args...]
    npm exec --package=<pkg>[@<version>] -- <cmd> [args...]
    npm exec -c '<cmd> [args...]'
    npm exec --package=foo -c '<cmd> [args...]'

    alias: x
```

## Descrption

This command allows you to run an arbitrary command from an npm package (either one installed locally, or fetched remotely), in a similar context as running it via `npm run`.

这个命令允许你从一个npm包中执行任意的命令（可以是本地安装的，也可以远程拉取），在一个与运行`npm run`类似的上下文中。

Run without positional arguments or `--call`, this allows you to interactively run commands in the same sort of shell environment that `package.json` scripts are run. Interactive mode is not supported in CI environments when standard input is a `TTY`, to prevent hangs.

不带位置参数或者`--call`执行，它允许你交互式的执行命令(在`package.json` scripts运行的shell环境中 ***（:pill::pill::pill:这里“在`package.json` scripts运行的shell环境”是指当scripts被`npm run`执行时，除了shell中已经存在的`PATH`，`npm run`会增加 `node_modules/.bin` 到 `PATH`中（提供给scripts的））***)。当标准输入是`TTY`时，交互模式不支持CI环境，以防止系统卡死 ***（:pill::pill::pill:这句话我是这么理解的，当标准输入是`tty`时，就会涉及人为手动交互，那么ci环境执行起来到此处时，就会一致被挂起，因为没有其他办法在tty中键入交互中需要键入的内容）***。

Whatever packages are specified by the `--package` option will be provided in the PATH of the executed command, along with any locally installed package executables. The `--package` option may be specified multiple times, to execute the supplied command in an environment where all specified packages are available.

无论什么包被`--package`指定，它将于本地其他安装的可执行包一起被提供到执行的命令的PATH中。`--package`也可以指定多次，所有被指定的包在这个命令执行的环境中都是可用的。

If any requested packages are not present in the local project dependencies, then a prompt is printed, which can be suppressed by providing either `--yes` or `--no`. When standard input is not a TTY or a CI environment is detected, `--yes` is assumed. The requested packages are installed to a folder in the npm cache, which is added to the PATH environment variable in the executed process.

如果任何被要求的包没有在本地项目的依赖中，一个提示被打印，它可以被 `--yes` 或 `--no`抑制。当标准输入不是`tty`或CI环境被探测到，`--yes`被假定。被要求的包会在npm缓存中的一个文件夹中被安装，它会被增加到PATH环境变量中，让其可用。

Package names provided without a specifier will be matched with whatever version exists in the local project. Package names with a specifier will only be considered a match if they have the exact same name and version as the local dependency.

仅仅提供了包名字没有提供其他描述符，它将在项目局部匹配存在的任何版本。包名字如果有特定的specifier，只有在本地依赖中它们有完全相同的名字和版本，被视为是匹配的。

If no `-c` or `--call` option is provided, then the positional arguments are used to generate the command string. If no `--package` options are provided, then npm will attempt to determine the executable name from the package specifier provided as the first positional argument according to the following heuristic:

如果没有`-c` 或 `--call`被提供，则位置参数被用来生成命令的字符串。如果没有 `--package`被提供，

- If the package has a single entry in its bin field in package.json, or if all entries are aliases of the same command, then that command will be used.
- If the package has multiple bin entries, and one of them matches the unscoped portion of the name field, then that command will be used.
- If this does not result in exactly one option (either because there are no bin entries, or none of them match the name of the package), then npm exec exits with an error.
To run a binary other than the named binary, specify one or more --package options, which will prevent npm from inferring the package from the first command argument.