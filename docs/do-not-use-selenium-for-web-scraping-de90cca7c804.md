# 请勿使用 Selenium 进行网络刮擦

> 原文：<https://medium.com/hackernoon/do-not-use-selenium-for-web-scraping-de90cca7c804>

**发布于**:2018 年 12 月 15 日

声明:
这主要是**从 Python 编程语言生态系统的角度**写的。

我注意到 [Selenium](https://www.seleniumhq.org/) 已经变得[相当流行](/the-andela-way/introduction-to-web-scraping-using-selenium-7ec377a8cf72)用于从网页上[抓取](https://towardsdatascience.com/web-scraping-using-selenium-python-8a60f4cf40ab)数据。

是的，**你可以使用 Selenium 进行网页抓取，但这不是一个好主意**。

另外，我个人认为那些教授如何使用 [Selenium](https://hackernoon.com/tagged/selenium) 进行网页抓取[的文章给了一个**使用什么工具进行网页抓取**的坏例子。](https://hackernoon.com/tagged/scraping)

# 为什么不应该使用 Selenium 进行网络抓取

第一， **Selenium 不是网页抓取工具**。

它是**“用于测试目的的自动化 web 应用程序”**，这个声明来自 Selenium 的主页。

第二，在 Python 中，有一个更好的工具 [Scrapy](https://scrapy.org/) 开源**网页抓取框架**。

聪明的读者会问:“**使用 Scrapy 比使用 Python 有什么好处？**

**你得到速度和很多速度**(不是安非他命:-)，开发速度和网页抓取时间速度。

有一些关于如何使[硒网](/dreamcatcher-its-blog/5-simple-tips-for-improving-automated-web-testing-or-efficient-web-crawling-using-selenium-python-43038d7b7916)刮[更快](https://stackoverflow.com/questions/39036137/how-yo-make-a-selenium-scripts-faster)的提示，并且**如果你使用 Scrapy，那么你就不会有那些问题，你会更快**。

仅仅因为这些文章的存在就证明了(至少对我来说)人们在工作中使用了错误的工具，一个“当你唯一的工具是锤子时，一切看起来都像钉子”的例子。

# 你应该用硒做什么

我个人只用 Selenium 进行**网页测试**。

我**会尝试用它来自动化 web 应用**(如果没有其他选择的话)，但是我至今还没有那个用例。

# 何时可以使用 Selenium 的例外

我认为使用 Selenium 作为网络抓取工具的唯一例外是**，如果你抓取的网站使用 JavaScript 来获取/显示你需要抓取的数据**。

Scrapy 确实有针对带有 [Splash](https://github.com/scrapinghub/splash) 的 JavaScript 的[解决方案，但是我从来没有用过，到目前为止我总能找到一些变通方法。](https://blog.scrapinghub.com/2015/03/02/handling-javascript-in-scrapy-with-splash)

# 用什么代替 Selenium 进行网页抓取

你可以猜到，我的建议是用[刺儿头](https://scrapy.org/)。

**我选择 Scrapy 是因为我花在**开发网络抓取程序(网络蜘蛛)的时间少，执行时间快。

我发现 Scrapy 在开发时间上更快，因为有了 [Scrapy 外壳](https://doc.scrapy.org/en/latest/topics/shell.html)和[缓存](https://doc.scrapy.org/en/latest/topics/downloader-middleware.html#module-scrapy.downloadermiddlewares.httpcache)。

在执行中，它**很快，因为可以同时完成多个请求**，这意味着[的数据传递不会按照请求](https://stackoverflow.com/questions/50229856/making-scrapy-request-deteministics)的顺序进行，只是你在调试时不会感到困惑。

# 那么[美汤](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) + [请求](http://docs.python-requests.org/en/master/)呢

在我决定花时间学习 Scrapy 之前，我曾经使用过这个组合。

**不要犯和我一样的错误**，Scrapy 的开发时间和执行时间比我目前发现的任何其他工具都要快。

# 临终遗言

这不是对使用 **Selenium 进行网页抓取的咆哮，对于非生产系统和学习/爱好来说这是好的**。

我明白了， **Selenium 很容易启动，你可以在屏幕上实时看到正在发生的事情**，这对开始做/学习网络抓取的人来说是一个巨大的好处，当你学习新东西时，拥有这种**早期道德规范**是很重要的。

但是我确实认为所有这些使用 Selenium 进行 web 抓取文章和教程应该有一个免责声明**不要在现实生活中使用 Selenium(如果您需要在一天内抓取 100K 个页面，在单个 Selenium 实例中是不可能做到的)**。

从 Scrapy 开始更难，你必须编写 XPath 选择器并查看 HTML 页面的源代码进行调试并不有趣，**但如果你想拥有快速的网络抓取，这就是代价**。

# 结论

**在你学会 Scrapy 后，你会比用 Selenium 更快** (Selenium 只是有一个低角度的学习曲线)，我个人需要几天时间来获得基础知识。

*原载于 2018 年 12 月 15 日*[*buklijas . info*](http://buklijas.info/blog/2018/12/15/do-not-use-selenium-for-web-scraping/)*。*