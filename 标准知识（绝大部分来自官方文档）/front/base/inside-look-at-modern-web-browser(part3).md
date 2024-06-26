# Inside look at modern web browser (part 3)

## Inner workings of a Renderer Process 渲染进程内部的工作

This is part 3 of 4 part blog series looking at how browsers work. Previously, we covered [multi-process architecture](./inside-look-at-modern-web-browser(part1).md) and [navigation flow](./inside-look-at-modern-web-browser(part2).md). In this post, we are going to look at what happens inside of the renderer process.

这是讲述浏览器如何工作的这一系列blog中的第3部分，一共4部分。之前我们已经讲了[multi-process architecture](./inside-look-at-modern-web-browser(part1).md) 和[navigation flow](./inside-look-at-modern-web-browser(part2).md)。在这篇文章中，我们讲继续看渲染进程中发生了什么。

Renderer process touches many aspects of web performance. Since there is a lot happening inside of the renderer process, this post is only a general overview. If you'd like to dig deeper, [the Performance section of Web Fundamentals](https://developers.google.com/web/fundamentals/performance/why-performance-matters/) has many more resources.

渲染进程接触了许多与web性能有关的方面。因为渲染进程里面有很多事情，本篇只做概览。如果你想深入了解，[Web Fundamentals Performance section](https://developers.google.com/web/fundamentals/performance/why-performance-matters/) 有很多资源。

## Renderer processes handle web contents 渲染进程处理web内容

The renderer process is responsible for everything that happens inside of a tab. In a renderer process, the main thread handles most of the code you send to the user. Sometimes parts of your JavaScript is handled by worker threads if you use a web worker or a service worker. Compositor and raster threads are also run inside of a renderer processes to render a page efficiently and smoothly.

渲染进程响应tab中发生的每一件事情。在一个渲染进程中，主线程处理你发送给用户的大多数代码。有时候你javascript的一部分是被worker线程处理的（如果你用了web worker或者service worker）。compositor和raster线程也运行的渲染进程，让渲染一个页面更搞笑和流畅。

The renderer process's core job is to turn HTML, CSS, and JavaScript into a web page that the user can interact with.

渲染进程的核心工作是将 HTML、CSS 和 JavaScript 转换为用户可以与之交互的网页。

![p1](./inside-look-at-modern-web-browser(part3)-images/renderer-process-df424472d0633_1920.png)

Figure 1: Renderer process with a main thread, worker threads, a compositor thread, and a raster thread inside

图1: 渲染进程，包含主线程，worker线程，compositor线程和raster线程。

## Parsing 分析

### Construction of a DOM 构建DOM

When the renderer process receives a commit message for a navigation and starts to receive HTML data, the main thread begins to parse the text string (HTML) and turn it into a Document Object Model (DOM).

当主线程接收到1个提交的消息从导航中，它开始接收HTML数据，主线程开始解析文本字符串（HTML）并将其转换为文档对象模型（DOM）。

The DOM is a browser's internal representation of the page as well as the data structure and API that web developer can interact with via JavaScript.

DOM是浏览器内部页面、数据结构和API的代表，web开发者可以通过JavaScript与之交互。

Parsing an HTML document into a DOM is defined by the [HTML Standard](https://html.spec.whatwg.org/). You may have noticed that feeding HTML to a browser never throws an error. For example, missing closing `</p>` tag is a valid HTML. Erroneous markup like `Hi! <b>I'm <i>Chrome</b>!</i>` (b tag is closed before i tag) is treated as if you wrote `Hi! <b>I'm <i>Chrome</i></b><i>!</i>`. This is because the HTML specification is designed to handle those errors gracefully. If you are curious how these things are done, you can read on "[An introduction to error handling and strange cases in the parser](https://html.spec.whatwg.org/multipage/parsing.html#an-introduction-to-error-handling-and-strange-cases-in-the-parser)" section of the HTML spec.

分析HTML文档到DOM被[HTML标准](https://html.spec.whatwg.org/)定义。你可能注意到，向浏览器提供HTML不会抛出错误。例如，缺少闭合`</p>`标签是有效的HTML。像`Hi! <b>I'm <i>Chrome</b>!</i>`（b标签在i标签之前关闭）这样的错误标记被处理为好像你写了`Hi! <b>I'm <i>Chrome</i></b><i>!</i>`。这是因为HTML规范被设计的可以非常优美的处理这些错误。如果你好奇这些事情是如何完成的，你可以读规范中的"[An introduction to error handling and strange cases in the parser](https://html.spec.whatwg.org/multipage/parsing.html#an-introduction-to-error-handling-and-strange-cases-in-the-parser)"这部分。

### Subresource loading 子资源加载

A website usually uses external resources like images, CSS, and JavaScript. Those files need to be loaded from network or cache. The main thread could request them one by one as they find them while parsing to build a DOM, but in order to speed up, "preload scanner" is run concurrently. If there are things like `<img>` or `<link>` in the HTML document, preload scanner peeks at tokens generated by HTML parser and sends requests to the network thread in the browser process.

一个站点通常使用外部资源，像图片，CSS和JavaScript。这些文件需要从网络或缓存中载入。主线程会一个一个请求在构建DOM时找到的这些外部资源，但为了加快速度，"预加载扫描器"同时运行。如果HTML文档中有像`<img>`或`<link>`这样的东西，预加载扫描器会窥探HTML解析器生成的标记，并在浏览器进程中发送网络请求。

![p2](./inside-look-at-modern-web-browser(part3)-images/dom-ffeed8e96a6e8_1920.png)

Figure 2: The main thread parsing HTML and building a DOM tree

图2：主线程分析HTML并构建DOM树

### JavaScript can block the parsing Javascript会堵塞分析进程

When the HTML parser finds a `<script>` tag, it pauses the parsing of the HTML document and has to load, parse, and execute the JavaScript code. Why? because JavaScript can change the shape of the document using things like `document.write()` which changes the entire DOM structure ([overview of the parsing model](https://html.spec.whatwg.org/multipage/parsing.html#overview-of-the-parsing-model) in the HTML spec has a nice diagram). This is why the HTML parser has to wait for JavaScript to run before it can resume parsing of the HTML document. If you are curious about what happens in JavaScript execution, [the V8 team has talks and blog posts on this](https://mathiasbynens.be/notes/shapes-ics).

当HTML解析器发现`<script>`标签时，它暂停解析HTML文档，并必须加载，解析和执行JavaScript代码。为什么？因为JavaScript可以使用像`document.write()`这样的东西改变文档的结构([HTML spec中的解析模型概述](https://html.spec.whatwg.org/multipage/parsing.html#overview-of-the-parsing-model)中有一个很好的图表)。这就是为什么HTML解析器必须等待JavaScript运行才能继续解析HTML文档。如果你好奇JavaScript执行中发生了什么，看[the V8 team has talks and blog posts on this](https://mathiasbynens.be/notes/shapes-ics)。

## Hint to browser how you want to load resources 暗示浏览器按你的想法加载资源

There are many ways web developers can send hints to the browser in order to load resources nicely. If your JavaScript does not use `document.write()`, you can add `async` or `defer` attribute to the `<script>` tag. The browser then loads and runs the JavaScript code asynchronously and does not block the parsing. You may also use [JavaScript module](https://developers.google.com/web/fundamentals/primers/modules) if that's suitable. `<link rel="preload">` is a way to inform browser that the resource is definitely needed for current navigation and you would like to download as soon as possible. You can read more on this at [Resource Prioritization – Getting the Browser to Help You](https://developers.google.com/web/fundamentals/performance/resource-prioritization).

有许多方法web开发者可以暗示浏览器恰当的加载资源。如果你的JavaScript不使用`document.write()`，你可以将`async`或`defer`属性添加到`<script>`标签。浏览器然后异步加载并运行JavaScript代码，而不会阻塞解析。你也可以使用[JavaScript模块](https://developers.google.com/web/fundamentals/primers/modules)(:pill: ==自行额外说明：javascript module默认有`defer` attribute，来源：<https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#defer>==)，如果合适。`<link rel="preload">`是一种告诉浏览器当前导航需要加载的资源的方式，你希望尽快下载。你可以在[Resource Prioritization – Getting the Browser to Help You](https://developers.google.com/web/fundamentals/performance/resource-prioritization).

## Style calculation 样式计算

Having a DOM is not enough to know what the page would look like because we can style page elements in CSS. The main thread parses CSS and determines the computed style for each DOM node. This is information about what kind of style is applied to each element based on CSS selectors. You can see this information in the computed section of DevTools.

光有DOM还不能完全确认页面会像什么样子，因为我们可以给页面元素用css加上样式。主线程分析CSS，计算出每个DOM节点的样式。每个节点上的样式信息是通过CSS选择器来对应的。你可以在DevTools的计算样式部分中看到这些信息。

![p3](./inside-look-at-modern-web-browser(part3)-images/computed-style-5e3c65a01f3f1_1920.png)

Figure 3: The main thread parsing CSS to add computed style

Even if you do not provide any CSS, each DOM node has a computed style. `<h1>` tag is displayed bigger than `<h2>` tag and margins are defined for each element. This is because the browser has a default style sheet. If you want to know what Chrome's default CSS is like, [you can see the source code here](https://cs.chromium.org/chromium/src/third_party/blink/renderer/core/html/resources/html.css).

即使你不提供任何CSS，每一个DOM节点都有1个computed style。`<h1>`比`<h2>`显示的更大，同时每一个元素都定义了margin。这是因为浏览器有一个默认样式表(:pill: ==自行额外说明：当你没有指定（通过style或者class）任何样式时，对于任意HTML标签，都会使用浏览器的默认样式。当你发现有些HTML标签，除了浏览器默认样式表外，有额外的样式时，要考虑到是继承造成的，请重新复查浏览器默认样式表。这些已经在chrome中，针对特定的html标签，一一对比了chrome源代码中的默认样式，确实如此==)。如果你想了解Chrome的默认样式，[你可以在这看源代码](https://cs.chromium.org/chromium/src/third_party/blink/renderer/core/html/resources/html.css)

## Layout 布局

Now the renderer process knows the structure of a document and styles for each nodes, but that is not enough to render a page. Imagine you are trying to describe a painting to your friend over a phone. "There is a big red circle and a small blue square" is not enough information for your friend to know what exactly the painting would look like.

现在，渲染进程知道了文档的结构和每一个节点的样式，但是还不足以可以渲染一个页面。想象你试图向你的朋友通过电话描述一幅图片。“有一个大的红色圆和一个小的蓝色方块”，对于让给你朋友精确的知道图片看起来是什么样子，信息是不够的。

![p4](./inside-look-at-modern-web-browser(part3)-images/game-human-fax-machine-17f10d8b400ac_1920.png)

Figure 4: A person standing in front of a painting, phone line connected to the other person

The layout is a process to find the geometry of elements. The main thread walks through the DOM and computed styles and creates the layout tree which has information like x y coordinates and bounding box sizes. Layout tree may be similar structure to the DOM tree, but it only contains information related to what's visible on the page. If `display: none` is applied, that element is not part of the layout tree (however, an element with `visibility: hidden` is in the layout tree). Similarly, if a pseudo class with content like `p::before{content:"Hi!"}` is applied, it is included in the layout tree even though that is not in the DOM.

![p5](./inside-look-at-modern-web-browser(part3)-images/layout-9d8ed8c743f45_1920.png)

Figure 5: The main thread going over DOM tree with computed styles and producing layout tree

<video width="320" height="240" controls>
  <source src="./inside-look-at-modern-web-browser(part3)-images/rXSCtc21M00XrRqcw56C.mp4" type="video/mp4">
</video>

Figure 6: Box layout for a paragraph moving due to line break change

Determining the Layout of a page is a challenging task. Even the simplest page layout like a block flow from top to bottom has to consider how big the font is and where to line break them because those affect the size and shape of a paragraph; which then affects where the following paragraph needs to be.

CSS can make element float to one side, mask overflow item, and change writing directions. You can imagine, this layout stage has a mighty task. In Chrome, a whole team of engineers works on the layout. If you want to see details of their work, [few talks from BlinkOn Conference](https://www.youtube.com/watch?v=Y5Xa4H2wtVA) are recorded and quite interesting to watch.

## Paint

![p7](./inside-look-at-modern-web-browser(part3)-images/drawing-game-7902ad8a91d2f_1920.png)

Figure 7: A person in front of a canvas holding paintbrush, wondering if they should draw a circle first or square first

Having a DOM, style, and layout is still not enough to render a page. Let's say you are trying to reproduce a painting. You know the size, shape, and location of elements, but you still have to judge in what order you paint them.

For example, `z-index` might be set for certain elements, in that case painting in order of elements written in the HTML will result in incorrect rendering.

![p8](./inside-look-at-modern-web-browser(part3)-images/z-index-fail-2529cf989dc65_1920.png)

Figure 8: Page elements appearing in order of an HTML markup, resulting in wrong rendered image because z-index was not taken into account

At this paint step, the main thread walks the layout tree to create paint records. Paint record is a note of painting process like "background first, then text, then rectangle". If you have drawn on <canvas> element using JavaScript, this process might be familiar to you.

![p9](./inside-look-at-modern-web-browser(part3)-images/paint-records-151165f18a91e_1920.png)

Figure 9: The main thread walking through layout tree and producing paint records

### Updating rendering pipeline is costly

<video width="320" height="240" controls>
  <source src="./inside-look-at-modern-web-browser(part3)-images/d7zOpwpNIXIoVnoZCtI9.mp4" type="video/mp4">
</video>

Figure 10: DOM+Style, Layout, and Paint trees in order it is generated

The most important thing to grasp in rendering pipeline is that at each step the result of the previous operation is used to create new data. For example, if something changes in the layout tree, then the Paint order needs to be regenerated for affected parts of the document.

If you are animating elements, the browser has to run these operations in between every frame. Most of our displays refresh the screen 60 times a second (60 fps); animation will appear smooth to human eyes when you are moving things across the screen at every frame. However, if the animation misses the frames in between, then the page will appear "janky".

![p11](./inside-look-at-modern-web-browser(part3)-images/jage-jank-missing-frames-187496b8fbc8e_1920.png)

Figure 11: Animation frames on a timeline

Even if your rendering operations are keeping up with screen refresh, these calculations are running on the main thread, which means it could be blocked when your application is running JavaScript.

![p12](./inside-look-at-modern-web-browser(part3)-images/jage-jank-javascript-b19c0193934a3_1920.png)

Figure 12: Animation frames on a timeline, but one frame is blocked by JavaScript

You can divide JavaScript operation into small chunks and schedule to run at every frame using `requestAnimationFrame()`. For more on this topic, please see [Optimize JavaScript Execution](https://developers.google.com/web/fundamentals/performance/rendering/optimize-javascript-execution) . You might also run your [JavaScript in Web Workers](https://www.youtube.com/watch?v=X57mh8tKkgE) to avoid blocking the main thread.

![p13](./inside-look-at-modern-web-browser(part3)-images/request-animation-frame-3b04e26660019_1920.png)

Figure 13: Smaller chunks of JavaScript running on a timeline with animation frame

## Compositing

### How would you draw a page?

<video width="320" height="240" controls>
  <source src="./inside-look-at-modern-web-browser(part3)-images/AiIny83Lk4rTzsM8bxSn.mp4" type="video/mp4">
</video>

Figure 14: Animation of naive rastering process

Now that the browser knows the structure of the document, the style of each element, the geometry of the page, and the paint order, how does it draw a page? Turning this information into pixels on the screen is called rasterizing.

Perhaps a naive way to handle this would be to raster parts inside of the viewport. If a user scrolls the page, then move the rastered frame, and fill in the missing parts by rastering more. This is how Chrome handled rasterizing when it was first released. However, the modern browser runs a more sophisticated process called compositing.

### What is compositing

<video width="320" height="240" controls>
  <source src="./inside-look-at-modern-web-browser(part3)-images/Aggd8YLFPckZrBjEj74H.mp4" type="video/mp4">
</video>

Figure 15: Animation of compositing process

Compositing is a technique to separate parts of a page into layers, rasterize them separately, and composite as a page in a separate thread called compositor thread. If scroll happens, since layers are already rasterized, all it has to do is to composite a new frame. Animation can be achieved in the same way by moving layers and composite a new frame.

You can see how your website is divided into layers in DevTools using [Layers panel](https://blog.logrocket.com/eliminate-content-repaints-with-the-new-layers-panel-in-chrome-e2c306d4d752?gi=cd6271834cea).

### Dividing into layers

In order to find out which elements need to be in which layers, the main thread walks through the layout tree to create the layer tree (this part is called "Update Layer Tree" in the DevTools performance panel). If certain parts of a page that should be separate layer (like slide-in side menu) is not getting one, then you can hint to the browser by using `will-change` attribute in CSS.

![p16](./inside-look-at-modern-web-browser(part3)-images/layer-tree-cc92336966c73_1920.png)

Figure 16: The main thread walking through layout tree producing layer tree

You might be tempted to give layers to every element, but compositing across an excess number of layers could result in slower operation than rasterizing small parts of a page every frame, so it is crucial that you measure rendering performance of your application. For more about on topic, see [Stick to Compositor-Only Properties and Manage Layer Count](https://developers.google.com/web/fundamentals/performance/rendering/stick-to-compositor-only-properties-and-manage-layer-count).

### Raster and composite off of the main thread

Once the layer tree is created and paint orders are determined, the main thread commits that information to the compositor thread. The compositor thread then rasterizes each layer. A layer could be large like the entire length of a page, so the compositor thread divides them into tiles and sends each tile off to raster threads. Raster threads rasterize each tile and store them in GPU memory.

![p17](./inside-look-at-modern-web-browser(part3)-images/raster-9dfd7af5a9554_1920.png)

Figure 17: Raster threads creating the bitmap of tiles and sending to GPU

The compositor thread can prioritize different raster threads so that things within the viewport (or nearby) can be rastered first. A layer also has multiple tilings for different resolutions to handle things like zoom-in action.

Once tiles are rastered, compositor thread gathers tile information called draw quads to create a compositor frame.

|||
|---|---|
|Draw quads|Contains information such as the tile's location in memory and where in the page to draw the tile taking in consideration of the page compositing.|
|Compositor frame|A collection of draw quads that represents a frame of a page.|

A compositor frame is then submitted to the browser process via IPC. At this point, another compositor frame could be added from UI thread for the browser UI change or from other renderer processes for extensions. These compositor frames are sent to the GPU to display it on a screen. If a scroll event comes in, compositor thread creates another compositor frame to be sent to the GPU.

![p18](./inside-look-at-modern-web-browser(part3)-images/composit-266744978ac93_1920.png)

Figure 18: Compositor thread creating compositing frame. Frame is sent to the browser process then to GPU

The benefit of compositing is that it is done without involving the main thread. Compositor thread does not need to wait on style calculation or JavaScript execution. This is why [compositing only animations](https://www.html5rocks.com/en/tutorials/speed/high-performance-animations/) are considered the best for smooth performance. If layout or paint needs to be calculated again then the main thread has to be involved.

## Wrap Up

In this post, we looked at rendering pipeline from parsing to compositing. Hopefully, you are now empowered to read more about performance optimization of a website.

In the next and last post of this series, we'll look at the compositor thread in more details and see what happens when user input like mouse move and click comes in.

Did you enjoy the post? If you have any questions or suggestions for the future post, I'd love to hear from you in the comment section below or [@kosamari](https://twitter.com/kosamari) on Twitter.

---

==以上是blog全文的翻译==

<https://developer.chrome.com/blog/inside-browser-part3>
