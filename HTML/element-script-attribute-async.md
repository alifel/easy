# `<script>` attribute `async`

For classic scripts, if the `async` attribute is present, then the classic script will be fetched in parallel to parsing and evaluated as soon as it is available.

对于经典的额scripts，如果`async`属性存在，那么经典的script将在文档解析（生成DOM）的同时并行的下载，在他可用的时候尽快evaluate。`

For module scripts, if the `async` attribute is present then the scripts and all their dependencies will be fetched in parallel to parsing and evaluated as soon as they are available.

对于module scripts，如果 `async` 属性出现，则scripts和它的所有依赖将会在文档继续进行分析（生成DOM）的同时并行下载，并在他们可用的时候尽可能快的evalute。

This attribute allows the elimination of parser-blocking JavaScript where the browser would have to load and evaluate scripts before continuing to parse. `defer` has a similar effect in this case.

这个属性允许消除Javascript的解析阻塞（浏览器要在继续分析文档之前，加载和evaluate script）。`defer`有类似的作用

This is a boolean attribute: the presence of a boolean attribute on an element represents the `true` value, and the absence of the attribute represents the `false` value.

这是一个布尔型的属性：一个布尔型的属性在element中出现，代表`true`，不出现代表`false`。

See Browser compatibility for notes on browser support. See also [Async scripts for asm.js](https://developer.mozilla.org/en-US/docs/Games/Techniques/Async_scripts).

在浏览器支持中看浏览器的兼容性。 也看一下See also [Async scripts for asm.js](https://developer.mozilla.org/en-US/docs/Games/Techniques/Async_scripts)。

---

==以上是mdn中script得async相关部分的截取==

<https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script>
