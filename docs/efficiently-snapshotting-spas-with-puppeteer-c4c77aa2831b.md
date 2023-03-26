# 使用木偶师高效拍摄单页应用程序

> 原文：<https://medium.com/hackernoon/efficiently-snapshotting-spas-with-puppeteer-c4c77aa2831b>

![](img/8e71f5e5a07ee99cf2eaaaaac8db5811.png)

## 在每月 5 美元的 VPS 上运行截图服务

## **动机**

我的爱好项目——npm charts，是一个单页的 app，展示了各种 NPM 包的下载趋势。如果你想知道使用哪个无头 chrome 库，你会看到这个图表

然而，当该页面被分享到脸书、Twitter 或 Slack 时，显示的预览图像都是一样的——我两年前拍摄的比较前端框架的截图，并作为该网站唯一的开放图形图像上传。啧啧。

## 问题是

一切都托管在我的 5 美元的数字海洋小水滴上。我把这个功能推迟这么久的原因之一是因为我担心 droplet 无法处理运行快照服务的负载。我必须努力提高效率。

## 从简单开始

让我们从简单开始，首先得到一些有用的东西:

This function is called by an express route handler for /chart-image

相当简单，但是为每个请求启动一个浏览器实例，然后关闭它似乎很浪费。

目前，在我的 MacBook 上，返回一张截图的平均时间约为 3.5 秒，在 D.O. droplet 上只需更长时间。让我们减少再利用。

> 注:给出的所有计时都是在我的 MacBook 上运行的本地服务器上测量的。它只考虑了在 2015 MBP 上返回图像的函数的执行速度。不包括网络传输速度！

## 共享浏览器

池将允许我们保持少数浏览器实例打开，并在每个截图请求中重用它们。Puppeteer 没有内置的池解决方案，但是有一些通用的库可用。我们将使用`[generic-pool](https://www.npmjs.com/package/generic-pool)`。

第一个池:

但是等等，我们也不需要为每个截图创建页面和设置视窗。让我们共享页面而不是浏览器:

让我们更新我们的`getChartImage`函数，从池中请求页面:

后续截图平均时间现在降到了~1.58 秒！*(出于好奇，在没有页面的情况下，共享浏览器的平均时间是 1.87 秒)*

> 更新:echojs 的 Michael J. Ryan 敏锐地指出,你可以走得更远，让页面集中在一个浏览器上。浏览器实例的启动时间将会缩短。Chrome 为每个标签/页面创建了一个独立的管理/运行过程！”[这里有一个链接，链接到一个更有效的要点 getBrowserPool](https://gist.github.com/anonymous/c3d9b0d51127ea0bd396432075b2990a)

## 利用“单页性”

SPAs 的优势之一是浏览器不必在导航时重新加载所有资源和重新解析所有脚本。然而，通过每次调用`page.goto`，我们在同一个应用内导航时不必要地触发了整个页面的重新加载。

这个问题的解决方案根据应用程序使用的框架和路由库而有所不同，但基本思想相当简单且可翻译

1.  在前端，公开路由功能，该功能将允许木偶师从全局上下文(即`window`)触发路线导航
2.  同样在前端，做一个标志，让木偶师知道路线转换何时完成。
3.  木偶师将标志翻转到`false`，调用路由函数，并轮询标志的值，直到前端将其翻转到`true`(表示路由更改完成)。

如果你的应用程序使用 React 和 React-Router v4，你可以在应用程序的某个地方使用`withRouter`来请求`history`对象，然后将它粘贴到窗口中。例如

在我使用 Vue 1.0 和 vue-router 0.7 的情况下，我将这一行添加到根组件的`ready`钩子中:

路由转换完成(数据加载和渲染完成)后，前端将翻转标志，向回发出信号，表示它已准备好拍摄屏幕截图:

让我们更新 getChartImage 以使用这些挂钩

一张截图现在只需要~860ms！我们已经设法将时间缩短到不到初始实施的四分之一。

我们还可以做一件事—

## 记忆

如果我们不必在每次访问这些图像时都生成它们，我们会节省更多的资源。

当有截图请求时，我们想—

1.  检查该资源的屏幕截图是否已经存在。
2.  如果是，检查它是否过时，需要更新。
3.  如果它存在并且没有过时，则直接返回该文件。
4.  如果它不存在或需要更新，请创建一个新的快照。

就是这样！在某个时间段内的后续请求只需要 0.3 毫秒。下一步将是从 CDN 而不是本地文件系统保存和提供它，但我认为目前这已经足够好了:)数字海洋的 droplet 附带 25g 的 SSD 和 1TB 的传输，将在我需要时保存它。

希望这是有帮助的！非常感谢 [Ben Hare](https://twitter.com/hare_ben) 和 [Jeffrey Burt](https://twitter.com/jephuff) 对草稿的反馈，以及 [Michael J. Ryan](https://twitter.com/tracker1?lang=en) 的建议和更正！

请到我的网站[npmcharts.com](https://npmcharts.com)查看你所有的 npm 包比较需求！以下是 webpack、browserify、rollup 和 package 中的一个:—

[Project source available on Github.](https://github.com/cheapsteak/npmcharts.com) Thanks to [@osdevisnot](http://twitter.com/osdevisnot) for pointing out the previous chart included “parcel”, which should have been “parcel-bundler” instead!

![](img/afbddd178e9dacb217be56e70226c8a8.png)

🐥[发微博给我@Cheapsteak](https://twitter.com/CheapSteak)

*🦈* [*数字海洋 10 美元推介链接*](https://m.do.co/c/91557a2689b0)