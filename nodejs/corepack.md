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

如果值正确，是被支持的包管理器，corepack将会

You can use `corepack use` to ask Corepack to update your local `package.json` to use the package manager of your choice:

你可以使用`corepack use`去要求corepack更新你的`package.json`，去使用你选择的包管理器

```shell
    corepack use pnpm@7.x # sets the latest 7.x version in the package.json
    corepack use yarn@* # sets the latest version in the package.json 
```
