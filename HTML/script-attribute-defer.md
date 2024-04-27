# script attribute defer

This Boolean attribute is set to indicate to a browser that the script is meant to be executed after the document has been parsed, but before firing [DOMContentLoaded](https://developer.mozilla.org/en-US/docs/Web/API/Document/DOMContentLoaded_event).

这个布尔型的属性是用来指示浏览器，脚本应该在文档解析完成，但[DOMContentLoaded](https://developer.mozilla.org/en-US/docs/Web/API/Document/DOMContentLoaded_event)事件之前执行。

Scripts with the `defer` attribute will prevent the `DOMContentLoaded` event from firing until the script has loaded and finished evaluating.

有`defer`属性的scripts将会阻止`DOMContentLoaded`事件的触发，直到script完成加载和evaluating。

> Warning: This attribute must not be used if the `src` attribute is absent (i.e. for inline scripts), in this case it would have no effect.
>
> 警告：如果`<script>`没有`src`这个属性（即：内连`<scripts>`），则这个属性性一定不能被使用。在这样的例子中不会有任何作用
>
> The defer attribute has no effect on module scripts — they defer by default.
>
> defer属性对module scripts没有作用，它们默认是defer的。

Scripts with the `defer` attribute will execute in the order in which they appear in the document.

有`defer`属性的scripts将会按照在文档中出现的顺序执行。

This attribute allows the elimination of parser-blocking JavaScript where the browser would have to load and evaluate scripts before continuing to parse. async has a similar effect in this case.

这个属性允许移除parser-blocking JavaScript，浏览器必须等待脚本加载和evaluating之后才能继续解析。async也有类似的效果。

---

==以上是mdn中script元素文档的部分截取==

<https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script>
