# 减少、再利用、反应

> 原文：<https://medium.com/hackernoon/reduce-reuse-react-1cd487f16fe0>

## 初学者指南，将这一小块可重用的代码变成它自己的 npm 包。

*注:这是一个最初出现在* [*JavaScript 一月*](https://www.javascriptjanuary.com/) *的交叉帖子。*

![](img/002aae8b2640ccb6a5f544da41f7a317.png)

Photo by [Gary Chan](https://unsplash.com/photos/YzSZN3qvHeo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 背景

不久前，在编写一个命中 REST 端点的 React [应用程序](https://hackernoon.com/tagged/application)时，我编写了一个小工具来构建一个正确编码的 Uri 路径。当时我也没多想。它完成了工作，我继续前进。

几个月后，在进行另一个项目时，我需要类似的东西。我开始找到它并复制代码，但我不记得我最初写在哪里了。

在我的本地文件系统和 GitHub 上做了大量的挖掘之后，我终于找到了它。我发誓这种事再也不会发生了。

# 减少

正如我提到的，该实用程序构建了一个 Uri 路径。例如，给定一个资源`“user”`和一个用户标识`“123”`，它可能返回一个特定用户资源的路径，如下所示:`"/users/123"`。

漫不经心的读者可能会说，“为什么不能把`userId`连接到字符串`"/users/"`的末尾，然后就到此为止呢？”可能是这样的。

```
const resource = 'users';
const userId = '123';
const path = `/${resource}/${userId}`;
```

如果您可以在您母亲的墓前发誓，资源的名称或 userId 不包含任何需要 Uri 编码的字符，那就太好了——不仅是现在，而是永远。

如果`userId`值不仅仅是数字，而是句柄，比如`"bob"`、`"jim"`等，会怎么样？而如果有`"this&that"`之类的手柄呢？这将产生一个无效的 URL 路径`"/users/this&that"`。

或者，如果用户输入他们的`userId`为`"?format=xml"`会怎么样？`path`将以`"/users/?format=xml"`结束，这可能以 XML 格式返回所有用户——完全不是我们所期望的。

我们可以像这样解决前面例子中的 Uri 编码问题。

```
const resource = encodeURIComponent('users');
const userId = encodeURIComponent('123');
const path = `/${resource}/${userId}`;
```

完美！它工作了。但是……每次你想生成一条路径的时候，都要付出很多努力。

这就是为什么我想出了一个实用程序，你可以隐藏你的主要逻辑，并可以重复使用。它结合了非常有用但经常被误解的`Array.reduce`方法，该方法采用一组值，并将它们缩减为单个值。在我们的例子中，我们接受一个字符串和值的数组，并将它们简化为一个字符串。

```
const buildUriPath = (strings, ...values) => (
  strings.reduce((partialUri, string, i) => (
    `${partialUri}${encodeURIComponent(values[i - 1])}${string}`
  ))
);
```

按照这里所示方式使用它，编码每个变量的所有工作都被抽象掉了。

```
const resource = 'users';
const userId = '123';
const path = buildUriPath`/${resource}/${userId}`;
```

简单来说，它需要两个数组:`strings`(字符串常量`['/', '/', '']`)和`values`(需要编码的模板化字符串值`['users', '123']`)。它构造并返回一个字符串`"/users/123"`。

好吧，那不完全是*准确。传递给它的是一个字符串数组，但我所说的值数组，实际上是可变数量的参数。我使用 ES6“rest”语法将参数转换成一个值数组。*

当我们减少字符串时，字符串数组的第 0 个元素作为 partialUri 传递，迭代从第一个元素开始，依此类推。

对`buildUriPath`的调用后面没有括号？这是什么巫术？这被称为标记模板文字。它是我们上面使用的 ES6 模板文字的特殊形式，但是它允许我们用函数来处理它。

记住我们不直接调用我们的代码。在将模板解析成单独的元素后，JavaScript 的模板文字函数调用它。

[样式组件](https://www.styled-components.com/)的粉丝已经熟悉了这种语法。这是对 ES6 规范的一个非常强大的补充。

# 再用

所以现在我有了一个超级棒的、方便的`buildUriPath`函数，让我们与世界分享它，这样每个人都可以使用它。但问题是...即使没有其他人想使用它，我仍然希望自己能够轻松地反复使用它。我们可以通过制作一个 npm 包来做到这一点。我很喜欢这个名字`build-uri-path`，所以我希望它是可用的...的确如此。

因为我们在 ES6 中编写了我们的实用程序，但是这个世界仍然依赖于 ES5，我们将使用 Babel 来为我们传输它。

下面描述的所有内容的完整来源可以在我的 [GitHub repo](https://github.com/donavon/build-uri-path) 中找到。

# 开源代码库

首先创建一个 GitHub 帐户(如果你还没有)，然后创建一个空白回购。将其克隆到您的本地驱动器，并更改到目录中。执行`npm init`创建一个骨架`package.json`。务必将入口点设置为`lib/index.js`。这将把下面的内容添加到您的`package.json`文件中，它指示您的包的消费者最初执行什么文件。

```
"main": "lib/index.js",
```

# 安装巴别塔

我们将在 ES6 中编写代码，但现实是今天的浏览器并不完全支持我们编写的语法。幸运的是，有一个叫做 Babel 的工具可以让我们将 ES6 源代码转换成 ES5 可发布代码。

要安装 Babel，请在您选择的终端中执行以下命令。

```
$ npm install -D babel-cli babel-preset-env
```

你还需要创建一个`.babelrc`文件。这告诉巴贝尔如何变身。

```
{
  "presets": ["env"]
}
```

# 获取编码！

打开你最喜欢的编辑器，用一个`index.js`文件创建一个`src`文件夹。这将是我们放置将被导出为`default`的`buildUriPath`函数的地方。

整个源代码看起来像这样。

```
const buildUriPath = (strings, ...values) => (
  strings.reduce((partialUri, string, i) => (
    `${partialUri}${encodeURIComponent(values[i - 1])}${string}`
  ))
);export default buildUriPath;
```

# 建设

运行`npm run build`，您应该会看到 Babel 构建了一个`lib/index.js`文件。如果你想看 ES5 编译的代码，可以看看这个文件。注意，它比它的 ES6 源代码要详细得多。这是 ES6 带来的语法优势。

对于较大的包，考虑使用像 [rollup.js](https://rollupjs.org/) 这样的打包器，但是对于我们的小包，发布 ES5 代码应该没问题。

# 测试

作为一名开发人员，您必须通过编写大量测试向世界和您自己证明，您的代码可以在各种输入值下工作！它们对于[回归](https://en.wikipedia.org/wiki/Software_regression)也至关重要。

测试本身就是一篇完整的文章。你可以在 1 月 1 日的[JavaScript 1 月](https://www.javascriptjanuary.com/)文章"[中阅读更多关于](https://www.javascriptjanuary.com/blog/but-really-what-is-a-javascript-test) [JavaScript](https://hackernoon.com/tagged/javascript) 测试的内容，但是，什么是 JavaScript 测试呢？

然而，您可以通过运行`npm test`看到我们的包通过了测试。

# 连续累计

你真的应该考虑建立持续集成(CI)，比如 [CircleCI](https://circleci.com/) 或 [Travis](https://www.travis-ci.org/) 。测试将在每次推送到您的代码仓库时自动运行。当您有多个源代码贡献者时，这将有助于确保代码质量。

同样，CI 超出了本文的范围，但是您可以在这里找到关于[设置 Travis CI 的相当全面的解释](https://github.com/dwyl/learn-travis)。

# 林挺

设置一个 linter，比如 [ESLint](https://eslint.org/) 也可以通过在编译和测试之前捕捉问题来帮助你减少错误。你可以考虑使用 Airbnb 的 ESLint 配置。

# 剧本

我们需要在我们的`package.json`文件中添加一些脚本来实现自动化。添加一个`build`命令，允许我们手动传输我们的代码。添加一个`prepublishOnly`钩子，这样我们就可以确保测试通过，并且每当我们执行`npm publish`时，我们的代码都会被自动编译。

我们的脚本部分如下所示。

```
"scripts": {
  "prepublishOnly": "npm test && npm run build",
  "build": "babel src --out-dir lib --ignore '**/*.test.js'",
  "test": "eslint src && jest"
},
```

# 类型脚本支持

如果你真的想留下深刻印象，你可以有选择地添加一个 TypeScript 类型定义文件(即所谓的`d.ts`文件)，这样 TypeScript 用户就可以拥有一流的类型支持。

在我们的例子中，只需创建文件`types/build-uri-path.d.ts`。该文件将如下所示。

```
declare module "build-uri-path" {
  function buildEncodedUri(
    strings: string[],
    ...values: string[]
   ): string;
   export default buildEncodedUri;
}
```

您还需要通过添加下面一行来引用您的`package.json`文件中的文件。

```
"types": "types/build-uri-path.d.ts",
```

详见[打字稿文件](https://www.typescriptlang.org/docs/handbook/declaration-files/templates/module-d-ts.html)。

# 最后……发布到 npm

剩下要做的就是发布到 npm。但首先您必须是 npm 注册中心的用户。如果您没有帐户，请使用`npm adduser`创建一个。一旦创建了 npm 用户，只需输入`npm publish`。脚本将运行——测试和构建——如果成功，你将发布你的第一个 npm 包，我的朋友！

# 反应

现在任何人都可以使用你闪亮的新包装。所以为什么我们不做第一个呢？我将使用 CodeSandbox 安装`build-uri-path`，并基于使用它的组件文件夹模式编写一个简单的 React 应用程序。

应用程序点击一个 REST 端点来获取一些数据。我正在使用[星球大战 API](https://swapi.co/) 作为我的后端。前端反应应用程序允许用户键入一个资源和一个 ID。由于这些信息直接来自不可信的来源(即用户)，因此需要对它们进行编码。好在有一个 npm 包可以做到这一点！

你可以看到完整的应用程序，带源代码，运行在 [CodeSandbox.io](https://codesandbox.io/s/7kw80l0owj) 上。

You can see the complete app, with source code, running on [CodeSandbox.io](https://codesandbox.io/s/7kw80l0owj).

文件`loadData.js`导入我们的`buildUriPath`函数，并使用它来构建发送到后端 REST API 的路径。

```
import buildUriPath from 'build-uri-path';
...
const path = buildUriPath`/${resource}/${id}`;
```

# 结论

您不必编写下一个 React 来为开源社区贡献一个包。如果你在工作中看到价值，那就把它拿出来。即使你是唯一受益者，这也是足够的理由。

但你可能会感到惊讶。其他人可能会发现它很有用，而您的微薄的小软件包可能是下一个开源轰动！

*感谢*[*Paul Bouzakis*](https://twitter.com/paulbouzakis)*帮助打字稿部分，特别感谢*[*Renato se lenica*](https://twitter.com/devdeprecating)*修复我的错误。；)*

我在美国运通公司工作，为美国运通工程博客写稿。在[*American express . io*](http://americanexpress.io/)*查看我的其他作品和我才华横溢的同事的作品。也可以* [*在 Twitter 上关注我*](https://twitter.com/donavon) *。*