# ES6:理解 Javascript 中的析构赋值语法

> 原文：<https://medium.com/hackernoon/understanding-the-destructuring-assignment-syntax-in-javascript-c3bf8e1e908a>

ECMAscript 6 规范引入了新的语法，使得从数组和对象中获取值变得更加容易。我们称这种语法为“析构”。

![](img/4eda65ab018f6fc3a9a2567b867acca8.png)

Photo by [Dayne Topkin](https://unsplash.com/photos/Sk-C-om9Jz8?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/construction-site?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 数组的析构赋值语法

析构赋值[语法](https://hackernoon.com/tagged/syntex)允许你在使用类似数组或对象的语法时，轻松地从数组和对象中获取值。这个语法非常短，非常容易阅读。

如果不使用析构语法，从数组中获取多个值会非常麻烦。你会这样做:

使用析构赋值语法，可以这样写:

很漂亮，对吧？让我解释发生了什么事。基本上，这种语法会自动将相应索引的值赋给一个变量。您可以决定是否将值赋给现有的变量，或者您可以简单地通过在赋值前添加`var`、`let`或`const`来声明新的变量。

## 有几个问题:

您可以跳过项目:

此语法也适用于嵌套数组:

您可以将析构语法与 rest 语法结合使用:

# 对象的析构赋值语法

就像数组一样，对象也有类似的析构语法。虽然从对象中获取值通常不那么麻烦，但是析构语法肯定会有所帮助。

用对象进行析构有助于将属性值赋给变量。它的工作方式与数组类似。指定要绑定的属性，后跟要将值绑定到的变量。这可能看起来像这样:

如果您不介意变量名和键名相同，您甚至可以通过使用简写语法来进一步优化。上面的话可以这样写:

就像数组一样，您也可以对嵌套对象使用以下语法:

# 析构语法用例

很高兴您现在知道了可以用析构语法做什么。但是除了用一种更短的方式来写东西之外，还有一些其他的用例可以使用这种语法。

在函数中接受参数时，析构语法会有所帮助。与其以特定的顺序请求特定的参数，不如只传递一个对象，然后使用析构语法只公开所需的属性，这样会容易得多:

使用析构语法的另一个好处是当您使用返回多个值的函数时:

就像数组一样，您也可以对对象这样做:

最后但同样重要的是，当从 CommonJS 模块导入时，析构语法非常有用。很可能模块导出的内容超过了您的需要，使用这种语法可以避免混淆名称空间。

# 结论

重构语法有助于编写更简洁的代码。将它与其他美容产品混合在一起会使它更加强大。它现在几乎可以在所有最新的浏览器中使用，不需要任何聚合填充或传输。如果你还没有使用它，我强烈推荐你使用它。

# 在阿姆斯特丹找工作？

我在 **Sytac** 工作，是一名高级前端开发人员，我们正在寻找专注于 Angular、React、Java 或 Scala 的中级/高级开发人员。Sytac 是荷兰一家雄心勃勃的咨询公司，为银行、航空、政府和零售部门的许多知名公司工作。你可以想到荷兰国际集团、荷兰皇家航空公司、德勤公司、阿霍尔德·德尔海兹公司、ABN·AMRO 公司、弗洛拉·荷兰公司等等。

从个人的观点来看，Sytac 确实与他们的客户组合不同，但也与他们如何照顾他们的员工不同。他们真的很关心员工的福利。除了丰厚的薪水(50K-75k)，你会在与咨询经理的定期会议中注意到这一点，还会从他们组织的活动数量以及他们为让所有员工满意而提供的所有其他津贴中注意到这一点。

如果你认为自己具备与最优秀的人共事的条件，请发邮件至 [luuk.gruijs@sytac.io](mailto:luuk.gruijs@sytac.io) 给我，我很乐意告诉你更多。