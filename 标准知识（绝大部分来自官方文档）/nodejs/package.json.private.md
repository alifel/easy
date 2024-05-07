# private

If you set `"private": true` in your `package.json`, then `npm` will refuse to publish it.

如果你设置了`"private": true`在你的`package.json`，那么npm将拒绝发布它。

This is a way to prevent accidental publication of private repositories. If you would like to ensure that a given package is only ever published to a specific registry (for example, an internal registry), then use the `publishConfig` dictionary described below to override the `registry` config param at publish-time.

这是一个阻止以外发布自己私有仓库的方法。如果你想保证包仅仅发不到特定的registry（例如内部registry）中，则使用下面描述的`publishConfig`去覆盖发布时的`registry`参数。

---

==以上是文档全文==

<https://docs.npmjs.com/cli/v10/configuring-npm/package-json#private>

npm version 10.5.2
