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

如果没有`-c` 或 `--call`被提供，则位置参数被用来生成命令的字符串。如果没有 `--package`被提供，npm将会尝试通过第一个位置参数提供的包说明符确定可执行的文件名字，具体按下面的启发方法：

- If the package has a single entry in its `bin` field in `package.json`, or if all entries are aliases of the same command, then that command will be used.
如果包在其`package.json`中的`bin`域中有单一的入口，或者所有入口都是相同命令的别名，则该命令被执行
- If the package has multiple `bin` entries, and one of them matches the unscoped portion of the `name` field, then that command will be used.
如果包有多个`bin`入口，它们中的一个匹配了`name`域的非scoped部分，这个命令会被执行。
- If this does not result in exactly one option (either because there are no bin entries, or none of them match the `name` of the package), then npm exec exits with an error.
如果没有得出一个具体的选项（要么因为没有`bin`入口，要么他们中没有任何一项匹配包的`name`），npm则带着一个error退出。

To run a binary other than the named binary, specify one or more `--package` options, which will prevent npm from inferring the package from the first command argument.

执行一个二进制文件而不是被命名的二进制文件，指定一个或多个`--package`选项，这会阻止npm从第一个命令参数中推断出包。

## `npx` vs `npm exec`

When run via the `npx` binary, all flags and options must be set prior to any positional arguments. When run via `npm exec`, a double-hyphen `--` flag can be used to suppress npm's parsing of switches and options that should be sent to the executed command.

当通过`npx`二进制文件运行时，所有选项和参数必须在位置参数之前设置。当通过`npm exec`运行时，双短划线`--`标志可以用来抑制npm的解析选项和参数，这些选项和参数应该被发送到被执行的命令。

For example:

`$ npx foo@latest bar --package=@npmcli/foo`
In this case, npm will resolve the foo package name, and run the following command:

在此例中，npm将解析foo包名称，并运行以下命令：

`$ foo bar --package=@npmcli/foo`

Since the `--package` option comes after the positional arguments, it is treated as an argument to the executed command.

因为`--package`选项在位置参数之后，所以它被当作被执行的命令的参数。

In contrast, due to npm's argument parsing logic, running this command is different:

相反，由于npm的参数解析逻辑，运行这个命令是不同的：

`$ npm exec foo@latest bar --package=@npmcli/foo`

In this case, npm will parse the `--package` option first, resolving the `@npmcli/foo` package. Then, it will execute the following command in that context:

在此例中，npm会先解析`--package`选项，解析出`@npmcli/foo`包。然后，它会在这个上下文中执行以下命令：

`$ foo@latest bar`

The double-hyphen character is recommended to explicitly tell npm to stop parsing command line options and switches. The following command would thus be equivalent to the npx command above:

双横线字符是被推荐的用来明确告诉npm停止解析命令行选项和开关。因此，以下命令与上面的`npx`命令是等价的：

`$ npm exec -- foo@latest bar --package=@npmcli/foo`

## Configuration

`package`

- Default:
- Type: String (can be set multiple times)

The package or packages to install for `npm exec`

`call`

- Default: ""
- Type: String

Optional companion option for `npm exec`, `npx` that allows for specifying a custom command to be run along with the installed packages.

```shell
    npm exec --package yo --package generator-node --call "yo node"
```

`workspace`

- Default:
- Type: String (can be set multiple times)

Enable running a command in the context of the configured workspaces of the current project while filtering by running only the workspaces defined by this configuration option.

Valid values for the `workspace` config are either:

- Workspace names
- Path to a workspace directory
- Path to a parent workspace directory (will result in selecting all workspaces within that folder)

When set for the npm init command, this may be set to the folder of a workspace which does not yet exist, to create the folder and set it up as a brand new workspace within the project.

This value is not exported to the environment for child processes.

`workspaces`

- Default: null
- Type: null or Boolean

Set to true to run the command in the context of all configured workspaces.

Explicitly setting this to false will cause commands like install to ignore workspaces altogether. When not set explicitly:

- Commands that operate on the node_modules tree (install, update, etc.) will link workspaces into the node_modules folder. - Commands that do other things (test, exec, publish, etc.) will operate on the root project, unless one or more workspaces are specified in the workspace config.
This value is not exported to the environment for child processes.

`include-workspace-root`

- Default: false
- Type: Boolean

Include the workspace root when workspaces are enabled for a command.

When false, specifying individual workspaces via the workspace config, or all workspaces via the workspaces flag, will cause npm to operate only on the specified workspaces, and not on the root project.

