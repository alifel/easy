# Binding

In programming, a binding is an association of an [identifier](https://developer.mozilla.org/en-US/docs/Glossary/Identifier) with a value. Not all bindings are [variables](https://developer.mozilla.org/en-US/docs/Glossary/Variable) — for example, function [parameters](https://developer.mozilla.org/en-US/docs/Glossary/Parameter) and the binding created by the [`catch (e)`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch) block are not "variables" in the strict sense. In addition, some bindings are implicitly created by the language — for example, [`this`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this) and [`new.target`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new.target) in JavaScript.

在程序中，binding是一个[identifier](https://developer.mozilla.org/en-US/docs/Glossary/Identifier)和值的关系。不是所有的binding都是[variables](https://developer.mozilla.org/en-US/docs/Glossary/Variable)。例如，function [parameters](https://developer.mozilla.org/en-US/docs/Glossary/Parameter) and the binding created by the [`catch (e)`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch) block are not "variables" in the strict sense。此外，一些binding是被语言暗指的，例如，JavaScript中的[`this`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this) 和[`new.target`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/new.target)。

A binding is [mutable](https://developer.mozilla.org/en-US/docs/Glossary/Mutable) if it can be re-assigned, and [immutable](https://developer.mozilla.org/en-US/docs/Glossary/Immutable) otherwise; this does not mean that the value it holds is immutable.

如果binding可以被再次赋值，那么它是可变的，否则它不可变。这不意味着值是不可变的。

A binding is often associated with a [scope](https://developer.mozilla.org/en-US/docs/Glossary/Scope). Some languages allow re-creating bindings (also called redeclaring) within the same scope, while others don't; in JavaScript, whether bindings can be redeclared depends on the construct used to create the binding.

一个binding通常是与一个[scope](https://developer.mozilla.org/en-US/docs/Glossary/Scope)关联的。一些语言允许在同一个scope中重新创建bindings（也称为重新声明），而其他语言则不允许; 在JavaScript中，是否可以重新声明绑定取决于创建binding的结构。

---

==官方文档==

<https://developer.mozilla.org/en-US/docs/Glossary/Binding>
