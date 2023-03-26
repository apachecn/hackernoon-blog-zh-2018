# 使用 Cosmic JS 部署一个简单的 Vue 博客

> 原文：<https://medium.com/hackernoon/deploy-a-simple-vue-blog-using-cosmic-js-ffd04df66873>

![](img/5a0506c1d142c38f1d38cb50541c98ba.png)

在这篇博客中，我将分三步演示如何部署一个简单的 Vue 博客。这个简单的博客应用程序是使用 Vue & Vuex 构建的。它通过 API 连接到[宇宙 JS CMS](https://cosmicjs.com/) 。你可以从你的宇宙 JS 仪表板管理你的内容。简单的 Vue 博客包括由 [Disqus](https://disqus.com) 支持的评论。从你的宇宙 JS 桶仪表板安装和管理你的简单 Vue 博客和它的所有内容。简单。😎可以参考下面的[原创教程](https://cosmicjs.com/articles/how-to-build-a-simple-blog-using-vue-cosmic-js-and-deploy-to-netlify)从头开始搭建 app，也可以继续阅读简单 3 步部署简单 Vue 博客。

# TL；速度三角形定位法(dead reckoning)

[简单 Vue 博客](https://cosmicjs.com/apps/simple-vue-blog#)
[简单 Vue 博客演示](https://cosmicjs.com/apps/simple-vue-blog/demo)
[简单 Vue 博客代码库](https://github.com/cosmicjs/vue-blog-cosmicjs)
[如何使用 Vue、Cosmic JS 构建简单博客并部署到 Netlify](https://cosmicjs.com/articles/how-to-build-a-simple-blog-using-vue-cosmic-js-and-deploy-to-netlify)

![](img/d3544043e83ff6d9a8c3d688a8bf086a.png)

我们将使用 [Cosmic JS](https://cosmicjs.com/) 来安装我们的示例应用程序，部署并进行内容更新。Cosmic JS 是一个 API 优先的内容管理平台，允许开发人员以任何编程语言构建应用程序，同时为内容编辑器提供一个熟悉的内容编辑器来管理来自云的动态内容。如果你还没有，那就从[注册](https://cosmicjs.com/signup)Cosmic JS 开始吧。

# 1.创建新的存储桶

![](img/f1e54700eafa69090acace73ad6a57b1.png)

您的 bucket 的名称是您正在构建的网站、项目、客户端或 web 应用程序的名称。

# 2.安装简单的 Vue 博客

![](img/6afe7aef64cc16c6ddaa03fd440eb117.png)![](img/55141ce60411a3d63547c3fa6dec5525.png)

[Cosmic JS](https://cosmicjs.com/) 让你能够在 Node.js、Vue.js、React、AngularJS 等编程语言之间进行筛选。

# 3.部署到 Web

导航至位于 Bucket Dashboard 左侧导航栏设置下方的“部署 Web 应用程序”。我点击了“部署到 Web”。然后，我可以在部署 web 应用程序时编辑对象。您将收到一封电子邮件，确认您的 web 应用程序的部署。如果您在部署过程中遇到任何问题，您可能会被转到 [Cosmic JS 故障排除页面](https://cosmicjs.com/troubleshooting)。

# 确认部署位置和分支

![](img/7a9ea155b6566813a852f92c299ad9e2.png)

[https://github.com/cosmicjs/vue-blog-cosmicjs](https://github.com/cosmicjs/vue-blog-cosmicjs)

我使用的是来自 Cosmic JS 社区的 Jazib Sawar 的内容就绪应用程序，所以我将使用他的主报告:

[](https://github.com/cosmicjs/vue-blog-cosmicjs) [## cosmicjs/vue-blog-cosmicjs

### 这是一个使用 Vue & Vuex 构建的简单博客应用程序。它通过 API 连接到 Cosmic JS CMS。您可以管理…

github.com](https://github.com/cosmicjs/vue-blog-cosmicjs) 

# 部署分支机构确认模式

![](img/526b747134bb7ebcce440a5bd11aebfb.png)

[https://github.com/cosmicjs/vue-blog-cosmicjs](https://github.com/cosmicjs/vue-blog-cosmicjs)

现在你的应用已经部署到了 Cosmic JS 应用服务器，你可以自由地从一个地方完全管理你简单的 Vue 博客及其所有内容。

[Cosmic JS](https://cosmicjs.com/) 是一个 API 第一的基于云的内容管理平台，使管理应用程序和内容变得容易。如果你对 Cosmic JS API 有任何疑问，请通过 [Twitter](https://twitter.com/cosmic_js) 或 [Slack](https://cosmicjs.com/community) 联系创始人。

[卡森·吉本斯](https://twitter.com/carsoncgibbons)是[宇宙 JS](https://cosmicjs.com/) 的联合创始人& CMO，宇宙 JS 是一个 API 第一的基于云的[内容管理平台](https://cosmicjs.com/)，它将内容与代码分离，允许开发人员用他们想要的任何编程语言构建流畅的应用程序和网站。