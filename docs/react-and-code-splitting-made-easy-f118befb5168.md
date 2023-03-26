# 反应和代码分割变得容易

> 原文：<https://medium.com/hackernoon/react-and-code-splitting-made-easy-f118befb5168>

代码分割一直是个问题。从那时起，当你添加大量不同的脚本标签，直到 RequireJS 出现。

代码分割一直存在，被支持，被使用，让我们跳过“如何”支持和使用它的问题。无论如何——从海外带来代码并执行清关——这是可以讨论的。

![](img/dad984d22e751d8c7f3f7358cf174eb1.png)

Importing components….

所以，让我们跳过历史课，直接跳到一个动作的开始。

## Webpack 2 出来了，给了一个导入。

代码分割(又一次)成为一件事，下周我们有一群“反应加载器”。你最好问的第一个问题很简单

> 为什么我需要“加载器”？

所以，你应该可以接受 npm 包或 React 组件只用于*一切*。为什么不给装载机装一个特殊的部件呢？

这种组件最简单的代码是简短的。

还有什么？这个组件实际上是做一些状态处理，并为你的应用提供代码分割。你可以照原样使用它。

只需添加一些错误处理，重试加载、预缓存、测试的能力，当您加载或未能加载时显示某些内容，您将获得一个真正的 react 组件。你应该只写一次，或者最好不要花时间使用现有的解决方案——一些来自开源的库。

## 那好吧。怎么挑那个库？

如果你去 github 搜索一个库——你会找到几个，他们会选择你要用的那个。

> 有什么区别？如何选择？

首先——忽略星级计数、提交、活动等等。“装载机”不是火箭，正如我们刚刚发现的——这是 **22 行稀疏代码**。

第二—看 API。你要用，API 要“适合”你。实际上，API 有两种“形式”。

```
1\. loadable(() => import('./Home'))
2\. loadable({ loader: () => import('./Home') })
```

