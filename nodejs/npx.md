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

如果任何请求的包在本地项目的依赖中，则它们安装到npm cache的一个文件夹中，它们被加入到了执行进程的`PATH`环境变量中。一个提示被打印（通过）

Package names provided without a specifier will be matched with whatever version exists in the local project. Package names with a specifier will only be considered a match if they have the exact same name and version as the local dependency.