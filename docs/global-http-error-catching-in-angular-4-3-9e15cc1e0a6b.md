# Angular 4.3+中的全局 HTTP 错误捕获

> 原文：<https://medium.com/hackernoon/global-http-error-catching-in-angular-4-3-9e15cc1e0a6b>

每当你做一个 HTTP 请求时，都有可能出错。如果事情出错，请求将返回一个带有错误代码的响应。例如，基于这个错误代码，您想要通知用户哪里出错了。现在，如果您可以将这个逻辑放在一个地方，并在任何地方重用它，会不会很方便？请继续阅读！

![](img/f047f7626266238ced200dc0583b517b.png)

Photo by [Duffy Brook](https://unsplash.com/photos/qzQP8Tbro_Q?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/train-interception?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

如果您在请求中附加了一个`catch`，那么您只能处理 [http](https://hackernoon.com/tagged/http) 错误。这可能看起来像这样:

当应用程序增长时，HTTP 请求的数量也会增加。给每个请求附加一个`catch`并不完全是枯燥的。从 4.3 开始，引入了新的 HttpModule，我们终于可以再次使用传统的拦截器了。

# 使用 http 拦截器

当使用一个 http 拦截器时，基本的思想是所有在你的模块中产生的请求都通过这个拦截器路由。这意味着您可以为每个请求自动添加或删除属性。当您与一个受保护的 API 对话，并且需要在每个请求中发送一个 api-key 时，这是非常方便的。

因为你发出的每个请求都要经过这个拦截器，所以我们也可以使用这个拦截器来捕捉所有作为`HttpErrorResponse`实例返回的请求。你应该这样做:

我们向`HttpHandler`发送请求，然后执行请求。在回调函数中，我们检查错误。如果错误是`HttpErrorResponse`的一个实例，我们可以将请求发送给一个错误处理程序，它会显示一条关于出错原因的小消息。

在下面的 stackblitz 中，您可以看到一个完整的工作示例。

# 这没有抓住什么

正如前面几次提到的，这种拦截方式只会捕获带有实例`HttpErrorResponse`的 http 响应。这意味着，如果您在请求中创建了一个`map` 来格式化您的响应并在其中出错，它将不会捕捉到它。

# 结论

当您的应用程序非常依赖与外部 rest API 的交互时，使用这个拦截器设置特别有用。如果 API 设置正确，当请求不正确时，它会返回一个很好的错误消息。该消息现在可以容易地显示和展示给用户。如果您对 Http 请求进行了大量的客户端格式化，您仍然应该创建逻辑来处理潜在的错误。

# 在阿姆斯特丹找工作？

我在 Sytac 工作，是一名高级前端开发人员，我们正在寻找精通 Angular、React、Java 或 Scala 的中级/高级开发人员。Sytac 是荷兰一家雄心勃勃的咨询公司，为银行、航空、政府和零售部门的许多知名公司工作。你可以想到荷兰国际集团、荷兰皇家航空公司、德勤公司、阿霍尔德·德尔海兹公司、ABN·AMRO 公司、弗洛拉·荷兰公司等等。

从个人的观点来看，Sytac 确实与他们的客户组合不同，但也与他们如何照顾他们的员工不同。他们真的很关心员工的福利。除了丰厚的薪水(50K-75k)，你会在与咨询经理的定期会议中注意到这一点，还会从他们组织的活动数量以及他们为让所有员工满意而提供的所有其他津贴中注意到这一点。

如果你认为自己具备与最优秀的人共事的条件，请发邮件至 [luuk.gruijs@sytac.io](mailto:luuk.gruijs@sytac.io) 给我，我很乐意告诉你更多。