This value is not exported to the environment for child processes.

## Examples

Run the version of `tap` in the local dependencies, with the provided arguments:

运行本地依赖中的`tap`的版本，并使用提供的参数：

```shell
$ npm exec -- tap --bail test/foo.js
# ~ or ~
$ npx tap --bail test/foo.js
```

Run a command other than the command whose name matches the package name by specifying a `--package` option:

运行一个命令，而不是命令名称与包（通过指定一个`--package`选项）名称匹配的命令：

```shell
$ npm exec --package=foo -- bar --bar-argument
# ~ or ~
$ npx --package=foo bar --bar-argument
```

Run an arbitrary shell script, in the context of the current project:

运行任意的shell脚本，在当前项目中的上下文中：

```shell
$ npm x -c 'eslint && say "hooray, lint passed"'
# ~ or ~
$ npx -c 'eslint && say "hooray, lint passed"'
```

## Workspaces support

这一部分涉及`workspaces`，当前没有用到，所以忽略了

## Compatibility with Older npx Versions

The `npx` binary was rewritten in npm v7.0.0, and the standalone `npx` package deprecated at that time. `npx` uses the `npm exec` command instead of a separate argument parser and install process, with some affordances to maintain backwards compatibility with the arguments it accepted in previous versions.

`npx`二进制文件在npm v7.0.0时重写了，之前独立的`npx`二进制文件被弃用。`npx`使用`npm exec`命令代替单独的参数解析器和安装进程，同时提供了一些 affordances 来保持与以前的版本中`npx`所接受的参数的向后兼容性。

This resulted in some shifts in its functionality:

这导致了一些功能的变化：

- Any `npm config` value may be provided.
- 任意 `npm config`的值都可用。
- To prevent security and user-experience problems from mistyping package names, npx prompts before installing anything. Suppress this prompt with the `-y` or `--yes` option.
- 为了防止安全性和用户体验问题，当用户输入错误时，npx在安装任意东西前会进行提示。使用`-y`或`--yes`选项可以抑制这个提示。
- The `--no-install` option is deprecated, and will be converted to `--no`.
- `--no-install`被废弃，使用 `--no`
- Shell fallback functionality is removed, as it is not advisable.
- The `-p` argument is a shorthand for --parseable in `npm`, but shorthand for `--package` in `npx`. This is maintained, but only for the `npx` executable.
- `-p`参数是`npm`的`--parseable`的简写，但是在`npx`中是`--package`的简写。这个功能被保留，但是只适用于`npx`二进制文件。
- The `--ignore-existing` option is removed. Locally installed bins are always present in the executed process `PATH`.
- `--ignore-existing`选项被移除。本地安装的 bins 总是在执行进程 `PATH` 中始终存在。
- The `--npm` option is removed. `npx` will always use the `npm` it ships with.
- `--npm`被移除。`npx` 总是使用它附带的 `npm`。
- The `--node-arg` and `-n` options have been removed.
- `--node-arg` 和 `-n`选项被移除。
- The `--always-spawn` option is redundant, and thus removed.
- `--always-spawn`选项是多余的，因此被移除。`
- The `--shell` option is replaced with `--script-shell`, but maintained in the `npx` executable for backwards compatibility.
- `--shell`选项被替换为`--script-shell`，但是保留在`npx`二进制文件中以保持向后兼容性。

## A note on caching

The npm cli utilizes its internal package cache when using the package name specified. You can use the following to change how and when the cli uses this cache. See npm cache for more on how the cache works.

npm cli 在使用指定的包名称时，会使用其内部的包缓存。你可以使用以下内容来改变cli如何使用这个缓存。有关缓存的工作方式，请参阅npm cache。

### prefer-online

Forces staleness checks for packages, making the cli look for updates immediately even if the package is already in the cache.

强制对包进行不新鲜检查，即使包已经在缓存中，也要立刻寻找更新。

### prefer-offline

Bypasses staleness checks for packages. Missing data will still be requested from the server. To force full offline mode, use `offline`.

忽略不新鲜检查。丢失数据荏苒从服务器请求。强制所有离线模式，使用`offline`。

### offline

Forces full offline mode. Any packages not locally cached will result in an error.

强制全离线模式。任何包没有在本地被缓存则报错。

### workspace

因为目前开发没有涉及workspace，所以该部分内容没有学习

### workspaces

因为目前开发没有涉及workspace，所以该部分内容没有学习

---

==以上是官方文档的所有内容==
