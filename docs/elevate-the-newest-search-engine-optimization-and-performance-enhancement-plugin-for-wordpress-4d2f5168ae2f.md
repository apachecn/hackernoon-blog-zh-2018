# elevate:WordPress 最新的搜索引擎优化和性能增强插件

> 原文：<https://medium.com/hackernoon/elevate-the-newest-search-engine-optimization-and-performance-enhancement-plugin-for-wordpress-4d2f5168ae2f>

大约六个月前，我坐在摩洛哥，为一个客户开发一个网站。我在网站上遇到的第一个问题是，尽管网站所有者安装了一个流行的 [SEO](https://hackernoon.com/tagged/seo) 插件，但它没有正确配置。

当然，它是在生成一个 XML 站点地图——但是它并没有真正在任何地方被使用。谷歌搜索控制台没有为该网站正确配置，这意味着 XML 网站地图没有提交到那里。此外，没有安装访问者跟踪脚本，所以网站所有者真的不知道他们的流量是多少。主站点标题，也就是谷歌上显示的标题，没有设置任何有意义的内容，这意味着这个网站不太可能排名靠前。

就在那时，我开始思考——难道没有更好的方法来做这些事情吗？为什么执行 SEO，甚至建立一个网站，必须如此复杂和容易出错？我开始做笔记——对于一个不太了解搜索引擎如何工作的人来说，如何才能成功地优化他们的搜索网站？

那时，我开始尝试一些想法，这些想法最终会融合成一个 WordPress 插件。现在，这个插件对每个人都是免费的。

所以不多说了，我想向你介绍一下 Elevate for WordPress。

![](img/dac8e32dfae917c25e50705af69e964d.png)

Elevate 的核心是一个插件，旨在帮助提高自托管 WordPress 网站的搜索排名。但它不仅如此，它还与一些服务(如谷歌)深度集成，以帮助自动化流程，并持续了解您的网站的运行情况。

例如，由于你的网站的速度(它对你的访问者有多负责任)被谷歌和其他搜索引擎用来调整你的排名，所以在任何时候了解你的网站有多快都是很重要的。Elevate 汇总了所有这些信息，以及重要的搜索指标(比如上周你的网站从 Google 获得了多少点击，等等。)就在 WordPress 管理面板的主 Elevate 仪表板上。

![](img/7cb0465a534e8b7a43fa85df1fb11bc5.png)

The Elevate Dashboard, with tie-ins to Google Search Console and Page

一旦完成配置，作为网站所有者，您将始终拥有所需的信息，以了解您的网站如何运行，无论是从速度角度，还是从搜索引擎角度。

使用 Elevate 做了大量工作的一个领域是自动配置领域。事实证明，在网站上可以做很多事情来自动配置它，而不需要最终用户的太多输入。例如，使用 Google 的 OAuth 机制，可以在 Google 搜索控制台上验证站点并自动提交站点地图。因此，Elevate 会在安装过程中自动为每个人完成这项工作。

![](img/67745c969a39e984c48ba4fa637995de.png)

Configuring your website in the intuitive interface

此外，如果它发现一个分析脚本可以通过你的谷歌帐户使用，它会抓取代码并使用它。如果找不到，它可以创建一个全新的分析属性，并自行使用。所以不要再去谷歌然后把你的代码剪切粘贴到 WordPress。

![](img/ddd87602b25a177159ae7b96c7f6a34a.png)

Elevate will automatically verify the site in Google Search Console, create a new Analytics property, and then configure the website to use it. No more messing around with scripts or manually setting this up around the web

在安装向导期间，我尽力去解决每一个具体的障碍，这些障碍会限制一个站点成功的能力。例如，我添加的最后一个项目是我觉得很重要的一个，以至于我绕了一圈才添加它，即使我已经到了我想要启动的地方——为整个网站设置网站范围的特色图像的能力。如今，大多数人都是根据每篇帖子建立一个形象，当你的访客分享你的帖子时，这个形象就会出现在脸书和 Twitter 上。

但是当他们分享内容而没有一张清晰的照片时会发生什么呢？这实际上是一个非常合理的情况，例如当有人在 WordPress 上分享一个分类页面，或者甚至是你网站的主页。让用户在安装过程中为他们的整个网站显式声明一个后备图像解决了这个问题，并确保网站的品牌将始终有一个默认状态，即使没有添加特色帖子。

![](img/e70b186feb63720f627e4dc71ff04137.png)

Elevate isn’t just about helping search engines find your contact, but also re-enforcing your brand all around the web

我努力工作的另一个领域是编辑文章内容时可用的实际选项。在我为客户服务过的大多数网站上，他们很少会明确地为每个帖子调整搜索信息。知道这一点后，我开始为搜索信息创建智能默认值。例如，Elevate 将智能地扫描帖子内容，并动态填充标题和描述字段。如果网站所有者想要进行调整，这些都是可见的，并且很容易编辑。

![](img/92f018939621a791d34522cf1852f2e3.png)

基于每个帖子调整搜索信息；智能默认值由占位符文本表示。

这些内容有效地展示了如果网站所有者发布内容而不为 SEO 做任何事情会发生什么，不管喜欢与否，这是经常发生的情况。因此，希望有了可以在管理面板中设置的智能默认设置和策略，这些搜索参数对网站所有者变得更加有用。

对于我在开发和测试周期中尝试的许多帖子来说，默认内容通常已经足够了。但是向前看，很高兴知道你可以很容易地调整每个帖子的搜索信息。

# Web 预览

我充实的另一个领域是预览你的内容在互联网上的外观的能力。所有内容作者的主要目标之一是让他们的内容被阅读和分享，所以网站所有者了解这些内容被分享后的样子是很重要的。

![](img/7791d80409b4b33322214c9bde1fdd13.png)

一个 web 预览窗口小部件的例子，展示了内容在 Twitter 上的样子

使用“网络预览”小工具可以预览这一效果，它会实时向你展示你的内容在 LinkedIn、Twitter、脸书、谷歌等网站上分享时的效果。

要在各种状态下(草稿与已发表的文章)即时完成这项工作，同时支持 Classic 和 Gutenberg 编辑器，实际上是相当棘手的。但最终结果不言而喻——只需简单的点击，您就可以快速预览您的内容在互联网上的任何地方的外观，并即时进行调整。

![](img/2562298b07e59bdca5caa2f5057d4c62.png)

Elevate and its Gutenberg integration

说到古腾堡，Elevate 将与它一起开箱即用。我们有一些关于如何更好地与 Gutenberg 集成的好主意，但请放心，它今天工作得很好，可以用于预览和生成搜索相关的内容。

# 成品

创建 Elevate 花了大约六个月的兼职时间，大部分时间是在周末的晚上四处涉猎。我真的为最终的结果感到骄傲，并计划再开发几个月的功能，让它变得更有用、更有吸引力。我的目标是让它成为与网站优化和搜索+社交媒体增强相关的一切的首选插件。接下来的一些改进将是自动配置和网站速度的提高，希望在未来几周内实现。

我希望你喜欢使用 Elevate，就像我喜欢创建它一样。如果您有任何问题，请联系我，让我知道是什么问题。由于这是新软件，测试版仅限于 10 个人，我怀疑在接下来的一两周内仍然会有一些错误出现。但请放心，我会及时解决这些问题，并在接下来的几周内开始推出一些很棒的新功能。

关于 Elevate 的更多信息，请访问 WordPress 的主[Elevate](https://elevatewp.io/)页面或其在 WordPress.org[库](https://wordpress.org/plugins/elevate-seo/)的页面。如果你有任何问题，请联系我，我会帮助你解决。