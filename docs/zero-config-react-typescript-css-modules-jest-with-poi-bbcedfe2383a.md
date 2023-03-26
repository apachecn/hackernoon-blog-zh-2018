# 零配置 React + Typescript + CSS 模块+ JEST with Poi

> 原文：<https://medium.com/hackernoon/zero-config-react-typescript-css-modules-jest-with-poi-bbcedfe2383a>

![](img/1d46d72db5867c898fa9a3161d49f671.png)

这篇文章最初发表在我公司的博客上。我们在 Appfocused 的使命是通过利用我们丰富的经验、对现代 UI 趋势的了解、最佳实践和代码工艺，帮助公司在 web 上实现 [*卓越的用户体验。*](https://www.appfocused.com)

Poi.js 是一个取代初学者工具包和样板项目的工具。Poi 的承诺是“不再有配置地狱”。在*几乎没有*配置的情况下，它提供以下内容:

*   自动传输和捆绑(使用 webpack 和 babel/postcss)
*   热代码重载
*   `./static`中的文件被复制到 dist 文件夹

我们将尝试进一步推动它，并获得一个稍微高级的配置来与 *Poi* 一起工作。在写作的时候，我的技术栈偏好是使用 [CSS 模块](https://hackernoon.com/tagged/css-modules)(在 *create-react-app* 中还没有现成的，没有弹出功能)用 [Typescript](https://hackernoon.com/tagged/typescript) 进行反应，并用 Jest 和 Enzyme 进行测试。

这就是我们即将实施的工作流程，它应该不会超过 15 分钟。系好安全带，我们开始吧。

# Poi

首先在您的项目中安装 Poi 工具本身:

`npm install poi -D`

接下来我们需要运行 Poi，我们将使用 npx 来帮助我们。Npx 允许在本地项目中完美地执行节点二进制文件。如果你还没有安装它，让我们把它分类。

`npm install npx -g`

如果您现在尝试运行`npx poi`，您会看到一个错误，因为根目录中仍然没有 *index.js* 文件。现在，让我们只添加一个空文件来让 Poi 高兴:

`touch index.js`

# 反应

我们有一些东西，但我们需要我们的超级反应能力。
这是我们通常通过互联网连接获得的信息:

`npm install react react-dom --save`

Poi 有现成的 Vue.js 配置，对于其他任何东西，您都需要一个预置。幸运的是，有一个 React:

`npm install poi-preset-react -D`

所有预设都在 *poi.config.js* 中连接，这是我们启动并运行 React 所需的唯一配置。至今印象深刻！

poi.config.js

接下来，我们添加一个简单的 *App.jsx* 文件来测试 jsx 是否运行良好

App.jsx

最后，我们需要为 react 应用程序添加一个强制的 *index.js* 内容，以在 DOM 中呈现 *App* 组件，从而开始享受#0CJS React 的热模块替换。

index.js

# 以打字打的文件

下一步是让 React 使用 Typescript。
为了完成这项任务，我们需要以下物品:

*   添加类型脚本
*   添加 *tsconfig.json*
*   为 typescript 添加一个 poi 预置并连接它
*   添加缺少的类型定义
*   添加 React-Typescript 组件

让我们首先安装 Typescript 和 Poi 预置作为我们项目的开发依赖项。

`npm install typescript poi-preset-typescript -D`

我们已经知道如何连接 Poi 预设，对吗？

接下来是一个简单的 *tsconfig.json* ，Typescript 需要它来更好地理解您希望它如何操作。让我们想出一个:

tsconfig.json

最后，我们将把我们的 *App.jsx* 重命名为 *App.ts* ，并添加缺失的 react 类型

`npm install @types/react -D`

下面是我们的新组件的外观:

App.tsx

# CSS 模块

Poi 文档说以 *.module.css* 结尾的文件*. module . scss**. module . less*默认应该支持 css 模块。

我们添加一个文件 *App.module.css* ，导入到 App.tsx 中:

App.module.css

App.tsx

当我们将 css 模块导入文件时，TS 会不高兴，会告诉我们 *[ts]找不到模块。/App.module.css*

让我们也解决这个问题，并添加一个 *global.d.ts* 文件，其中包含以下行:

`declare module '*.css';`

斑点狗！我们现在有了一个带有 typescript 和 css 模块的 react 应用程序。您也可以在终端中发出`npx poi build`命令，将它构建到一个 *dist/* 文件夹中。

# 用 Jest 和 Enzyme 进行单元测试

安装带有类型的 *jest* 和 *jest-css-modules* 以使 webpack 和 jest 与 css 模块配合良好:

`npm install jest [@types/jest](http://twitter.com/types/jest) ts-jest jest-css-modules`

添加酶及其所有适配器和类型:

`npm install enzyme @types/enzyme enzyme-adapter-react-16 @types/enzyme-adapter-react-16 -D`

接下来我们需要为*酶*配置适配器:

setupTests.ts

并将一些 *jest* 配置添加到我们的 *package.json* 中，使其能够很好地与我们的类型脚本文件和 css 模块配合工作。

package.json

最后，让我们为我们的 *App.tsx* 组件编写一个简单的单元测试，并将其放入 *__tests__* 文件夹:

App.spec.tsx

`npx jest`将运行我们的测试，瞧，我们一切都是绿色的！我们现在准备编码一些重要的东西。