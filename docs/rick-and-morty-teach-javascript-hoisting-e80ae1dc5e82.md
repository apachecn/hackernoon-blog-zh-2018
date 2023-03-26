# Rick 和 Morty 教授 JavaScript 提升

> 原文：<https://medium.com/hackernoon/rick-and-morty-teach-javascript-hoisting-e80ae1dc5e82>

![](img/ab18fab63d76ed33ffdddf7113751f21.png)

*这是瑞克和莫蒂的粉丝虚构形象。本帖不是节目赞助的。*

*作者拉吉·阿因拉*

莫迪:我想回答一个问题。为什么会这样？这是什么绝地废话？

```
sayMyNameSayMyName('Morty'); // 'I said your name, Morty.' function sayMyNameSayMyName(name){
   return `I said your name,${name}.`;
}
```

里克:吊装。

莫迪:是啊，是啊，但到底是什么在提升？

Rick:我将尝试使用标记这种古怪现象的动名词来帮助定义。开始了。咳咳声明，不管是变量还是函数，都被提升到程序的顶部。

莫迪:好吧，这很容易理解。算是吧。但是等等……所以你是在告诉我用原力是举不起来的？

```
sayMyNameSayMyName('Morty'); // TypeError: undefined is not a function var sayMyNameSayMyName = function(name){
   return `I said your name,${name}.`;
}
```

里克:嗯，你知道，申报受到特殊对待。作业可以说是二等品。他们没有被提升的特权。

莫迪:但是为什么？

Rick:这是执行上下文。那是咎由自取。

莫迪:执行什么？

Rick:每一行代码都有一个上下文。有两个关键的上下文需要记住。你有全局和函数上下文。看起来是这样的:

```
/*Global--woohooo I'm freee*/two(); // 2function two(){
 /*Function
   ========
*/ return 2; }
```

莫迪:等等。上下文和范围是一样的，对吗？

瑞克:*叹息*

年轻的绝地武士，你还有很多要学的。不，范围是指访问。如果变量是在全局范围内声明的，它可以被函数或块访问。函数是独角兽，因为它们创建自己的作用域。但这与上下文不同。

你看，我们都可以说我们来自地球。这就是我们的全球背景。但是我们不能都说我们来自华盛顿 DC。这就是函数上下文。在 JavaScript 中，可以用`this`关键字确定当前的上下文。

莫迪:那么上下文和提升有什么关系呢？

里克:是的，所以……

首先，想象解释者是发现你信号的外星人，现在正在找你。外星人将从地球开始，我们的全球背景。地球上有七大洲。它可能从北美开始。

莫迪:为什么？

瑞克:它喜欢北美奇异的几何形状。

无论如何，它会创建一个范围链，包含你可能的国家，然后你的州，然后你的城市，然后你的街道。

现在，让我们试着看看 JavaScript 解释器的思想。当解释器读取代码时，它会自动进入全局上下文。

解释器的工作类似于外星人的搜索策略，首先寻找一个函数调用(信号)。它不会执行它，直到它可以创建上下文(查找您的信息)。

解释器完成它的任务要经历两个阶段:创建阶段和执行阶段。

# 1)请注意，我们正在进入创作阶段。

一个函数中可以有多个函数，所以解释器将初始化一个作用域链(国家、州、城市、街道)。

它将创建一个`variable object`来保存各种参数、参数和函数/变量声明。

然后它创建`this`来存储当前上下文。

这是过于简单化了。我们将进一步简化它，只关心解释器如何处理函数声明和变量声明。

功能:

当翻译器的鼻子碰到一个`function`关键词时，它会寻找这个名字。然后，它在 variables 对象中存储对该函数名的引用。

变量:

当解释器的鼻子碰到一个`var`、`let`或任何与变量相关的关键字时，它首先将变量名存储在变量对象中。然后自动用 undefined 初始化。

你能开始明白为什么把一个函数赋给一个变量并希望它被提升是行不通的吗？当我们调用`myNameIs(name)`时，解释器会找到我们的函数表达式，但它只会读入变量名并给它赋值 undefined。

```
sayMyNameSayMyName('Morty'); // 'I said your name, Morty.'
 myNameIs('Morty'); // undefined//
function sayMyNameSayMyName(name){
   return `I said your name,${name}.`;} var myNameIs = function(name){
   return `your name is,${name}.`;
}
```

下一阶段你会更明白这一点。

# 2)执行阶段

在执行阶段，值被分配给执行上下文中的变量。

过早调用`myNameis()`的问题是解释器在创建阶段就给`myNameIs()`赋值了 undefined。如果您在函数表达式之后调用了`myNameIs()`，解释器将有时间在执行阶段在全局上下文中分配`myNameIs()`的值。

调用`sayMyNameSayMyName()`之所以有效，是因为对声明函数的引用存储在创建阶段。当代码被执行时，我们能够毫无问题地运行它。

# 结论

莫迪:所以…提升是关于执行环境的？

里克:是的。

![](img/a80b3a6f9f4050f264853378519d2f65.png)