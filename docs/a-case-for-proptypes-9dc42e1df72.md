# PropTypes 的案例

> 原文：<https://medium.com/hackernoon/a-case-for-proptypes-9dc42e1df72>

![](img/a73cb853a275f6e13508e4819741954a.png)

A human typing on a typewriter, probably very strongly… ok I am done with the puns. Photo by rawpixel.com from Pexels

下面是数据类型的定义，通常称为类型:

*>数据类型是计算机编程中变量或对象所能容纳的数据类型的分类。*

我在学校遇到的大多数编程语言都是严格类型化的。也就是说，如果你将变量定义为 string 类型，它将是一个字符串，而不是别的。不像 Javascript，你不必决定一个变量应该是一个布尔值还是一个字符串，你可以在任何时候改变它，或者解释器会为你这样做(见闪电对话[瓦特](https://www.destroyallsoftware.com/talks/wat)

如果你想一想，当你创建一个变量并调用它的时候，你和自己签订了一个绑定契约，它将返回一个布尔值。所以如果你用一种严格类型的语言来创建这个变量，你会说布尔型是它的定义，如果它不是布尔型，那么你的程序会警告你，对你大喊大叫，或者干脆放弃。

让我们明确一点，我们会犯错误，所以类型帮助我们少犯错误。现在，你说——说重点，标题说 PropTypes！！！

这就是问题所在——我对 Flow 并不十分满意，在我看来，如果你要经历这个麻烦，就用 TypeScript 吧。但是——如果你在一个更大的项目中工作，或者在团队中工作，你可以在避免类型类错误/错误上投入最少的努力就是使用 PropTypes。

因为如果我想使用组件侧边栏，我们可以查看属性类型，即“logo:string . is required；链接:对象；页脚:instaceOf(页脚)`

至少对我来说，这足以让我使用组件，而不需要阅读它是如何真正工作的。这有点像那个组件的一个小维基。

我们在后端使用 Ruby，在我们的模型文件中有关于数据库表的注释。我认为这是我们团队的一份工作合同，让这些评论保持最新，我认为这也是正确的类型。有时它们会制造很多不必要的噪音，但是如果它们能让你远离一个大问题，对我来说这是值得的。

如果您想进一步了解我在本文中谈到的内容，这里有一些资源:

*   [道具类型 npm 包](https://www.npmjs.com/package/prop-types)
*   [流程](https://flow.org/)(JS 的静态类型检查器)
*   [打字稿](https://www.typescriptlang.org/)