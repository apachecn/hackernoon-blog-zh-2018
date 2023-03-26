# 使用 JavaScript 应用函数的技巧

> 原文：<https://medium.com/hackernoon/tricks-in-using-javascript-apply-function-148b1dcb9503>

![](img/dfb20b8fb0e03c7351899d74c372dbe3.png)

Photo by [Isis França](https://unsplash.com/photos/hsPFuudRg5I?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/machine?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

当你使用内置的 [JavaScript](https://hackernoon.com/tagged/javascript) 函数 Math.max 和 Math.min 时，你有没有觉得很麻烦？

![](img/c8e753c8da7d8579e38a7f1bec746798.png)

Image from google.com

我们已经知道这两个内置函数接受可变参数列表。例如，当使用 **Math.max** 获得最高值时，可以这样写

```
Math.max(5,2,1,3,4) // Outputs 5\. 
```

效果很好。如果我们要处理一个值数组，我们可以这样写我们的[代码](https://hackernoon.com/tagged/code)。

```
const list = [5,2,1,3,4];Math.max(list[0],list[1],list[2],list[3],list[4]);
```

**这个实现有什么问题？**

我猜你知道这有什么问题。您可能会注意到，这种实现并不灵活。如果数组变大了怎么办？我们在 Math.max 参数中添加更多的值。作为一名开发人员，我们不想硬编码这些值。除非我们知道要处理的数组的大小。

我们如何解决这个问题？将功能应用于救援。

所以我们将使用 Apply 函数在 **Math.max** 和 **Math.min** 中提供一个变量参数

**例如:**

```
function max(array) {
  return Math.max.apply(Math, array);
}function min(array) {
  return Math.min.apply(Math, array);
}max([5,2,1,3,4]); // Outputs 5
min([5,2,1,3,4]); // Outputs 1
```

这相当于我们之前所做的，但是现在我们使用一个内置的 JavaScript apply 函数。我们在参数中传递的值转换成逗号分隔的值，例如:**(【5，2，1，3，4】->5，2，1，3，4)** 。

现在我们的 **Math.max** 和 **Math.min** 现在很灵活，它现在可以处理大量的值，而不用担心它。

希望对^_^有帮助

在推特上关注我[【https://twitter.com/llaudevc】](https://twitter.com/llaudevc)