# 两年的 JavaScript 函数式编程:经验教训

> 原文：<https://medium.com/hackernoon/two-years-of-functional-programming-in-javascript-lessons-learned-1851667c726>

![](img/a11a580f4155fc38fa82ce8aaf50cbf9.png)

这篇文章不是关于学习 FP 原则或 JavaScript FP 库的。有许多关于这个主题的好文章。这篇文章是关于在一个项目中切换到函数式 JS 的冒险和后果。

当这个故事开始的时候，我已经是一个有 10+年经验的职业程序员了。C++，然后 C#，然后 Python。我可以编写任何程序。我对已经获得的模式和原则的信心扩展到了我看不到学习新东西的理由的程度。“我知道编程中 90%好的部分，”我想。

幸运的是，2016 年 5 月我们开始开发 [XOD 项目](https://xod.io/?utm_source=post&utm_campaign=fp_dev&utm_medium=medium)。XOD 是面向电子爱好者的可视化编程 IDE。为了保持随意性，我们必须有一个网络版的 IDE。Web？JavaScript！JavaScript 中成熟的 IDE？是的，我们不会用快速而肮脏的 jQuery 结束；我们需要更好的东西。

当时，一种用于重型前端开发的新技术正在出现:一种叫做 React 的东西及其伴随的 Flux/Redux 模式。在文档和文章中，它们与函数式编程的概念高度交织在一起。我开始探索 FP。

哇哦。就像我发现了另一个大陆。开发的澳大利亚，程序员倒着走，数据在路的另一边流动。当然，我听说过 Haskell、OCaml、LISP，但我曾经认为这些开发人员是一种边缘知识分子，他们为了编程而编程，而不是为了发布产品。我对自己专业水平的信心很快被侵蚀了。

XOD 是一种基因中带有功能性和反应性编程原则的产品。这在开发开始之前并不明显。我“发明”或者借鉴其他产品的很多东西，确实是 FP 基础。所以，星配星随，我们要用一些沉重的现代 FRP JavaScript 创建一个 FRP 编程环境。

预见到这些事件，努力是值得的。FP 给了这个项目一个非常坚实和灵活的框架。我不想再回头看“经典”编程了，在可预见的未来，我肯定会用函数式编程原则开发所有新项目。

# 打破壁垒

你会在 NPM 上找到大量的 [JavaScript 函数式编程库。其中最引人注目的是](https://npms.io/search?q=fp)[拉姆达](http://ramdajs.com/)。这是一种“下划线”或“下划线”，但是要记住 FP-first。Ramda 提供了几十个函数来处理数据和编写函数。

函数本身是好的，但是你需要一些 FP 对象来处理。另一个库 [Ramda Fantasy](https://github.com/ramda/ramda-fantasy) 会把它们给你。你可能还会注意到其他流行的 FP 库，比如[避难所](https://github.com/sanctuary-js/sanctuary)、[长笛](https://github.com/fluture-js/Fluture)、 [Daggy](https://github.com/fantasyland/daggy) 。当你开始有想法的时候，看看它们。不过，先从拉姆达开始，让你的大脑保持原位。

这是你遇到的第一个障碍。如果你查看任何一个 FP 库的文档，最好的情况下，你会得到很多 WTF 的问题。混乱的参数顺序、外来的术语、一些函数的不明确的实用价值会使你倾向于停止尝试并切换回惯用的编程。所以…

**第 1 点**。从与特定语言或库无关的文章开始学习 FP。您需要首先概述基本概念，了解好处，评估如何将您现有的代码转换以适应新的世界。

很多关于函数式编程的文章都是书呆子数学家混蛋写的。没有经过初步训练就阅读它们是危险的:类别和变体会让你大吃一惊，却什么也没换来。

幸运的是，有优秀的出版物可以开始。对我影响最大的读物是:

*   [功能编程指南](https://mostly-adequate.gitbooks.io/mostly-adequate-guide/)
*   [拉姆达思维](http://randycoulman.com/blog/categories/thinking-in-ramda/)
*   [Frisby 教授介绍可组合函数式 JavaScript](https://egghead.io/courses/professor-frisby-introduces-composable-functional-javascript)
*   [图片中的函子、应用程序和单子](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)

# 无意义的疯狂

当你开始探索 FP 的时候，你学到的第一个不寻常的概念是隐性编程，也称为无点风格或者(讽刺的)无意义编码。

基本思想是省略函数参数名，或者更准确地说，完全省略参数:

```
export const snapNodeSizeToSlots = R.compose(
  nodeSizeInSlotsToPixels,
  pointToSize,
  nodePositionInPixelsToSlots,
  offsetPoint({ x: WIDTH * 0.75, y: HEIGHT * 1.1 }),
  sizeToPoint
);
```

这是一个典型的函数定义，完全由其他函数组合而成。它没有声明输入参数，尽管调用需要它们。即使没有上下文，您也可以理解该函数的作用，就像一些传送带接收大小并产生一些像素坐标。要了解具体的细节，您需要深入研究组成该组合的函数。反过来，它们可能是其他函数的组合，等等。

这是一个非常强大的技术，直到你把它提升到荒谬的地步。当我们开始积极地使用 FP 技巧时，我们把把一切都转换成无点的问题当作一个我们必须一次又一次解决的难题:

```
// Instead of
const format = (actual, expected) => {
  const variants = expected.join(‘, ‘);
  return `Value ${actual} is not expected here. Possible variants are: ${variants}`;
}// you write
const format = R.converge(
  R.unapply(R.join(‘ ‘)),
  [
    R.always(“Value”),
    R.nthArg(0),
    R.always(“is not expected here. Possible variants are:”),
    R.compose(R.join(‘, ‘), R.nthArg(1))
 ]
);
```

啊，什么？你很酷，你已经解决了。在代码评审中与他人分享这个难题。

接下来，你学习单子和纯度。好了，我的功能从现在开始不能有任何副作用。它们不能引用`this`(没关系)，不能引用 time 和 random (o-o-ok)，不能引用除了给它们的参数之外的任何东西，甚至是全局字符串常量，甚至是数学 Pi。您从最外层的函数通过嵌套链向下到内部传递必要的参数、工厂和生成器，展开签名，然后学习读取器或状态单子。哎哟，你用零星的单子图和链感染了你所有的代码，这碗意大利面就做好了！

所以，组合子！多么有趣的动物。哦，Y-combinator 不仅是一个启动加速器，还是一个递归替换。下次我遇到可以通过递归或简单的“reduce”调用简单解决的问题时，让我们使用它。

**第二点**。函数式编程是关于λ演算、单子、态射和组合子的*而不是*。它是关于拥有许多小的定义良好的*可组合*函数，而没有全局状态、它们的参数和 IO 的突变。

换句话说，如果无要点风格在特定情况下有助于更好地沟通，就使用它。否则，不要。不要使用单子，因为你可以，当它们精确地解决问题时使用它们。对了，你知道安`Array`和`Promise`是单子吗？如果没有，这并不妨碍你正确应用它们。你应该在一定程度上训练你的直觉，当你明白什么是单子所需要的，或者更好的是，它根本不需要。它来自实践，不要过度使用新的东西，直到你能舒服地思考它。

单独来说，尽可能切换到没有副作用的小的可组合函数会给你带来最大的好处。从它开始。

# 要么抛出一个异常，要么返回 null

切换到 FP 风格的一个方面曾经让我很恼火。在经典的 JS 中，至少有两个选项来显示错误:

*   返回 null/undefined 而不是结果
*   抛出异常

当你获得 FP 时，你仍然有这些选择，并作为奖励获得`Either`和`Maybe`单子。我现在应该如何处理错误？我的 lib 的公共 API 应该是什么样子的？

从一个角度来看，`Maybe` / `Either`是一种更“合适”的方式，但对于图书馆消费者来说可能会比较陌生。空值和异常是惯例，但是你总是以控制台中的“T4”结束。长话短说…

**第三点**。不要害怕通过`Maybe` s 和`Either` s 的错误处理，这对夫妇是你在一元世界中最好的收获。

看看优秀的[面向铁路的编程](https://fsharpforfunandprofit.com/rop/)模式。在你的公共 API *和*中使用 Maybes，如果你担心你不被理解，提供带有后缀的瘦包装卫星，比如‘unsafe’、‘nullable’、‘exc ’,供命令式 JS 使用

# 清晰是一种药物

当您在用函数式编程原则开发的项目中合作时，您会很快注意到结果。现在做一个回顾需要低得多的认知负荷。如果你看函数，函数代码是你应该考虑的。你不再需要去想象改变这个领域会给那个组件带来什么样的后果。你不觉得在这里是浅抄，深抄，还是只是引用更合适。你只需要不要局限于你现在看到的十行代码。

然后，当你看到一个过时的代码时，它总是看起来很可疑。“嗯……为什么它改变了我的对象中的一个字段？为什么它存储在字段中，它会在随机时刻未经许可就变异我的对象吗？”经典代码开始看起来不对了。

**第四点**。你必须选择 FP 兼容的库和 FP 兼容的同事。后者尤为重要。如果摩擦面积大，团队一部分人争取 FP，另一部分人自由地破坏原则，最后 FP 会在项目中落败。

雇佣 FP JS 开发者更难，因为它设置了一个很高的最低水平。但是一旦你找到了，你就很有可能为你的产品找到最好的专家。在 XOD 中，我们都是 FP 专家，我很高兴我们一起工作。

# 利益伴随着受害者

函数式编程与主流有很大不同，以至于你正在使用的主流工具将停止工作。

Flow 和 Typescript 无法正常工作，因为它们很难表达所有 currying 和参数多态性。例如，虽然有针对 Ramda 的绑定，但它们经常给你错误的警报，当确实有错误时，消息非常含糊不清。

您可以找到一些在运行时执行类型检查的库。我们用[这样一个](https://github.com/xodio/hm-def)。唉，它们的伸缩性不好。性能损失通常高于函数执行本身的成本。因此，您只能通过显式启用它来进行检查，例如，对于单元测试。

如果你在深度作文中犯了一个错误，比如把输入输出类型搞得有点乱，看到栈迹就会哭。

```
Error: Can’t find prototype Patch of Node with Id “HJbQvOPL-” from Patch “@/main”
 at /home/nailxx/devel/xod/packages/xod-func-tools/dist/monads.js:88:9
 at /home/nailxx/devel/xod/node_modules/sanctuary-def/index.js:2491:23
 at /home/nailxx/devel/xod/node_modules/sanctuary-def/index.js:860:20
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_pipe.js:3:14
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_arity.js:7:53
 at src/project.js:887:5
 at /home/nailxx/devel/xod/node_modules/sanctuary-def/index.js:2491:23
 at /home/nailxx/devel/xod/node_modules/sanctuary-def/index.js:860:20
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_pipe.js:3:14
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_pipe.js:3:27
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_arity.js:5:45
 at _filter (/home/nailxx/devel/xod/node_modules/ramda/src/internal/_filter.js:7:9)
 at /home/nailxx/devel/xod/node_modules/ramda/src/filter.js:47:7
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_dispatchable.js:39:15
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_curry2.js:20:46
 at f1 (/home/nailxx/devel/xod/node_modules/ramda/src/internal/_curry1.js:17:17)
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_pipe.js:3:14
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_arity.js:5:45
 at src/typeDeduction.js:171:37
 at /home/nailxx/devel/xod/node_modules/sanctuary-def/index.js:2491:23
 at /home/nailxx/devel/xod/node_modules/sanctuary-def/index.js:864:20
 at src/project.js:618:33
 at _Right.chain (/home/nailxx/devel/xod/node_modules/ramda-fantasy/src/Either.js:67:10)
 at src/project.js:617:8
 at /home/nailxx/devel/xod/node_modules/sanctuary-def/index.js:2491:23
 at /home/nailxx/devel/xod/node_modules/sanctuary-def/index.js:860:20
 at _map (/home/nailxx/devel/xod/node_modules/ramda/src/internal/_map.js:6:19)
 at map (/home/nailxx/devel/xod/node_modules/ramda/src/map.js:57:14)
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_dispatchable.js:39:15
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_curry2.js:20:46
 at f1 (/home/nailxx/devel/xod/node_modules/ramda/src/internal/_curry1.js:17:17)
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_pipe.js:3:14
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_pipe.js:3:27
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_pipe.js:3:27
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_arity.js:5:45
 at validateProject (src/project.js:1031:3)
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_pipe.js:3:27
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_pipe.js:3:27
 at /home/nailxx/devel/xod/node_modules/ramda/src/internal/_arity.js:5:45
 at src/flatten.js:1021:5
 at /home/nailxx/devel/xod/node_modules/sanctuary-def/index.js:2491:23
 at /home/nailxx/devel/xod/node_modules/sanctuary-def/index.js:864:20
 at Context.<anonymous> (test/flatten.spec.js:1805:27)
```

在寻找问题根源时，大多数跟踪都是没有意义的。幸运的是，一旦 FP 代码第一次成功运行，你就可以确信它坚如磐石，不会给你带来任何惊喜。如果你在 JS 中做 FP，明显的结果是需要一个完整的单元测试套件。

代码覆盖率和断点也会中断。FP 代码更像 CSS 而不是 JS。看看 [XOD 来源](https://github.com/xodio/xod/tree/master/packages/xod-project/src/)。在 CSS 中放置一个断点并一步一步地执行它有意义吗？CSS 文件的覆盖范围是什么？当然，效果也不是 100%。在从声明式风格切换回命令式风格的地方，这些工具仍然可以工作；但是现在你的代码对于 devtools 来说是支离破碎的，体验也发生了巨大的变化。

**第五点**。一旦你碰了 FP，你就会不高兴和生气。当我从 Windows 切换到 Linux 时，我经历了同样的情绪，并且明白这两者都很糟糕，我没有办法撤销这些知识。从成熟的 IDE 到 Vim 的切换也是如此。霍普，你明白这个想法。

我们能两全其美吗？同时获得不疯狂的函数式编程和优秀的开发者体验？我也这么认为还有其他一些以 JS 为目标的语言从一开始就是功能性的: [Elm](http://elm-lang.org/) ， [PureScript](http://www.purescript.org/) ， [OCaml (BuckleScript)](https://bucklescript.github.io/) ， [ReasonML](https://reasonml.github.io/) 。

我最近在实践中尝试了 ReasonML，但那是另一回事了。如果你想听，就拍几下；)