你可以在 [react-imported-component](https://github.com/theKashey/react-imported-component) 、[react-universal-component](https://github.com/faceyspacey/react-universal-component)和[loadable-component](https://github.com/smooth-code/loadable-components)中找到第一个，而第二个— [react-loadable](https://github.com/jamiebuilds/react-loadable) 和 [react-async-component](https://github.com/ctrlplusb/react-async-component) 。

在“额外的”选项中也有一点小小的不同，你可以传递到加载器中

1.  可加载元件-您可以指定加载时要显示的内容，在发生错误时，或者如何显示元件。

```
const Home = loadable(() => import('./Home'), {
  LoadingComponent: Loading,
  ErrorComponent: ErrorDisplay,
  render: ({ Component, loading, ownProps }) => {...})
});
```

Loadable-components 还提出用第三方工具来处理延迟和超时。

[](https://github.com/smooth-code/loadable-components) [## 平滑代码/可加载组件

### 可加载组件反应代码分裂，使简单的✂️✨

github.com](https://github.com/smooth-code/loadable-components) 

2.React-imported-component 实际上有相同的 API，只是引入了额外的 onError 属性。最好参考可加载组件自述文件来理解如何处理反应导入组件。

[](https://github.com/theKashey/react-imported-component) [## kashey/react-导入组件

### react-imported-component-Bundler 独立于任何延迟组件的解决方案，以实现更好的 SSR、代码拆分和…

github.com](https://github.com/theKashey/react-imported-component) 

3.React-async-component 也是一样的。有很多有趣的问题和答案。

[](https://github.com/ctrlplusb/react-async-component) [## ctrl plusb/react-async-组件

### 异步解析组件，支持代码分割和高级服务器端…

github.com](https://github.com/ctrlplusb/react-async-component) 

4.React-loadable 略有不同，首先接受的不仅仅是“导入”函数，而是由最终组件解决(即它可以处理“映射”)，将 LoadingComponent 和 ErrorComponent 混合在一起，并内置“延迟”功能以减少加载“微调器”的闪烁。

```
Loadable({
  loader: () => import('./WillFailToLoad'), // oh no!
  loading: (props) => {
     if (props.error) {
       return <div>Error!</div>;
     } else {
       return <div>Loading...</div>;
     }
  }
});
```

 [## jamiebuilds/react-loadable

### react-loadable-:hourglass _ flow _ sand:一个高阶组件，用于加载带有承诺的组件。

github.com](https://github.com/jamiebuilds/react-loadable) 

5.React-universal-component 与所有其他组件也没有太大区别。唯一一个有真正动态的动态导入。

```
const Component = universal(props => import(`./${props.page}`))
```

> PS:这种做法可能副作用很大，要注意。

[](https://github.com/faceyspacey/react-universal-component) [## 面空间/反应通用组件

### 反应-通用-组件-🚀React 通用组件的最终答案:同步 SSR +代码拆分

github.com](https://github.com/faceyspacey/react-universal-component) 

它们都很好地完成了自己的工作，如果您想了解更多关于代码拆分本身的知识，请参考关于这些加载器的文章。

[](/faceyspacey/announcing-react-universal-component-2-0-babel-plugin-universal-import-5702d59ec1f4) [## React 通用组件 2.0 &巴别塔插件通用导入

### 怎么了，大西洋！

medium.com](/faceyspacey/announcing-react-universal-component-2-0-babel-plugin-universal-import-5702d59ec1f4) [](https://jamie.build/react-loadable.html) [## 介绍 React Loadable

### React“The Beach House(Mndsgn Remix)”中以组件为中心的代码拆分和加载

杰米。建立](https://jamie.build/react-loadable.html) [](/smooth-code/introducing-loadable-components-️-646dd3ab0aa6) [## 引入可加载组件✂️✨

### 一个解决 React 代码分割客户端和服务器端的库。

medium.com](/smooth-code/introducing-loadable-components-️-646dd3ab0aa6) 

> PS:其实那些文章大多是关于“进口”的东西。

如果您正在构建纯粹的前端 SPA，可以随意选择“加载器”，甚至使用您自己的加载器，因为这是一个很好的选择。

## 客户端代码分割和服务器端渲染

> 从表面上看，它们都有相似之处，尤其是在只与客户方打交道时。然而，我个人发现，当您尝试执行服务器端渲染时，每个解决方案之间的真正区别变得很明显。— [react-async-loader](https://github.com/ctrlplusb/react-async-component/issues/37) ，第 37 期

客户端代码拆分是**一件超级容易的事情**。“好的”客户端代码分解成更复杂的东西…但是你仍然可以在一两个小时内写完所有你需要的东西。结果可能会比上面所有的库更好。是的，为客户端编写“加载程序”很容易。

服务器端和代码拆分？啊？实际上没有人解决这个问题。

## 问题是

你正在**服务器**上渲染你的应用程序，并将**的整个** html 发送到**客户端**。接下来，客户端 ***重新水合*** 代码，用 React 表示替换死的 html，让它活起来。没有代码分割的简单**。**

问题#1 是——由于代码分割，客户端**没有**所有必需的**代码**来完全恢复服务器呈现的响应。一秒钟前还不错的页面被 spinners 取代了，几秒钟后又变得不错了。它将尽可能多地渲染，加载延迟的部分，然后重新渲染其余部分。事情就是这样运作的！

> 你搞砸了你在 SSR 上做的所有工作，让客户生气，甚至由于闪烁的内容和旋转的加载程序而导致癫痫发作。

> 规则#1 —首先装载所有“备用”零件。只有下一个复水内容，使客户看不到复水。

问题#2 是——服务器端 React 渲染是同步的。“代码分离”组件是“异步”同步的。如果您将在服务器上渲染它们，您将什么也不渲染，或者“加载组件”，但不是您必须渲染的 React 组件。它是异步的，并且将在您将结果发送到客户端之后准备好呈现**。答对了。第一价格！**

> 规则#2 —客户端异步。在服务器上同步。不要混。

那些问题是纠缠在一起的——你得知道你在渲染时“将要”加载什么，“是否”加载了它，还要让客户端知道如何重复。

也许 Lyft 的 [**通用异步组件**](https://github.com/lyft/universal-async-component) 就是 *cheatest* 那个。他们使用“string-replace-loader”来替换“import ”,让它在服务器上同步，然后从 webpack stats 中提取“used”脚本。所有内容 50 行代码。

[**React-lodable**](https://github.com/jamiebuilds/react-loadable)**做的几乎一样，但是使用 babel 插件来“传输”关于所用导入的信息，并且在异步导入之后提供同步需求。**

****通用部件**走了相似和不同的路。他们做着同样的事情，通过 babel-plugin-universal-import 传递“解密”的信息，但他们传递的是“导入”中的信息。**

**接下来上面列出的所有库将访问 webpack stat 信息，以确定用户在主上下文之前必须加载哪些额外的脚本。**

> **优点:他们完成了工作。**
> 
> **缺点:仅 webpack，将在主脚本之前同步导入额外的脚本。可能会受到 webpack 将来可能引入的一些优化的影响。**

****可加载和异步组件**非常不同——它们在服务器端精确地“**渲染反应树**，为最终结果提供“异步渲染”的东西。这不需要任何 webpack 或 babel 插件，但可能会影响页面渲染的性能。您必须在客户端执行客户端渲染，将所有内容重新渲染几次。**

**接下来，他们在前端做了几乎相同的事情。但是有一个问题——您一次只能导入一个“级别”的导入。如果你有一个异步页面，它加载异步小部件——他们将首先加载页面，只有在它之后，他们才能够**加载小部件，并得到代码来完成它。****

**PS: Loadable-components 有一个巴别塔插件来缓解(一点)这个问题。**

> **优点:可以使用任何捆扎机，即使没有额外的配置**
> 
> **缺点:慢速异步 SSR，慢速“受波浪影响”的前端渲染。**

****React-imported-component** 介于两者之间。它仍然总是异步的，但是通过简单地执行导入并在第一个用户生成页面之前记忆结果，使 SSR 呈现同步。只是一个简单的假设，承诺将在所有套接字之前执行，需要接受第一个客户端请求来呈现一些东西。**

**接下来，它将跟踪导入的使用情况，并尝试在客户端补液之前重放它们。**

> **优点:这是工作吗**
> 
> **缺点:不只是 webpack，不受 wave 影响，不慢，将来也不会坏。**

## **瓦特？**

**有一件事，我忘了说——这些加载器如何发出“同步”请求，并了解在再水合之前客户端必须加载什么。**

**他们使用 babel 来找到“import ”,并使用 import 里面的字符串作为“mark ”,并作为文件名指向`require`真正的文件。喜欢**

```
const LoadableComponent = Loadable({
  loader: () => import('./my-component'),
  loading: Loading,
});
```

**但是！如果您将导入从该功能中移出**

```
const loader = () => import('./my-component');
const LoadableComponent = Loadable({
  loader,
  loading: Loading,
});
```

**它会打碎东西。这是[已知的](https://github.com/jamiebuilds/react-loadable#how-do-i-avoid-repetition)问题。这仅适用于 SSR。**

**如果你想将加载器作为 UI 库或 UI 套件的一部分，我的意思是——标准站点范围的“加载”或“错误”、分析、监控——你将无法通过帮助器函数包装“加载器”或转换成 HOC。它将**正确停止 SSR** ，只要它将丢失使用过的组件的轨迹。**

**实际上，这不是一个“常见”的问题:**

*   **react-universal-component [用`universalImport`替换了](https://github.com/faceyspacey/babel-plugin-universal-import) `import`，被导入的同步和异步“版本”所迷惑。**
*   **react-imported-component 几乎做了同样的事情，用“findable”包装器代替。**
*   **universal-async-component 也用它们的包装器代替了`import`。**

**所以——他们都将能够理解正在发生的事情，并使用“间接”进口。“问题”只是 react-loadable 和 loadable-components 的“问题”。webpack v4 对`mjs`文件[的支持可能会破坏](https://github.com/smooth-code/loadable-components/issues/46)文件，因为目前的实现方式。**

**第二件重要的事情——如果你想加载模块“A”——web pack 可能会加载“B”、“C”和“D”。由于 CommonChunk、AsyncChunk、Webpack v4 AutoMagicChunking 或他们将来会发明的任何东西。理解(和预测)webpack stat 文件的所有魔力并不容易，这就是为什么由于“意外的”串联插件 universal-async-component 可能已经被破坏了。**

****反应导入组件**改变了这一点。一点点。这不是好的，也不是最终的解决方案，但可能现在对每个人都有效。**

1.  **它将您提供的“导入函数”转换为 string，并在其中注册导入。所以你可以从任何地方传递“import ”,但是你仍然不能使用复杂的函数，只要除了 loader 函数体之外的所有东西对导入的组件都是不可见的。
    与同样“攻击”导入的 react-universal-component 和 universal-async-component 的区别很简单——导入的组件不会每次都执行“导入功能”。在启动时只有一个，保持异步，允许你在里面放入任何逻辑(像一堆导入，等待，最后产生一个新的组件)。**
2.  **它在您的代码库中搜索导入，产生一个“查找”文件，包含您项目中的每一个`import`。接下来，它可以使用它通过“mark”(导入名称)导入一些东西，以减少“加载波”。**

**并且加载将是**异步的**。并且在主束之后装载。在“什么时候”加载东西——在“main”之前同步或者在“main”之后异步——有什么区别吗？当然，第二个版本会更快，快${numberOfExtraScripts}倍。**

> **优点:可以和任何捆绑工一起工作。**
> 
> **缺点:需要单独步骤从代码库中“提取”导入。还有巴别。**

**有很多装载机，所以尝试一下这个新的。实际上，它并不新鲜，因为它是在一年前作为 React-Hot-Loader 的“react-hot-component-loader”而诞生的。也可以随意尝试我上面列出的所有其他库。只是因为他们都是不同的，他们中的每一个都可能更适合你。他们都会变得更好并进化。**

> **PS:就像 react-imported-components 刚刚做的那样(这已经是第 4 版了)**

**![](img/0c02142b18bb98f18f73bc86432148a2.png)**

**而代码拆分本身呢？去做吧！不多。主要是在逻辑层，而不是组件层。**

**关键思想是——将“拆分”的代码与“数据”并行加载。或者您必须首先加载代码，这将开始加载数据。我们为客户体验而战，而不是代码分割本身。**

## **想要更多吗？**

**[](/@antonkorzunov/react-server-side-code-splitting-made-again-a61f8cbbd64b) [## React 服务器端代码拆分已完成..又

### React 代码拆分在一年前就成为了一件事。从那时起，我们一直在寻找新的方法来代码分割和推迟…

medium.com](/@antonkorzunov/react-server-side-code-splitting-made-again-a61f8cbbd64b)**