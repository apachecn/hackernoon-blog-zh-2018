# JavaScript ✨中异步样板文件的解决方案

> 原文：<https://medium.com/hackernoon/a-solution-to-async-boilerplate-in-javascript-2fa717801c3b>

![](img/3290ff11d4faf569c3f9b96da6daa850.png)

[Source](https://en.wikipedia.org/wiki/Tuxedo#/media/File:Dinner_Jackets,_1898..jpg)

你写过类似这样的代码吗？

也许你也有错误处理或一个奇特的延迟微调，增加了更多的复杂性。为项目中的每个异步任务编写相同的代码会增加很多样板文件，尤其是当应用程序中有很多 HTTP 请求时。

既然是这样一个常见的问题，[几种](/stashaway-engineering/react-redux-tips-better-way-to-handle-loading-flags-in-your-reducers-afda42a804c6) [解决方案](/@lachlanmiller_52885/a-pattern-to-handle-ajax-requests-in-vuex-2d69bc2f8984) [已经](https://github.com/f/vue-wait) [已经](https://gist.github.com/ddanger/21d7f4bd3580d2041b7c56ca04b25b8b) [已经](/@Farzad_YZ/handle-loadings-in-react-by-using-higher-order-components-2ee8de9c3deb) [提出](https://github.com/abdullah/vuex-module-generator)。事实上，React 团队目前正在通过 [React 悬念](/@baphemot/understanding-react-suspense-1c73b4b0b1e6)在 React 本身中构建一个解决方案。有了所有这些潜在的解决方案，看起来样板文件似乎是一个已经解决的问题。

然而，几乎所有这些解决方案都被绑定到一个框架上。这不一定是个问题——框架也可以很棒！然而，最好有一个能够使用纯 JavaScript 的解决方案。这将使您可以在任何情况下跟踪异步请求的状态:无论是在您选择的 UI 框架、普通 JS 还是您最喜欢的状态管理解决方案中。

例如，我用 Vue 做了很多工作，我发现了一些与 Vuex 配合得很好的解决方案。但是，如果我想在组件的状态中而不是在全局存储中跟踪请求的状态，它们根本不会工作。大多数 React 解决方案都有类似的问题。另一方面，一个纯 JavaScript 解决方案将能够处理异步任务的所有复杂性，同时仍然足够灵活，可以与几乎所有的前端技术栈一起工作。

我最近发布了 [tuxi](https://github.com/superMDguy/tuxi) ，它旨在解决异步状态样板问题，同时仍然满足我概述的灵活性需求。虽然它有一个非常简单的 API，但它做了一些非常酷的事情:

*   通过区分“未决”任务和“旋转”任务，可配置延迟微调器。
*   处理快速触发请求的多个实例的情况:只返回最近触发的请求的数据。
*   它还可以处理一个请求的多个实例，其中每个请求的状态被分别存储和访问。
*   通过插件支持 Vue 和 Vuex，没有反应或严格的模式错误。
*   有一个插件 API，可以在不做太多工作的情况下增加与 React 和 Redux 的集成(React 插件的开放问题[在这里](https://github.com/superMDguy/tuxi/issues/1)，如果你感兴趣并想给出一个👍).

# 例子

## 纯 JavaScript

## 某视频剪辑软件

## Vuex

# 最终注释

Tuxi 还是比较新的，但是相当稳定。它有 [100%](https://codecov.io/github/superMDguy/tuxi?branch=master) [通过](https://circleci.com/gh/superMDguy/tuxi/tree/master)单元测试覆盖，我正在生产中使用它。虽然我还没有写完整的文档，但是你可以阅读[测试](https://github.com/superMDguy/tuxi/tree/master/tests)来获得完整的使用示例。此外，如果您有任何问题、请求或建议，请随时提出问题，我会尽快回复您。

更新(10/11/2018):我写了[文档](https://github.com/superMDguy/tuxi/blob/HEAD/docs/readme.md)。