# corepack

Corepack is an experimental tool to help with managing versions of your package managers. It exposes binary proxies for each supported package manager that, when called, will identify whatever package manager is configured for the current project, download it if needed, and finally run it.

corepack是一个实验阶段的工具，帮助管理包管理器的版本。它导出每一个被支持的包管理器的二进制代理（如果需要，则下载并最终运行它），当调用它的时候，会识别当前项目配置的每一个包管理器。

Despite Corepack being distributed with default installs of Node.js, the package managers managed by Corepack are not part of the Node.js distribution and:

尽管corepack已经已经默认在安装node.js时一起分发了，但是被corepack管理的包管理器并不是node.js分发的一部分。

- Upon first use, Corepack downloads the latest version from the network.
    在第一次使用时，corepack会从网络下载最新版本。
- Any required updates (related to security vulnerabilities or otherwise) are out of scope of the Node.js project. If necessary end users must figure out how to update on their own.
    任何必要的更新（与安全漏洞或其他相关的）不在node.js项目范围之内。如果需要，最终用户需要自己决定如何更新的问题。

This feature simplifies two core workflows:

功能被简易化为2个核心流程：

- It eases new contributor onboarding, since they won't have to follow system-specific installation processes anymore just to have the package manager you want them to.
    它简化了新贡献者的入职流程，因为他们不再遵循一个系统指定的安装流程安装你希望他们使用的包管理器。

- It allows you to ensure that everyone in your team will use exactly the package manager version you intend them to, without them having to manually synchronize it each time you need to make an update.
    它永续你确保你团队的每一个人使用你希望他们使用的精确的包管理器版本，每次你需要做一个包管理器的版本升级时，不用他们手动同步。

## workflows

### Enabling the feature

Due to its experimental status, Corepack currently needs to be explicitly enabled to have any effect. To do that, run `corepack enable`, which will set up the symlinks in your environment next to the node binary (and overwrite the existing symlinks if necessary).

由于它的实验性质的状态，corepack当前需要被显示的开启才能有作用。要实现这一点，运行`corepack enable`，它会建立一个symlink在你的环境中，放在node的二进制文件旁（如果需要，覆盖存在的symlink）

From this point forward, any call to the supported binaries will work without further setup. Should you experience a problem, run `corepack disable` to remove the proxies from your system (and consider opening an issue on the Corepack repository to let us know).

从这一点开始，任何对支持的二进制文件的调用都不需要做什么其他设置了。如果您遇到问题，运行`corepack disable`从你的系统移除先前的东东（并考虑在corepack的仓库打开一个问题让我们知道）

### configuring a package

The Corepack proxies will find the closest `package.json` file in your current directory hierarchy to extract its "packageManager" property.

corepack代理将找到最近的`package.json`文件在当前目录层级中去匹配它的"packageManager"属性。

If the value corresponds to a supported package manager, Corepack will make sure that all calls to the relevant binaries are run against the requested version, downloading it on demand if needed, and aborting if it cannot be successfully retrieved.

如果值正确，是被支持的包管理器，corepack会确保所有相关包管理器二进制的调用都是针对要求的版本的，如果有需要会下载它，如果没有找到会中断。

You can use `corepack use` to ask Corepack to update your local `package.json` to use the package manager of your choice:

你可以使用`corepack use`去要求corepack更新你的`package.json`，去使用你选择的包管理器

```shell
    corepack use pnpm@7.x # sets the latest 7.x version in the package.json
    corepack use yarn@* # sets the latest version in the package.json 
```

### uprading the global versions

When running outside of an existing project (for example when running `yarn init`), Corepack will by default use predefined versions roughly corresponding to the latest stable releases from each tool. Those versions can be overridden by running the `corepack install` command along with the package manager version you wish to set:

当在一个存在的项目外运行的时候（例如：运行`yarn init`的时候），corepack默认使用预设置的版本，大概是每一个工具最新的稳定版。这些版本可以通过执行 `corepack install` 来用你期望的版本覆盖：

```shell
    corepack install --global yarn@x.y.z 
```

Alternately, a tag or range may be used:

或者，tag和范围也可以使用

```shell
    corepack install --global pnpm@*
    corepack install --global yarn@stable 
```

### offline worklow

Many production environments don't have network access. Since Corepack usually downloads the package manager releases straight from their registries, it can conflict with such environments. To avoid that happening, call the `corepack pack` command while you still have network access (typically at the same time you're preparing your deploy image). This will ensure that the required package managers are available even without network access.

许多生产环境不能访问网络。由于corepack通常下载包管理器的发行版是直接通过它的仓库，这与这种环境冲突。为了避免这种情况发生，调用`corepack pack`命令，当你可以访问网络的时候（通常是在你准备部署镜像的时候）。这样就能确保需要的包管理器在没有网络的情况下可用。

The pack command has various flags. Consult the detailed Corepack documentation for more information.

包管理命令有很多flag。可以通过[corepack的文档](https://github.com/nodejs/corepack#readme)查询更多信息。

## supported package managers

The following binaries are provided through Corepack:

下面的二进制包通过corepack被提供：

| Package manager | Binary names |
| --- | --- |
| yarn | yarn, parnpkg |
| pnpm | pnpm, pnpx |

## Common requestions

### How does Corepack interact with npm?

While Corepack could support npm like any other package manager, its shims aren't enabled by default. This has a few consequences:

尽管corepack可以向其他任何软件包管理器一样支持npm，但是默认是没有shim的。有下面一些原因：

- It's always possible to run a npm command within a project configured to be used with another package manager, since Corepack cannot intercept it.
    就算在一个项目中配置了其他包管理器，npm命令也可以执行，因为corepack不与它交互。

- While `npm` is a valid option in the "`packageManager`" property, the lack of shim will cause the global npm to be used.
    当`npm`作为"`packageManage`"属性的一个可使用的选项时，由于缺少shim会导致使用全局npm。

### Running `npm install -g yarn` doesn't work

npm prevents accidentally overriding the Corepack binaries when doing a global install. To avoid this problem, consider one of the following options:

当做一个全局安装时，npm会阻止以外的覆盖corepack的包。为了避开这个问题，考虑下面的选项：

- Don't run this command; Corepack will provide the package manager binaries anyway and will ensure that the requested versions are always available, so installing the package managers explicitly isn't needed.
    不要执行这个命令；corepack提供了包管理器的二进制包，会确保被要求的版本总是可用，以至于具体的包管理器是不被需要的。

- Add the `--force` flag to `npm install`; this will tell npm that it's fine to override binaries, but you'll erase the Corepack ones in the process. (Run `corepack enable` to add them back.)
    增加`--force`标志，在使用`npm install`时，这会告诉npm可以覆盖二进制文件，但是这会擦掉corepack的二进制文件。（执行`corepack enable`重新添加回它们）

---

==以上是官方文档的所有内容==

<https://nodejs.org/docs/latest/api/corepack.html>

nodejs v22.0.0
