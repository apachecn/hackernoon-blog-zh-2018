# 使用 Alexa SDK 进行测试驱动开发

> 原文：<https://medium.com/hackernoon/test-driven-development-with-alexa-sdk-777f6b5e5486>

![](img/8519a3e19120e14eed76eaafc2c105cc.png)

Photo by [Andres Urena](https://unsplash.com/@andresurena?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## Alexa 技能构建和单元测试快速指南

# 什么是测试驱动开发？

根据[维基百科](https://en.wikipedia.org/wiki/Test-driven_development)的说法，它的意思是“需求被转化为非常具体的[测试用例](https://en.wikipedia.org/wiki/Test_case)，然后软件被改进以通过新的测试，只是。”基本上，在开始开发软件/特性本身之前，你必须首先编写你正在开发的软件/特性的测试。

这里还有几篇关于 TDD 的好处以及为什么你应该应用 TDD 的文章:
- [测试驱动开发](/mobile-quality/test-driven-development-d16fd216d45c)作者[Jan Olbrich](https://medium.com/u/b13c3ea3a356?source=post_page-----777f6b5e5486--------------------------------)
-[TDD 不是事半功倍吗？你为什么要在乎？](http://Isn’t TDD(Test Driven Development) twice the work? Why should you care?)作者[纳夫迪普·辛格](https://medium.com/u/ecfc4ed36c8e?source=post_page-----777f6b5e5486--------------------------------)

# 准备

在开始之前，您可能需要设置或安装以下几项:

*   亚马逊网络服务账户(免费的也可以)
*   [node.js](https://nodejs.org/en/) (虽然我个人更喜欢通过 [nvm](https://github.com/creationix/nvm) 来安装和维护)
*   您选择的文本编辑器

# 安装

1.  创建一个新的项目文件夹(`mkdir project-name`)
2.  导航到新文件夹(`cd project-name`)
3.  初始化 npm ( `npm init`)
4.  按照你的喜好完成所有的事情(如果你不知道它是什么，只要按下回车键，它就会保持默认)，除了`test command:`，输入`node test/testflow.js`。命令行应该类似于下面的代码片段。

```
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.See `npm help json` for definitive documentation on these fields
and exactly what they do.Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.Press ^C at any time to quit.
package name: (emptyproject) 
version: (1.0.0) 
description: 
entry point: (index.js) 
test command: node test/testflow.js
git repository: 
keywords: 
author: 
license: (ISC)
```

现在您已经为项目设置了 node.js。

# 单元测试

为维护为 Alexa 技能提供单元测试的 [testflow](https://github.com/robm26/testflow) 向 [robmccauley](https://medium.com/u/6fe2a060fc58?source=post_page-----777f6b5e5486--------------------------------) 大声疾呼。以下是以下命令将执行的操作:

*   将测试流存储库克隆到项目的“test”文件夹中。(如果这个项目本身也是 git 项目，我建议做`git submodule add`而不是`git clone`。)
*   创建一个名为“对话框”的文件夹
*   在“对话框”文件夹中创建名为“default.txt”的文件
*   编辑克隆的“testflow”存储库中的“SourceCodeFile”行，使其指向文件夹基本级别中的“index.js ”(您可以根据个人喜好随意编辑它)。

```
git clone [git@github.com](mailto:git@github.com):robm26/testflow.git test
mkdir dialogs
touch dialogs/default.txt
sed -i '9c const SourceCodeFile = "../index.js"' test/testflow.js
```

在“dialogs”文件夹的“default.txt”文件中，应该有您要测试的意图。测试流存储库中的缺省值包括以下内容，但是你应该根据你的技能添加一些定制的内容。

```
LaunchRequest
AMAZON.HelpIntent
AMAZON.StopIntent
```

如果你通过话语获取变量，你可以这样做:

```
DoSomething Input1=userInput1 Input2=userInput2
```

为了更深入地了解如何使用 testflow，我建议查看一下它的[教程页面](https://github.com/robm26/testflow/blob/master/tutorial/TUTORIAL.md)和作者本人的[博客帖子](https://developer.amazon.com/blogs/alexa/post/35eb8ae8-2cd8-4de7-86c5-97a1abc239b9/testflow-simulate-conversations-with-your-alexa-skill-code-to-ease-debugging)。

# 发展技能

说实话，这个话题我没有太多可说的。下面是一些示例代码，展示了一个示例技能的样子。(也记得做`npm install --save alexa-sdk`。)

我建议查看官方的 [Alexa GitHub 知识库](https://github.com/alexa/alexa-cookbook)，它提供了各种你可以在上面工作的示例技能。

# 部署

## Alexa 技能包

进入[页面](https://developer.amazon.com/alexa/console/)并选择“创建技能”。在那里你应该看到选项“添加意图”和“自定义插槽类型”。您应该从上面相应地添加适当的插槽类型和意图。完成后，从边栏中选择“端点”并选择“AWS Lambda ARN”作为技能的服务端点。制作技能 ID 的副本，因为您稍后会需要它。

## 自动气象站λ

选择“从头开始”创建 AWS Lambda 函数。之后，你应该有一个选项来“添加触发器”，你会选择“Alexa 技能包”。现在，在配置对话框中，您应该输入先前复制的技能 ID，然后选择“添加”。

之后，选择回到标有项目名称的框中。下面应该会出现一个“功能代码”对话框。虽然您肯定可以将您的代码复制并粘贴到在线文本编辑器中，但您已经安装并在代码中使用的包可能会默认包含在内。也有可能你有一个以上的技能 JavaScript 文件。

在这种情况下，您可以在“代码输入类型”下选择“上传. ZIP 文件”。在本地，将所有 JavaScript 文件连同`node_modules`文件夹一起压缩，然后上传结果 zip 文件。

# 提交

完成后，您可以返回 Alexa 技能工具包页面，选择“分配”选项卡并填写适当的信息，然后提交您的技能进行审核。希望一切顺利，你的技能在 Alexa 技能商店发布！

# 信用

*   [使用 Alexa 技能工具包培养技能](https://developer.amazon.com/docs/ask-overviews/build-skills-with-the-alexa-skills-kit.html)
*   [测试流程:用你的 Alexa 技能代码模拟对话，以简化调试](https://developer.amazon.com/blogs/alexa/post/35eb8ae8-2cd8-4de7-86c5-97a1abc239b9/testflow-simulate-conversations-with-your-alexa-skill-code-to-ease-debugging)