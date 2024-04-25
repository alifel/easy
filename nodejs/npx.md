# npx

Run a command from a local or remote npm package 
运行一个命令，从本地或者远端npm包

## Synopsis

```shell
    npx -- <pkg>[@<version>] [args...]
    npx --package=<pkg>[@<version>] -- <cmd> [args...]
    npx -c '<cmd> [args...]'
    npx --package=foo -c '<cmd> [args...]'
```

## Description

This command allows you to run an arbitrary command from an npm package (either one installed locally, or fetched remotely), in a similar context as running it via `npm run`.

这个命令允许你运行任意的命令，从一个npm包中（要么本地安装的，要么从远程拉取），在与`npm run`执行时相似的上下文中。

Whatever packages are specified by the `--package` option will be provided in the `PATH` of the executed command, along with any locally installed package executables. The `--package` option may be specified multiple times, to execute the supplied command in an environment where all specified packages are available.

无论什么包通过`--package`选项被提供，它都会被添加到被执行命令的`PATH`中，与任何本地安装的可执行包一起。`--package`可以指定多次，在命令执行的环境中所有被指定的包都用。

If any requested packages are not present in the local project dependencies, then they are installed to a folder in the npm cache, which is added to the `PATH` environment variable in the executed process. A prompt is printed (which can be suppressed by providing either `--yes` or `--no`).

如果任何要求的包在本地项目依赖中没有出现，则它们安装到npm cache的一个文件夹中，它们被加入到了执行进程的`PATH`环境变量中。一个提示被打印（可以通过`--yes`或者`--no`来抑制相关打印）

Package names provided without a specifier will be matched with whatever version exists in the local project. Package names with a specifier will only be considered a match if they have the exact same name and version as the local dependency.

包名不带版本号，将匹配本地项目中存在的任何版本。包名带版本号，只有在本地依赖中存在同样的名称和版本时才会匹配。

If no `-c` or `--call` option is provided, then the positional arguments are used to generate the command string. If no `--package` options are provided, then npm will attempt to determine the executable name from the package specifier provided as the first positional argument according to the following heuristic:

如果没有`-c`或者`--call`选项，那么位置参数会被用来生成命令字符串 ***(:pill::pill::pill:这里的命令字符串就相当于直接在shell中执行的命令串)***。如果没有`--package`选项，那么npm会尝试，用下面的启发式算法来从包（通过第一个位置参数提供的）中确定可以执行的name：

- If the package has a single entry in its `bin` field in `package.json`, or if all entries are aliases of the same command, then that command will be used.
- 如果包有一个`bin`字段中只有一个条目，或者如果所有条目都指向同一个命令，那么这个命令就会被使用。
- If the package has multiple `bin` entries, and one of them matches the unscoped portion of the `name` field, then that command will be used.
- 如果包有多个`bin`条目，并且其中一个条目与`name`字段的unscoped部分匹配，那么这个命令就会被使用。
- If this does not result in exactly one option (either because there are no bin entries, or none of them match the name of the package), then npm exec exits with an error.
- 如果结果没有精确一个选项（因为没有bin条目，或者没有一个条目与包名称匹配），那么npm exec会退出并显示错误。

To run a binary other than the named binary, specify one or more `--package` options, which will prevent npm from inferring the package from the first command argument.

要运行一个二进制文件，而不是命名的二进制文件，指定一个或多个`--package`选项，这会阻止npm从第一个命令参数中推断出包。

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

## Compatibility with Older npx Versions

The `npx` binary was rewritten in npm v7.0.0, and the standalone `npx` package deprecated at that time. `npx` uses the `npm exec` command instead of a separate argument parser and install process, with some affordances to maintain backwards compatibility with the arguments it accepted in previous versions.

`npx`二进制文件在npm v7.0.0时重写了，之前独立的`npx`二进制文件被弃用。`npx`使用`npm exec`命令代替单独的参数解析器和安装进程，同时提供了一些 affordances 来保持与以前的版本中`npx`所接受的参数的向后兼容性。

This resulted in some shifts in its functionality:

这导致了一些功能的变化：

- Any npm config value may be provided.
- To prevent security and user-experience problems from mistyping package names, npx prompts before installing anything. Suppress this prompt with the -y or --yes option.
- The --no-install option is deprecated, and will be converted to --no.
- Shell fallback functionality is removed, as it is not advisable.
- The -p argument is a shorthand for --parseable in npm, but shorthand for --package in npx. This is maintained, but only for the npx executable.
- The --ignore-existing option is removed. Locally installed bins are always present in the executed process PATH.
- The --npm option is removed. npx will always use the npm it ships with.
- The --node-arg and -n options have been removed. Use NODE_OPTIONS instead: e.g., NODE_OPTIONS="--trace-warnings --trace-exit" npx foo --random=true
- The --always-spawn option is redundant, and thus removed.
- The --shell option is replaced with --script-shell, but maintained in the npx executable for backwards compatibility.

***以上是官方文档的所有内容***
