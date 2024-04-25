# Config

## More than you probably want to know about npm configuration

## Description

This article details npm configuration in general. To learn about the `config` command, see [npm config](https://docs.npmjs.com/cli/v10/commands/npm-config).

这篇文章详细介绍了 npm 配置。要了解 npm config 命令，请参阅 [npm config](https://docs.npmjs.com/cli/v10/commands/npm-config)。

npm gets its configuration values from the following sources, sorted by priority:

npm 从以下 sources 获取配置值，按优先级排序：

### Command Line Flags

Putting `--foo bar` on the command line sets the `foo` configuration parameter to `"bar"`. A `--` argument tells the cli parser to stop reading flags. Using `--flag` without specifying any value will set the value to `true`.

在命令行中添加 `--foo bar` 设置 `foo` 配置参数为 `"bar"`。`--` 后面的参数告诉 cli parser 停止读取标志。使用 `--flag` 而不指定任何值将设置值为 `true`。

Example: `--flag1 --flag2` will set both configuration parameters to `true`, while `--flag1 --flag2 bar` will set `flag1` to `true`, and `flag2` to `bar`. Finally, `--flag1 --flag2 -- bar` will set both configuration parameters to `true`, and the `bar` is taken as a command argument.

示例：`--flag1 --flag2` 将设置两个配置参数为 `true`，而 `--flag1 --flag2 bar` 将设置 `flag1` 为 `true`，`flag2` 为 `bar`。最后，`--flag1 --flag2 -- bar` 将设置两个配置参数为 `true`，`bar` 被当作命令参数。

### Environment Variables

Any environment variables that start with `npm_config_` will be interpreted as a configuration parameter. For example, putting `npm_config_foo=bar` in your environment will set the `foo` configuration parameter to `bar`. Any environment configurations that are not given a value will be given the value of `true`. Config values are case-insensitive, so `NPM_CONFIG_FOO=bar` will work the same. However, please note that inside `scripts` npm will set its own environment variables and Node will prefer those lowercase versions over any uppercase ones that you might set. For details see this issue.

任何以 `npm_config_` 开头的环境变量将被解释为configuration参数。例如，将 `npm_config_foo=bar` 设为环境变量后将设置 `foo` 配置参数为 `bar`。没有给定值的环境变量将获得值 `true`。配置值不区分大小写，所以 `NPM_CONFIG_FOO=bar` 作用一样。但是，请注意，在 `scripts` 中，npm 将设置自己的环境变量，Node将优先使用小写的版本而不是任何大写的版本。

Notice that you need to use underscores instead of dashes, so `--allow-same-version` would become `npm_config_allow_same_version=true`.

注意，您需要使用下划线而不是破折号，所以 `--allow-same-version` 将变为 `npm_config_allow_same_version=true`。

### npmrc Files

The four relevant files are:

4个相关的文件：

- per-project configuration file (`/path/to/my/project/.npmrc`)
- per-user configuration file (defaults to `$HOME/.npmrc`; configurable via CLI option `--userconfig` or environment variable `$NPM_CONFIG_USERCONFIG`)
- global configuration file (defaults to `$PREFIX/etc/npmrc`; configurable via CLI option --globalconfig or environment variable `$NPM_CONFIG_GLOBALCONFIG`)
- npm's built-in configuration file (`/path/to/npm/npmrc`)


- 项目配置文件 (`/path/to/my/project/.npmrc`)
- 用户配置文件 (defaults to `$HOME/.npmrc`; configurable via CLI option `--userconfig` or environment variable `$NPM_CONFIG_USERCONFIG`)
- 全局配置文件 (defaults to `$PREFIX/etc/npmrc`; configurable via CLI option --globalconfig or environment variable `$NPM_CONFIG_GLOBALCONFIG`)
- npm的内置配置文件 (`/path/to/npm/npmrc`)

See [npmrc](https://docs.npmjs.com/cli/v10/configuring-npm/npmrc) for more details.

### Default Configs

Run `npm config ls -l` to see a set of configuration parameters that are internal to npm, and are defaults if nothing else is specified.

## Shorthands and Other CLI Niceties

The following shorthands are parsed on the command-line:

下面是命令行中简写的解析：

```shell
-a: --all
--enjoy-by: --before
-c: --call
--desc: --description
-f: --force
-g: --global
--iwr: --include-workspace-root
-L: --location
-d: --loglevel info
-s: --loglevel silent
--silent: --loglevel silent
--ddd: --loglevel silly
--dd: --loglevel verbose
--verbose: --loglevel verbose
-q: --loglevel warn
--quiet: --loglevel warn
-l: --long
-m: --message
--local: --no-global
-n: --no-yes
--no: --no-yes
-p: --parseable
--porcelain: --parseable
-C: --prefix
--readonly: --read-only
--reg: --registry
-S: --save
-B: --save-bundle
-D: --save-dev
-E: --save-exact
-O: --save-optional
-P: --save-prod
-?: --usage
-h: --usage
-H: --usage
--help: --usage
-v: --version
-w: --workspace
--ws: --workspaces
-y: --yes
```

If the specified configuration param resolves unambiguously to a known configuration parameter, then it is expanded to that configuration parameter. For example:

如果指定的配置param可以唯一地解析为已知的配置参数，则将其扩展为该配置参数。例如：

```shell
    npm ls --par
    # same as:
    npm ls --parseable
```

If multiple single-character shorthands are strung together, and the resulting combination is unambiguously not some other configuration param, then it is expanded to its various component pieces. For example:

如果多个单字符简写串在一起，并且结果组合可以唯一地解析为另一个配置参数，则将其扩展为其各个组成部分。例如：

```shell
    npm ls -gpld
    # same as:
    npm ls --global --parseable --long --loglevel info
```

## Config Settings

这部分忽略，这里面是所有的配置项，具体实时查阅文档

***不算Config Settings部分，其余是文档的全部（using npm -> config）***
