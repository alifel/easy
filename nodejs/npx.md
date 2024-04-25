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

If no -c or --call option is provided, then the positional arguments are used to generate the command string. If no --package options are provided, then npm will attempt to determine the executable name from the package specifier provided as the first positional argument according to the following heuristic:

- If the package has a single entry in its bin field in package.json, or if all entries are aliases of the same command, then that command will be used.
- If the package has multiple bin entries, and one of them matches the unscoped portion of the name field, then that command will be used.
- If this does not result in exactly one option (either because there are no bin entries, or none of them match the name of the package), then npm exec exits with an error.

To run a binary other than the named binary, specify one or more --package options, which will prevent npm from inferring the package from the first command argument.