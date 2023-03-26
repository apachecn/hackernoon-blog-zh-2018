# 单元测试:一种简单化和语言无关的方法

> 原文：<https://medium.com/hackernoon/unit-testing-a-simplistic-and-language-agnostic-approach-918edff86d11>

![](img/f1a8f38d24b54b53c5b816a135e5525e.png)

“No problem thumbs up” via [Giphy](https://giphy.com/gifs/rad-sooziq-capture-5xrkJe3IJKSze)

如今，单元测试是软件工程师的必备技能。但是，他们中的许多人不知道这一点，并认为这是来自另一个世界的东西。

事实是:

> 单元测试只是为了确保其他代码行按预期工作而编写的一些代码行。

这些经过测试的代码行是您的应用程序的特性。就是这样，没有任何魔法。

我不想吓到任何人。所以，我不会使用库、标准、类和关键字。我将尝试用一种简单的方式来解释单元测试。使用纯代码，不导入任何类或库。这些例子是用 Python 写的。

# 动机

为什么我们必须做单元测试？单元测试是避免或警告您问题的最简单的方法。比如当一些代码改变破坏了应用程序的另一个功能时。

让我们看一个代码的例子:

正如我们所料，函数 **get_company_as_string** 工作正常。以下是一些价值和相应回报的示例:

```
In [1]: get_company_as_string('Samsung')
Out[1]: 'Name: Samsung | Founders: Lee Byung-chul.'In [2]: get_company_as_string('Apple Inc.')
Out[2]: 'Name: Apple Inc. | Founders: Steve Jobs, Steve Wozniak and Ronald Wayne.'In [3]: get_company_as_string('Microsoft')
Out[3]: 'Name: Microsoft | Founders: Bill Gates, Paul Allen.'In [4]: get_company_as_string('XPTO')In [5]:
```

但是，如果，我们需要创建一个函数，返回公司基金会创始人的年龄总和？所以，我们需要调整数据结构，使创始人名单保持他们的年龄。

之后，我们有下面的代码。这是前面代码的基础，对 founders 结构进行了修改，并增加了一个新功能:

并且，当我们运行我们的新函数时，我们得到这样的结果:

```
In [1]: get_sum_ages_of_company_founders('Samsung')
Out[1]: 26In [2]: get_sum_ages_of_company_founders('Apple Inc.')
Out[3]: 87In [3]: get_sum_ages_of_company_founders('Microsoft')
Out[3]: 41In [4]: get_sum_ages_of_company_founders('XPTO')In [5]:
```

因此，新特性的实现是成功的，我们可以部署新版本的应用程序了。是吗？不不不。

让我们执行函数 **get_company_as_string** 。请注意，我们没有更改该函数，并且我们已经验证了它工作正常。让我们看看它是否会继续发展:

```
In [6]: get_company_as_string('Apple Inc.')
Out[6]: "Name: Apple Inc. | Founders: [{'name': 'Steve Jobs', 'age': 21}, {'name': 'Steve Wozniak', 'age': 25}, {'name': 'Ronald Wayne', 'age': 41}]."
```

惊喜！结果是不一样的，函数不再像我们预期的那样工作。

是的，这是一个简单的例子，但是，想象一下，如果我们的应用程序有超过 100 或 1000 个函数。我们如何确保所有功能都按照我们的预期运行？或者在出现问题时收到警报？

你知道答案:单元测试！

# 断言/期望

这是单元测试的基础，也可能是最简单的东西。这是一个关于函数的小对话。我们告诉函数一些东西，并验证响应是否是它应该的。

例如，我们如何测试两个数相加的函数？给这个函数两个数字。将收益与这两个数字的和进行比较，这是我们已经知道的。如果函数是错误的，比较将会失败。

我们知道 2 + 2 是 4，-3+-2 是-5，-1+3 是 2。因此，如果我们向函数发送 2 和 2，我们期望接收 4。如果我们发送-3 和-2，我们期望接收-5，如果我们发送-1 和 3，我们期望接收 2。如果其中一个与我们预期的不同，那么这个函数就是错误的。很简单吧？

让我们看看单元测试在之前的重构中是如何帮助我们的。记住:我没有使用任何特定的库或类。我想简化解释并保持不可知论。

在旧版本的应用程序代码中，函数 **get_company_as_string** 工作正常。在这一点上，我们知道一些东西:

*   如果我们告诉*“苹果公司”*这个函数，我们期望得到*“姓名:苹果公司|创始人:史蒂夫·乔布斯、史蒂夫·沃兹尼亚克和罗纳德·韦恩。”，以及*
*   如果我们告诉*‘XPTO’*，我们期望收到 *None* 。

所以，用编码的方式:

*   **get_company_as_string('苹果公司')***应该等于* **'名称:苹果公司|创始人:史蒂夫·乔布斯、史蒂夫·沃兹尼亚克、罗纳德·韦恩。'** *和*
*   **get _ company _ as _ string(' XPTO ')**应该等于 **None** 。

代码如下:

Minimalist test example without classes and libraries

…以及测试的执行:

```
In [1]: 
   ...: if get_company_as_string('Apple Inc.') == 'Name: Apple Inc. | Founders: Steve Jobs, Steve Wozniak
   ...:  and Ronald Wayne.':
   ...:     print('Success on test #1.')
   ...: else:
   ...:     print('Error on test #1.')
   ...: 
   ...: if get_company_as_string('XPTO') is None:
   ...:     print('Success on test #2.')
   ...: else:
   ...:     print('Error on test #2.')
   ...:     
Success on test #1.
Success on test #2.
```

现在，我们有了确保函数正常运行的代码。因此，我们可以在实现新功能后测试功能。

```
In [1]: 
    ...: if get_company_as_string('Apple Inc.') == 'Name: Apple Inc. | Founders: Steve Jobs, Steve Woznia
    ...: k and Ronald Wayne.':
    ...:     print('Success on test #1.')
    ...: else:
    ...:     print('Error on test #1.')
    ...: 
    ...: if get_company_as_string('XPTO') is None:
    ...:     print('Success on test #2.')
    ...: else:
    ...:     print('Error on test #2.')
    ...:     
Error on test #1.
Success on test #2.
```

我们出错了。或者这会是一个成功吗？我们在实现新功能时所做的更改破坏了函数 **get_company_as_string。但是，我们的测试可以识别这个问题，现在，我们可以解决这个问题。**

因此，只需对功能稍作修改，我们就可以验证所有测试都已通过，并确保我们软件的质量:

Function get_company_as_string refactored to work after changes in data structure of companies founders.

这是一种简单的单元测试方法。在这篇文章中，我试图解释这种技能的巨大重要性，并帮助那些不知道如何开始软件测试的人。

通过单元测试，你可以提高软件的质量。防止对现有代码的维护产生不必要的副作用是非常有用的。单元测试可以防止很多问题，比如软件崩溃和调试时间的浪费。并且帮助你和你的队友维护一个高质量的软件。所有这一切，不费吹灰之力。

我打算在这篇文章中谈一点 TDD 和 Mock，但是，这篇文章已经太大了。所以，这将是另一篇文章。

如果你觉得这篇文章有帮助，给我一些掌声👏。