# 不要使用节点(后端)，除非你需要异步代码

> 原文：<https://medium.com/hackernoon/dont-use-node-as-a-server-unless-you-need-asynchronous-code-60f41729dbc1>

## 满足您需求的最简单的技术将节省您的时间和精力，从而更好地解决问题

[Node.js](https://nodejs.org) 是一项令人惊叹的[技术](https://hackernoon.com/tagged/technology)。它的主要创新是基于事件循环的执行模型，该模型有助于编写以非阻塞操作运行的程序。[这里](https://www.infoworld.com/article/3210589/node-js/what-is-nodejs-javascript-runtime-explained.html)是 infoworld 上的一篇解释这个想法的文章。

当你在 nodejs 中编写服务器时，一切都是基于回调的，事件循环在单个 [javascript](https://hackernoon.com/tagged/javascript) 线程上协调执行。

Nodejs 结合了两个重要的编程思想:[事件驱动编程](https://www.coursera.org/learn/interactive-python-1/lecture/3HN9h/event-driven-programming)和[并发性](https://www.ics.uci.edu/~rickl/courses/ics-h197/2014-fq-h197/talk-Wu-Concurrency-is-NOT-parallelism.pdf)，在某种程度上，程序员可以从概念上进行处理。如果你能理解下面的代码，你就可以开始用 nodejs 写程序了:

```
<html>
<head>
<script>
document.write("Round and round the cobbler's bench,<br>");
document.write("The monkey chased the weasel.<br>");
document.write("The monkey thought 'twas all for fun<br>");
setTimeout(surprise, 4000);
function surprise(){
    alert("POP!");
    document.write(". . . goes the weasel.");
}
</script>
</head>
</html>
```

Rich satire on imitation and IP

编程就是建立以前的想法，并以一种新的和创造性的方式执行它们。这正是 nodejs 所做的。它采用了一种为网络浏览器设计的混乱和不一致的语言，并使用谷歌的 v8 引擎，使其成为当时最强大和性能最好的服务器技术之一。

在过去的十年里，javascript 已经从勉强突破十大编程语言的[上升到被许多人认为是 2018 年要学习的头号编程语言:](https://spring.io/blog/2009/02/23/programming-language-popularity)

[](https://medium.freecodecamp.org/best-programming-languages-to-learn-in-2018-ultimate-guide-bfc93e615b35) [## 以下是 2018 年最值得学习的编程语言

### 这是任何想要在 2018 年选择正确的编程语言职业道路的人的权威指南。

medium.freecodecamp.org](https://medium.freecodecamp.org/best-programming-languages-to-learn-in-2018-ultimate-guide-bfc93e615b35) 

我 100%同意学习 javascript 是个好主意。

但是仅仅因为你应该学习 javascript，并不意味着你应该为你的 web 服务器使用 nodejs。使用 nodejs 或 javascript 还有其他原因。Node.js 是一个巨大的开源工具生态系统的中心，javascript 是一种拥抱简单和灵活性的语言，尽管它有时会变得混乱。

当在 nodejs 中对服务器编程时，回调增加了复杂性，并且可能是混乱的来源，即使您很小心并且很好地设计了您的程序。如果异步非阻塞设计是系统需求的关键部分，我只推荐使用 nodejs 作为服务器。

有哪些更简单的 web 服务器编程方法？

*   PHP 和 MySQL
*   使用 Python 或 Perl 的 CGI
*   Bottle.py / SimpleHTTPServer
*   魔力/樱桃/辛纳特拉
*   Django / Ruby on Rails
*   GoLang(仍然需要理解并发性，但是只有在需要的时候才使用它)

从长远来看，我认为这些工具都是比使用 nodejs 更容易编写和维护 web 服务器的方法。这是我的观点，很多人可能不同意我的观点，但是熟悉你的选择和相关的权衡是值得的。值得注意的是，golang 是唯一上榜的强类型语言。GoLang 有自己具有挑战性的概念，但在最初的学习曲线之后，我认为它允许你在想编程的时候简单地编程，而 nodejs 的回调模型改变了你必须思考程序的方式。

在我看来，学习一门新语言实际上比一下子掌握一种新的范式、生态系统和框架更容易。nodejs 服务器生态系统中的一切都必须异步编写，因为线程模型和良好的设计使得将同步和异步操作结合起来不是一个好主意。Async/await 可以提高清晰度，但在表面之下，您仍然需要理解异步发生了什么，*就好像它是用回调编写的一样。*否则，你可能会被泄漏抽象的[法则](https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/)所伤害。异步编程给编程过程的每一步都增加了复杂性和概念上的开销，所以在选择使用它时要小心。

如果您决定用 nodejs 编写您的应用程序，它可能会比其他一些工具花费更多的工作，但有时这是值得的。

我只能证明一个例子，我决定使用 nodejs 只是因为它是“服务器 javascript”。我决定为在线回合制浏览器游戏编写一个[框架，对我来说很重要的一点是，开发者可以使用相同的语言来定义服务器逻辑，就像在浏览器中编写游戏一样。“服务器 javascript”通常应该是您选择使用 nodejs 的最后一个原因。](https://github.com/derekmc/turnbased-server)

虽然 nodejs 的概念是可访问的和强大的，但我认为从长远来看，它们是设计和构建 web 应用程序的一种更具挑战性的方法，并且可能会增加维护您的 web 应用程序的难度。