# 网络新功能—2018 年谷歌输入输出大会

> 原文：<https://medium.com/hackernoon/whats-new-for-the-web-google-i-o-2018-27670cbc5e1a>

谷歌结束了它的年度开发者大会，谷歌 I/O 2018。虽然你可能没有时间看完所有的公告，但我还是做了一个简短的列表，列出了网络上发生的事情。

如果我落下了什么东西，我提前道歉。有很多(小而重要的事情)正在发生。我非常欢迎从评论中得到反馈，看看这篇文章遗漏了什么！

# 网

*   【Webpack(不要和 Webpack 混淆)。允许您打包您的网站，并允许离线共享。
*   网络政策。林挺 HTML CSS a la [JavaScript](https://hackernoon.com/tagged/javascript) 的内置平台。我的意思是，你可以限制使用的 [CSS](https://hackernoon.com/tagged/css) 动画，图像大小是允许的。它将通过的最大 CSS，等等。
*   分层 API。

# 。应用顶级域名(TLD)

![](img/fdca3e70ba050d6f129b1396208a7ec1.png)

Introducing .app domain names and how to secure them (Google I/O ‘18)

看视频[这里](https://www.youtube.com/watch?v=kBkX30Cj7Bw)。

*   。io 顶级域名(TLD)是分配给英属印度洋领地的互联网国家代码顶级域名！(这很好，我只是认为它是为开发人员制作的…它不是)
*   全部。应用程序域必须在 HTTPS 提供服务。
*   实现 HSTS。
*   HSTS 阻止 HTTP 降级攻击。ISP 可能会将 HTTPS 降级为 HTTP。这是通过使用 HSTS 来防止的
*   您还可以防止广告注射。
*   您也可以预加载域。
*   的。应用程序顶级域已预加载。
*   你不需要在 Nginx 或者。应用
*   将您的 topdomain 提交到[预加载列表](https://hstspreload.org)。这是一个被硬编码到 Chrome 中的网站列表，仅适用于 HTTPS。

# PWA——渐进式网络应用——一些注意事项。

*   为了让应用程序可以安装，你必须执行 window . addevent listener(' before install prompt ')，因为 Chrome 不会自己显示安装提示。
*   将 **"scope"** 属性添加到 manifest.json 中！！如果不添加它，当你更改网址时，会将你从应用程序弹出到浏览器标签。
*   添加[受信任的 Web 活动。](https://developers.google.com/web/updates/2017/10/using-twa#set_up_digital_asset_links_in_an_android_app)

> **可信网络活动**是一种集成*您的*网络应用内容的新方式，例如*您的* PWA 与*您的* Android 应用，使用基于自定义标签的协议

*   设置数字资产链接
*   更新 AndroidManifest.xml
*   更新 res/values/strings.xml
*   创建和部署 assetlinks.json
*   创建活动
*   [https://g.co/TrustedWebActivities](https://g.co/TrustedWebActivities)

# Chrome DevTools 控制台

![](img/92fff32ef7ee3755c91b4f0b9e6af956.png)

What’s new in Chrome DevTools (Google I/O ‘18)

我强烈推荐查看[这个视频](https://www.youtube.com/watch?v=mfuE53x4b3k&t=16s)！超级有帮助！

*   搜索所有已加载的文件:
*   Ctrl+Shit+P 打开选择器。
*   转到 3 个点中的“更改”以查看您执行的所有更改。
*   在控制台的网络选项卡中新建搜索，按 ctrl + F。
*   Performance.getEntriesByType 是一个 Chrome DevTools speicifc API，它允许你从 [URL](https://youtu.be/mfuE53x4b3k?t=14m36s) 获取东西。看看这个。
*   $_ 表示控制台在 Chrome DevTools 中之前的结果！例如，它从控制台获取以前的输出(可以是变量、字符串、数组等)
*   devtools 上的 Debug()允许你在一个函数上停止。
*   监视器()
*   您也可以使用 queryObjects 从类对象中查询！
*   您可以在过滤器中使用布尔构造函数。
*   [**热切评价**](/better-things-digital/chrome-devtools-instant-evaluation-of-javascript-expressions-cf13af597a2e) 。(你必须检查这个——残忍)在铬黄色的旗帜后面。

# 更多 Chrome 开发工具

我决定制作这个新的部分，因为下面的不是实际视频的一部分。

*   Chrome DevTools 通过获取元素的背景颜色，让你看到元素的对比度。
*   如果你的图片上面有文字，Chrome DevTools 不会帮你捡起来。但是，你可以点击选择背景颜色，让它发挥它的魔力。

# 很高兴知道

*   要在 iFrame 上自动播放，父级需要启用自动播放，并且必须具有 allow = " autoplay 全屏”查看更多[此处](https://youtu.be/5azRhKsSU_M?t=8m45s)。
*   您可以通过请求 requestPictureInPicture 来使用画中画(这适用于 Android 8.0 以上版本)
*   演示 API 通过有线连接 HDMI 进行演示。这将使您的浏览器准备好在投影仪或外部屏幕上演示。

# 录像

*   AOM(开放媒体联盟)-谷歌的 AV1 视频编解码器。与 VP9 相比，节省了将近 30%!
*   沙加玩家。采用最佳实践。
*   可以看到应用的覆盖面。
*   导航器.连接.有效类型
*   通过在 link 标签中添加 preconnect 来避免代价高昂的往返。
*   实验性:优先级提示
*   Guess.js

# wordpress 软件

*   让 wordpress 站点具有响应性:
*   马修积累了优势
*   50%的网络是由某种 CMS 驱动的。
*   Wordpress 占据了网络的 30%。
*   [放大器主题支持](https://youtu.be/a8NScvBhVnc?t=21m16s):
*   AMP 插件 0.7 发布
*   AMP 插件 1.0(正在开发中)

# 浏览器中的辅助功能

*   [浏览器中的可访问性](https://www.youtube.com/watch?v=wkvslBGkhZY):
*   使用 lighthouse 测试可访问性。有一个辅助功能面板，可以让你检查你的应用程序对屏幕阅读器的友好程度。
*   新的 CSS 伪类:focus-visible。
*   可访问性对象模型

# 现代 JavaScript

*   用现代 JavaScript 构建网络的未来
*   [这不是新的](https://jakearchibald.com/2017/es-modules-in-browsers/)(我个人对此并不知情)。<script type = " module " src = "/my path _ to _ js _ module . mjs "></script>

*   通过以下操作预加载模块:
*   <link rel="”modulepreload”" href="”lib.mjs”"> (modulepreload 不是错别字)检查这里的[链接](https://developers.google.com/web/updates/2017/12/modulepreload)。
*   的。浏览器中不需要 mjs 扩展名。人们只是使用它，因为 NodeJS 确实执行了扩展。由于您将它的类型指定为模块，所以浏览器会将其作为 JavaScript 文件读取。
*   的。meta 不是标准的一部分，无论是在浏览器上还是在节点上，它都会给你不同的结果。
*   现在，您可以使用下划线将数千个 JavaScript 数字分组(从 2.7 开始，TypeScript 支持这一功能):
*   您可以键入 1_000_000_000，而不是 10000000。
*   它也适用于 hex。
*   还在标准化中。
*   有一种新的数据类型，叫做 [BigInt](https://github.com/tc39/proposal-bigint) 。
*   [用于循环](http://2ality.com/2017/12/for-await-of-sync-iterables.html)的等待。
*   regex 的新 **S** 和 **U** 标志。
*   新的[名称为您的 regex: /(？<年> \d{4})。这将非常方便，因为您可以通过以下方式访问正则表达式(来源于](https://developers.google.com/web/updates/2017/07/upcoming-regexp-features)[此链接](http://const pattern = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/u; const result = pattern.exec('2017-07-10'); // result.groups.year === '2017' // result.groups.month === '07' // result.groups.day === '10')):

```
const pattern = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/u;
const result = pattern.exec('2017-07-10');
// result.groups.year === '2017'
// result.groups.month === '07'
// result.groups.day === '10'
```

*   这个正则表达式适用于巴别塔和聚合填充
*   现在可以在 Unicode 中使用 RegEx 了。这也适用于 HTML 标签！。
*   [消极落后](http://2ality.com/2017/05/regexp-lookbehind-assertions.html)
*   string . match all(RegEx)-For Of 循环
*   [string . trim start()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/trimStart)[string . trimend()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/trimEnd)。这类似于填充机制。
*   最终添加到承诺中(Vs then，catch)。它也适用于 try catch 块。
*   (ECMA 2018)——[对象文字中的对象传播](http://2ality.com/2016/10/rest-spread-properties.html)。这相当于进行浅层复制。
*   您可以通过向对象添加扩展操作符来防止对象意外变异！Vs 对象.赋值

# WebXR

*   沉浸式 Web VR + AR。它现在被称为 WebXR
*   Chrome 能够在相同的帧率下实现 2 倍的像素。
*   [Chrome 67 中的起源试验](https://blog.chromium.org/2018/04/chrome-67-beta-webxr-origin-trial.html)。WebXR 设备 API。(更多信息见链接)
*   Chrome://flags/#webxr
*   魔法之窗。当您拥有移动设备(智能手机)并加载包含 WebXR 内容的 web 应用程序时所产生的效果。你可以通过在 XR (AR 和/或 VR)应用周围平移手机来了解它是如何移动的。
*   [navigator . xr . request device()](https://immersive-web.github.io/webxr/#xr-interface)
*   XRView s

> 一个`[XRView](https://immersive-web.github.io/webxr/#xrview)`描述了一个 XR 场景的单个视图。每个[视图](https://immersive-web.github.io/webxr/#view)对应于 XR 设备用来向用户呈现图像的显示器或显示器的一部分

# WASM

*   使用 [Emscriptem](http://kripken.github.io/emscripten-site/docs/compiling/WebAssembly.html) 作为编译器将 C 和 C++编译成 WebAssembly。
*   Emsc —南 WASM=1 -o
*   Emcc 给了我们两个文件。
*   模块对象允许我们调用编译函数
*   EM_JS
*   [Wasm 螺纹](https://github.com/WebAssembly/threads)
*   [SharedArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) 。这适用于网络工作者。这一点很重要，因为 WebWorkers 过去常常将信息从主线程复制到 worker 中，现在使用 SharedArrayBuffer 就不必再这样做了！它现在与主线程共享它！这是一个正在进行中的。
*   Webassembly 可以通过引用 DOM 来运行。
*   导入 foo.wasm
*   管理对象建议

# 更多灯塔和铬 UX 报告

*   使用 Lighthouse 和 Chrome UX 报告优化 web 应用程序性能
*   使用实验室(指标)+野外(现场-来自用户)数据。
*   设备，它们所在的网络。
*   互动指标:TTI
*   视觉指标:FCP
*   第一次输入延迟:是用户第一次与页面交互的延迟。测量从用户第一次与您的站点交互(即，当他们点击链接、点击按钮或使用自定义的 JavaScript 支持的控件)到浏览器实际能够响应该交互的时间。
*   【g.co/dev/SpeedToolsOverview 
*   [Chrome 用户体验报告](https://developers.google.com/web/tools/chrome-user-experience-report/)优化 web 应用性能。它允许你从谷歌 Chrome 用户的数据中比较你的网站与其他网站相比有多好。它每月更新。
*   谷歌大查询
*   速度记分卡
*   使用具有适当安全性的 CSP。(查看谷歌网络标准帖子)

# 无头铬合金

*   无头铬与木偶
*   您可以使用它来自动化测试。
*   前端的前端。
*   将其用于任何平台和非平台特性。
*   你可以在 Headless(没有 UI)和 non headless 中启动 Chrome。
*   你可以得到一页的图表。喜欢所有的链接和一切。
*   谷歌爬虫使用 Chrome 41，你需要填充这个！

# Recaptcha V3

*   它有一个基于分数的系统。
*   它不向用户显示任何内容。
*   通过中间人攻击，一次性密码可以像密码一样容易地被网络钓鱼。
*   请改用安全密钥。它们直接与浏览器对话。
*   [注册并登录网络(Google I/O’18)的新功能](https://www.youtube.com/watch?v=kGGMgEfSzMw)
*   makeCredential.js

就是这样！我漏掉了一些信息！我希望得到社区的一些反馈！