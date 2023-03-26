# JavaScript Decorators:函数参数的乐趣

> 原文：<https://medium.com/hackernoon/playing-around-with-your-standard-run-of-the-mill-javascript-decorator-example-28d0445307e1>

![](img/2f838e13dd820f3e31245422179d0c71.png)

自 ES2015 问世以来，JavaScript decorators 一直是[的一种语言特性，但在支持它的 JavaScript 引擎中，它们在很大程度上仍然是“实验性的”。它们是什么？如果你熟悉 Java，你会知道如何通过注释包装方法。要点是:装饰器将一个 JavaScript 类方法包装在另一个函数中，并由注释调用。](/google-developers/exploring-es7-decorators-76ecb65fb841)

我将为[示例代码](https://github.com/JeffML/JSDeco)使用 NodeJS，因此需要几个模块、插件和配置。

# 装置

1.  初始化 npm 项目
2.  安装 babel 命令行工具；我们将需要这个来传输修饰的方法
3.  安装传输所需的插件

```
npm init -ynpm install --save babel-clinpm install --save-dev babel-eslint babel-plugin-transform-decorators-legacy babel-polyfill babel-preset-env babel-register eslint eslint-plugin-node
```

4.使用以下设置向您的项目添加一个`.babelrc`文件:

```
{
  "presets": ["env"],
  "plugins": ["transform-decorators-legacy"]
}
```

**附加说明**:如果你使用 VSCode 作为编辑器，进入设置，打开实验装饰器，让任何警告消失。

5.要运行示例代码，请在 package.json 中添加以下运行目标:

```
"scripts": {
    "start": "babel-node yourdecoratedcode.js --require babel-polyfill"
  },
```

# 标准日志示例

然后，我将查看无处不在的`@log`示例，并在稍后对其进行一点调整。这是一个计划香草装饰，你可以找到许多变化。

评论应该说清楚发生了什么。`log`函数是 decorator，它包装了 decorator `@log`注释后面的类方法。为了使用它，类方法被注释为:

底部显示为注释的控制台输出来自前面清单的第 15 行。

# 使用参数名记录日志

经过一点挖掘，我能够[找到代码示例](https://stackoverflow.com/questions/1007981/how-to-get-function-parameter-names-values-dynamically)，这些代码示例说明了从方法签名中获取参数名的方法。现在让装饰器不仅转储参数值，还转储参数名:

# 添加合成参数

实际上，您并不局限于给定的参数，而是可以从装饰器内部推断参数值，并将它们传递给包装的方法。这可能看起来有点疯狂，但是我已经在我从事的一个项目中发现了这个特性的用途。

下面的 decorator 要通过参数名 来推断*传入的参数值。我将让包装函数转储它的参数值，而不是装饰器将字符串转储到控制台。*

这个装饰器将把调用函数时`MyClass.newStuff.isWombat and MyClass.newStuff.sugar`中的内容作为参数传递给原始函数:

输出是…

```
{ isWombat: true,
  sugar: [ 'in the morning', 'in the evening', 'at suppertime' ] }
{ isWombat: true,
  sugar: [ 'You are my candy, girl', 'and you keep me wanting you' ] }
```

好吧，那就结束了。代码库可以在这里找到[。](https://github.com/JeffML/JSDeco)