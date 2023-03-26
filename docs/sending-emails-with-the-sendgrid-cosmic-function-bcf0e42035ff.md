# 使用 SendGrid Cosmic 功能发送电子邮件

> 原文：<https://medium.com/hackernoon/sending-emails-with-the-sendgrid-cosmic-function-bcf0e42035ff>

![](img/0510a31fc1d54d24322fcbddf482f897.png)

我们最近发布了[宇宙函数](https://cosmicjs.com/docs/functions)(公测版)。我们很高兴能帮助团队利用新的无服务器解决方案构建令人惊叹的现代产品。

在这个简短的教程中，我将向您展示如何启动并运行 [SendGrid 电子邮件功能](https://github.com/cosmicjs/send-email-function)以通过 SendGrid 发送电子邮件(要查看更多示例功能，请登录并转到*Your Bucket>Settings>Functions*)。

# 部署功能

**1。安装功能**
要安装 SendGrid 功能，进入*你的桶>设置>功能*，找到 SendGrid 功能，点击“安装功能”。

**2。添加密钥，并部署**
安装完该函数后，您将被重定向到添加 AWS 凭证和环境变量的页面。按照步骤添加您的 AWS 凭证(如果您手头没有的话)。

转到您的 SendGrid 帐户，在[设置> API 密钥](https://app.sendgrid.com/settings/api_keys)中找到您的 API 密钥(您可能需要创建一个新的)，并将其作为值添加到 SENDGRID_API_KEY。

添加密钥后，单击“部署函数”,一分钟后您的函数将被部署并为请求做好准备。

# 编写 Web 联系人表单代码

接下来，让我们创建一个联系人表单来访问新部署的端点。按照以下步骤安装[反应启动器](https://cosmicjs.com/getting-started#quickstart):

```
npm i -g cosmic-cli
cosmic init react-starter
cosmic develop
```

现在转到您的应用程序代码库，转到位于`pages/default.js`的默认页面组件，编辑它，如下所示:

**注意这里的几件事:** 1。我们添加了一个`form`元素来接收电子邮件、名字和姓氏的输入。
2。我们添加了`handleSubmit`方法来处理表单提交，该方法获取表单的值并将数据发送到我们的宇宙函数端点。

**就这样！现在我们有了一个端点，它接收来自表单的数据，并将其发送到 SendGrid API 进行处理。**

# 利益

更少的代码我们不必担心在我们的应用程序中构建 API 端点来将数据发送到 SendGrid。

更少的配置麻烦
我们不必担心配置麻烦，比如可能泄露客户端的密钥。我们不需要与任何人共享 API 密钥，只需要与端点共享。Config 在 Cosmic 函数中作为环境变量处理。

可重用的
我们可以在任何其他需要发送电子邮件的应用程序中使用这个端点。

我希望你喜欢这个 SendGrid 功能的快速浏览，现在已经准备好安装和部署了(转到*你的桶>设置>功能*)。如果你有任何问题或意见，请告诉我。[在 Twitter 上联系我们](https://twitter.com/cosmic_js)和[加入我们的 Slack 社区](https://cosmicjs.com/community)。