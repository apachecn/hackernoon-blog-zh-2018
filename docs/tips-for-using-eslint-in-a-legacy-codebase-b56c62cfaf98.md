# 在遗留代码库中使用 ESLint 的技巧

> 原文：<https://medium.com/hackernoon/tips-for-using-eslint-in-a-legacy-codebase-b56c62cfaf98>

![](img/0658f0351e15c343659ae035a13de33d.png)

ESLint 是一个非常棒的工具，它可以帮助检测代码库中有问题的模式，并强制执行编码约定。对于任何试图维护高质量 [JavaScript](https://hackernoon.com/tagged/javascript) 代码库的团队来说，这是一个必备的工具。

然而，当您第一次向遗留代码库介绍 ESLint 时，很难不被它抛出的大量错误所淹没。

```
$ eslint .✖ 20983 problems (20983 errors, 0 warnings)19032 errors, 0 warnings potentially fixable with the ` — fix` option.
```

可以理解的是，旧代码在编写时没有考虑 ESLint 规则，但是当任何新代码中的错误被淹没在所有这些噪音中时，这就成了一个问题。在这篇博文中，我将介绍一些可以帮助你显著减少错误数量的技巧。

# 自动修复

ESLint 有一个非常有用的[实用程序](https://eslint.org/docs/user-guide/command-line-interface#--fix)，可以自动修复大多数错误。什么可以固定，什么不可以固定，取决于规则本身。这对于减少错误数量的影响取决于所使用的代码库和配置。如果您注意到在上面的 shell 代码片段中，出现了`20983`错误，`19032`可以被自动修复！让我们这样做:

```
$ eslint — fix .✖ 1700 problems (1700 errors, 0 warnings)
```

`1700`错误听起来比`20983`容易处理得多！

# 指定环境和全局

除了本地 js 对象(Date、parseInt 等)之外，每个 JS 环境(Browser、Nodejs 等)都有自己的一组主机/全局对象(window、process、setTimeout 等)。ESLint 没有假设一个环境，所以你可能会看到像`'window' is not defined`或`'setTimeout' is not defined`这样的错误。通过[指定环境](https://eslint.org/docs/user-guide/configuring#specifying-environments)，可以修复这些错误。

直到几年前，向 web 代码库添加依赖项的最常见方式是使用`<script>`标签，它们使用全局变量来公开它们的 API，比如用`$`表示 jQuery，`_`表示下划线等等。您可能会看到类似`'$' is not defined`或`'moment' is not defined`的错误。这些错误可以通过[指定全局](https://eslint.org/docs/user-guide/configuring#specifying-globals)来修复。

# 禁用规则

这应该是最后一招，在我们开始禁用左右两侧的规则之前，让我们来了解一下可以禁用规则的不同级别:

## 线路电平

这将禁用线路的规则

```
alert(‘foo’); *// eslint-disable-line no-alert, quotes, semi**// eslint-disable-next-line no-alert, quotes, semi*alert(‘foo’);
```

## 气泡水准仪

这将禁用多行规则

```
*/* eslint-disable no-alert, no-console */*alert(‘foo’);console.log(‘bar’);*/* eslint-enable no-alert, no-console */*
```

## 文件级别

这将禁用整个文件的规则

```
*/* eslint-disable no-alert */*alert(‘foo’);
```

## 目录级别

这将禁用目录的规则。这是通过在该目录中创建一个新的配置文件来完成的。一个项目中可以有多个 ESLint 配置文件。最接近的配置文件将覆盖外层目录中定义的配置文件。

当您只需要禁用特定目录中的一些规则，但仍然需要为代码库的其余部分启用这些规则时，此功能非常有用。例如，如果您启用了[无幻数](https://eslint.org/docs/rules/no-magic-numbers)规则，并且因为在断言中使用幻数而看到来自`tests/`目录的许多错误，您可以在`tests/`目录中创建的配置文件中禁用该规则。

点击了解有关该功能[的更多信息。](https://eslint.org/docs/user-guide/configuring#configuration-cascading-and-hierarchy)

## 项目级别

如果您在根配置文件中禁用规则，那么您可以禁用整个代码库的规则。通常这种方法在你使用一个[共享配置](https://eslint.org/docs/user-guide/configuring#using-a-shareable-configuration-package)比如 [eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb) 或者 [eslint-config-standard](https://www.npmjs.com/package/eslint-config-standard) 等并且想要添加你自己的覆盖时使用。

## 关于禁用规则的思考

在项目级别禁用规则是非常容易的，但是这样做意味着任何新编写的代码都不会根据这些规则进行检查。

在行级别禁用是最好的，因为只有那些行受到影响，但这可能非常繁琐，因为我们可能需要对数千行执行此操作。

根据出错文件的数量，最好在文件或目录级别禁用。这比在行级别禁用更容易，对新代码的影响也比在项目级别禁用小。

如果有一个[工具](https://hackernoon.com/tagged/tool)可以检查代码库并为受影响的代码行添加`eslint-disable-line`注释，那就太好了，但是我不知道这样的工具是否存在。

# 结论

使用上述步骤，可以修复或禁用代码库中的所有错误。任何新的错误都只是因为新编写的代码，只要我们一看到它们就可以修复。我们可以根据可用带宽分配一些时间来检查被禁用的错误，并逐步修复它们。

感谢阅读！如果你喜欢这个，你可能也会喜欢:[使用 ESLint 和更漂亮的工具加速你的代码审查](http://www.sheshbabu.com/posts/speed-up-your-code-reviews-using-eslint-and-prettier/)

最初发表于 sheshbabu.com 的