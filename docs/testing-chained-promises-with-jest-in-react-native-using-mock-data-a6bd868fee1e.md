# 使用模拟数据在 react-native 中用 jest 测试连锁承诺

> 原文：<https://medium.com/hackernoon/testing-chained-promises-with-jest-in-react-native-using-mock-data-a6bd868fee1e>

我正在为我的 react 本地应用程序开发登录屏幕，并把 google-sign-in 作为选项。该逻辑包含嵌套承诺，并且是使用链式承诺构建的。

开发代码很容易，但是当我开始为这个逻辑编写单元测试用例时，我真的大吃一惊。我找不到任何好的文档来处理这种情况，尽管在这个过程中很少有[例子](https://stackoverflow.com/questions/36400623/test-promise-chain-with-jest)有所帮助

jest 团队为异步代码提供的[文档](https://jestjs.io/docs/en/tutorial-async)适用于单个承诺。代码结构见[https://github.com/facebook/jest/tree/master/examples/async](https://github.com/facebook/jest/tree/master/examples/async)。

在阅读了文档之后，我认为缺少了一些东西

1.  它说 `__mocks__`文件夹应该和要测试的文件在同一个文件夹中。
2.  它要求将待测试的函数相对导入到测试文件夹中。

这对于相对导入来说很好，但是我的代码是通过修改。`babelrc`文件。

以下是我的文件夹结构:-

我喜欢将测试分开，所以我创建了一个单独的`__tests__` 文件夹。

现在，如果我按照上面提到的 jest 的要求，我必须在一个特定的屏幕中创建一个名为`__mocks__`的文件夹。

为了使这个工作，现在我必须从`__mocks__`相对导入到`__tests__/tests/screens/Auth`。我试过了，效果很好。但是由于我的整个项目都使用绝对导入，所以我不想专门为此添加相对导入。

我做了一些小的调整，以使这个工作使用绝对进口。

所以让我们开始吧。

让我们先看看`Auth.container.js`代码，看看业务逻辑是如何实现的。

在按钮 onPress 上，我调用该组件中的函数`onPress`，该函数执行以下操作:-

1.  使用[react-native-google-sign-in](https://github.com/joonhocho/react-native-google-sign-in)向 Google 认证并返回`email`、`idToken`和`status`。

如果你愿意，我已经在这个文件中添加了`TEST_authorizeGoogle`,我会在测试代码中谈到它。对于现在刚刚结账的`authorizeUser`功能。

2.用生成的`email`和`idToken`调用我的后端，为用户生成一个新的`accessToken`，并让他(或她)登录。

现在再次忽略`TEST_setLoginGoogle`。

现在让我们看看实际的测试案例。

首先，我从本地创建的`mocks`文件夹中导入了模拟。

接下来，我使用`TEST_setAuthorizeGoogle`覆盖函数`authorizeGoogle`，使用`TEST_setLoginGoogle`登录 Google。这样做是因为在 ES6 中，命名导出总是常量。查看[此处](https://stackoverflow.com/a/48173881/2485624)了解更多信息。

首先，我在上面代码的第 54 行测试第二个承诺的调用是否带有第一个承诺的结果。

其次，我在第 55 行测试最终数据是否与服务器返回的模拟数据相同。

通过这种方式，我能够分离测试文件夹，并使用绝对导入来测试我的组件的连锁承诺。

欢迎反馈！！干杯！！