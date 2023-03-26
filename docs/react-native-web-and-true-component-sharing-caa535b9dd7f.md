# react-native-web 和真正的组件共享

> 原文：<https://medium.com/hackernoon/react-native-web-and-true-component-sharing-caa535b9dd7f>

巫术？

S o，先说一下这个。关于 React Web 和 React Native 之间的组件共享已经讨论了一段时间。有可能编写一个组件库并在两个项目之间共享它们吗？

web 在 Div 和 Span 中运行，在视图和文本中本地反应等等..对于那些不熟悉的人来说，这是问题的根源。

我最近意识到 [react-native-web](https://github.com/necolas/react-native-web) 并且一直很想了解它，我终于开始了解它了。React-native-web 是由尼古拉斯·加拉格尔写的。

[](https://github.com/necolas/react-native-web) [## necolas/react-native-web

### React-Native-Web-React Native for Web

github.com](https://github.com/necolas/react-native-web) 

# 下面是我对功能的初步探索，以及创建一个工作演示的步骤。我计划在以后的文章中深入探讨这些特性和评论。

链接到我的 react-native-web 工作演示 repo[这里](https://github.com/btg5679/reactNativeWeb)，更多在最后。

我做的第一件事是从 [create-react-app](https://github.com/facebookincubator/create-react-app) 引导。然后你需要`npm install react-native-web`，然后你就准备滚了。

现在，继续用下面的要点内容替换您的`index.js`文件。

你会马上注意到一些东西，react-native-web 的尝试是将 react 的平台无关功能原生到 web 上。您正在编写 React 本机代码，在后台它会被转换为 web 友好组件。

给你一个主意，这是我的`App.js`文件:

看起来像是支持服务器端渲染，平台特定的指令(想想在 web OS 上让这个按钮 20em 高，否则 10)。看起来也支持通过 [Jest](https://facebook.github.io/jest/) 进行测试。

这是我的 github 项目，你可以从中引导，只有`nmp install`和`npm start`。

[](https://github.com/btg5679/reactNativeWeb) [## btg5679/reactNativeWeb

### 在 GitHub 上创建一个帐户，为 reactNativeWeb 开发做出贡献。

github.com](https://github.com/btg5679/reactNativeWeb) 

我将深入研究并发布一篇关于特性的评论，请继续关注第 2 部分。让我知道你的经历和一般想法。

# 如果你喜欢这个故事，请在 twitter 上关注我，并继续关注它！