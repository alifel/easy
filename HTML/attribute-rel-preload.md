# rel = preload

The `preload` value of the `<link>` element's `rel` attribute lets you declare fetch requests in the HTML's `<head>`, specifying resources that your page will need very soon, which you want to start loading early in the page lifecycle, before browsers' main rendering machinery kicks in. This ensures they are available earlier and are less likely to block the page's render, improving performance. Even though the name contains the term load, it doesn't load and execute the script but only schedules it to be downloaded and cached with a higher priority.

`<link>`元素的`rel` attribute设置为`preload`让你在HTML文档的`<head>`部分申明了一个fetch请求，该请求指定了一个你的页面将很快需要用到的资源，你想要在页面生命周期的早期就加载它，在浏览器主要的渲染机制启动前。这能确保它们尽早的可用，更少的可能阻塞页面渲染，提高性能。尽管名字中包含了load一词语，它不下载和执行script，仅仅是安排了一个较高优先级的下载和缓存。

## the basic

You most commonly use `<link>` to load a CSS file to style your page with:

你最常用的是用`<link>`加载css文件，去排版你的页面：

```HTML
<link rel="stylesheet" href="styles/main.css" />
```

Here however, we will use a `rel` value of `preload`, which turns `<link>` into a preloader for any resource we want. You will also need to specify:

不论怎么样，我们使用`rel`值为`preload`，这会使`<link>`成为我们所需的任意资源的预加载器。你还需要指定：

- The path to the resource in the `href` attribute.
  在`href`属性中指定资源的路径。
- The type of resource in the `as` attribute.
  在`as`属性中指定资源的类型。

A simple example might look like this (see our JS and CSS example source, and also live):

一个简单的例子可能看起来像这样：

```HTML
<head>
  <meta charset="utf-8" />
  <title>JS and CSS preload example</title>

  <link rel="preload" href="style.css" as="style" />
  <link rel="preload" href="main.js" as="script" />

  <link rel="stylesheet" href="style.css" />
</head>

<body>
  <h1>bouncing balls</h1>
  <canvas></canvas>

  <script src="main.js" defer></script>
</body>
```

Here we preload our CSS and JavaScript files so they will be available as soon as they are required for the rendering of the page later on. This example is trivial, as the browser probably discovers the `<link rel="stylesheet">` and `<script>` elements in the same chunk of HTML as the preloads, but the benefits can be seen much more clearly the later resources are discovered and the larger they are. For example:

这里我们预先加载了我们的CSS文件和javascript文件，因此对于稍后页面的渲染他们将会一旦需要就可用。这个例子很简单，因为浏览器很可能在同一个HTML块中发现了被预加载的`<link rel="stylesheet">`和`<script>`元素，但当资源被发现得越晚，或者资源体积越大时，预加载的好处就会体现得更加明显。例如：

- Resources that are pointed to from inside CSS, like fonts or images.
  CSS中的指向的资源，比如字体或图片。
- Resources that JavaScript can request, like imported scripts.
  JavaScript可以请求的资源，比如导入的script。

`preload` has other advantages too. Using `as` to specify the type of content to be preloaded allows the browser to:

`preload`也有其他优势。使用`as`来指定要预加载的内容类型，允许浏览器：

- Store in the cache for future requests, reusing the resource if appropriate.
  存储在缓存中，以便将来请求时重用资源，如果合适。
- Apply the correct content security policy to the resource.
  应用正确的内容安全策略到资源上。
- Set the correct `Accept` request headers for it.
  设置正确的`Accept`请求头。

## What types of content can be preloaded?

Many content types can be preloaded. The possible `as` attribute values are:

许多内容类型都可以被预加载。`as`属性可能的值有：

- `fetch`: Resource to be accessed by a fetch or XHR request, such as an ArrayBuffer, WebAssembly binary, or JSON file.
  资源会被fetch或者XHR request请求访问，例如一个ArrayBuffer，WebAssembly二进制文件，或者JSON文件。
- `font`: Font file.
- `image`: Image file.
- `script`: JavaScript file.
- `style`: CSS stylesheet.
- `track`: WebVTT file.

> :warning: Note: font and fetch preloading requires the crossorigin attribute to be set; see [CORS-enabled fetches](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/rel/preload#cors-enabled_fetches) below.
>
> 注意：字体和fetch预加载需要设置crossorigin属性；请参阅下面的[CORS-enabled fetches](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/rel/preload#cors-enabled_fetches)。


>:warning: Note: There's more detail about these values and the web features they expect to be consumed by in the HTML spec — see Link type "preload". Also note that the full list of values the as attribute can take is governed by the Fetch spec — see request destinations.
---

<https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/rel/preload>
