# 为什么应该避免使用 ES6 生成器在 React 中生成密钥

> 原文：<https://medium.com/hackernoon/using-es6-generators-to-generate-keys-in-react-b781c53d54b3>

几天前，我开始阅读 **ES6 发电机**(或发电机功能)，这是一个我想学习和理解很久的新功能。回想起来，这并不是一个很难的概念，但可能需要一些时间来适应发电机的工作方式。当我在阅读 Arfat Salman 的这篇精彩的文章时(我建议在继续阅读之前阅读)，我有了一个想法/问题:

> 我们可以使用 ES6 生成器为 React 中的元素集合生成键吗？

答案是**绝对是的**！但是我们应该吗？**可能不是**，正如评论中指出的那样，因为这与我最初的预期完全不同！然而，如果你对阅读我所尝试的和为什么失败感兴趣，继续前进！

![](img/8d94ac3dbfe3cef048817d790a831a8d.png)

Photo by [Artem Sapegin](https://unsplash.com/photos/ZMraoOybTLQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/iteration?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在我们深入研究代码之前，我想先介绍一些关键概念(双关语，非故意):

**生成器:**生成器是一个行为类似于迭代器的函数。它可以中途停止，然后从停止的地方继续，并保持内部状态。生成器的一个非常简单的例子是计数器，它看起来像这样:

```
function * Counter() {
  let n = 0;
  while (true) yield n++;
}
```

**键:**键被 React 用来惟一地标识集合中的元素(不严格地说是`<ul>`和`<ol>`列表和`<li>`元素)。据我所知，它们在虚拟 DOM diff 的计算中起着重要的作用，这反过来会影响你的应用程序的性能。你可以在[官方文档页面](https://reactjs.org/docs/lists-and-keys.html#keys)上阅读 React 中按键的工作原理。

## 生成密钥

基于以上内容，很明显可以使用生成器函数来动态地将键分配给集合中的元素。这个函数唯一要做的事情就是为每个元素生成一个上下文唯一的键。为了将这个例子推广到其他地方(例如，作为一个元素`id`生成器)，我将为每个集合的键加上一个惟一的标识符。以下是我对密钥生成器函数的理解:

```
function * Keymaker(listName, ListComponent) {
  let keyNum = 0;
  while (true) {
    let itemKey = `${listName}_${keyNum}`;
    yield props => <ListComponent key={itemKey} {...props} />;
    keyNum++;
  }
}
```

这里发生了几件事，需要解释一下:

*   由于生成器函数在调用之间保留一个状态，所以使用`keyNum`和集合的前缀(`listName`)来为每个元素生成一个惟一的键，然后将它传递给元素，最后，在移动到下一个元素之前递增值。
*   生成器函数充当组件的包装器(`ListComponent`)，组件由集合包装器本身传递。这使得生成器可以在不同的列表和集合之间重用。

此时，您可能想知道如何使用`Keymaker`生成器，所以这里有一个展示其用法的代码笔:

组件内部唯一神奇的事情是用前缀和组件实例化了`KeyMaker`,以呈现每个项目，随后是将每个数据对象映射到一个键控元素的后续调用。

## 重新生成密钥

这就是全部的内容——8 行代码，你可以永远忘记键控对象了……至少我是这么认为的！事实证明，每次渲染时都会再次调用生成器，为现有元素创建新的键。这正是我们不希望从中获得任何性能好处的情况。哎呦！

这个例子应该是一个即插即用的解决方案，但是它最终更像是一个应该避免的模式。但是，您可以稍微修改它，以其他有趣的方式利用生成器函数(生成动态的、上下文唯一的`id`值，或者根据想到的上下文创建不同的组件类型)，特别是如果您将结果分配给不会在每次应用程序渲染时调用生成器而变异的变量！

*感谢您阅读本文。如果你喜欢，记得给它一个或十个掌声！*