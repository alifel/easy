# `npm-debug.log`

When a package fails to install or publish, the npm CLI will generate an `npm-debug.log` file. This log file can help you figure out what went wrong.

当一个包安装或发布失败时，npm CLI 会生成一个 `npm-debug.log` 文件。这个日志文件可以帮助你找出问题所在。

If you need to generate a `npm-debug.log` file, you can run one of these commands.

如果你需要生成一个 `npm-debug.log` 文件，你可以运行以下命令之一。

For installing packages:

对于安装包：

`npm install --timing`

For publishing packages:

对于发布包：

`npm publish --timing`

You can find the `npm-debug.log` file in your `.npm` directory. To find your `.npm` directory, use npm config get `cache`.

你可以在你的 `.npm` 目录中找到 `npm-debug.log` 文件。要找到你的 `.npm` 目录，使用 `npm config get cache` 命令。

If you use a CI environment, your logs are likely located elsewhere. For example, in Travis CI, you can find them in the `/home/travis/build` directory.

如果你使用 CI 环境，你的日志可能位于其他地方。例如，在 Travis CI 中，你可以在 `/home/travis/build` 目录中找到它们。
