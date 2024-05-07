# import

The static `import` declaration is used to import read-only live [bindings](./binding.md) which are [exported](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) by another module. The imported bindings are called live bindings because they are updated by the module that exported the binding, but cannot be re-assigned by the importing module.

静态`import`申明被用来导入一个只读的[bindings](./binding.md) (它被另外一个模块[exported](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export))。被导入的binding被叫作live binding，因为他们被模块（导出他们的）更新，但是不能被导入它的模块再次赋值。
