# 如何不做一个平庸的开发者！

> 原文：<https://medium.com/hackernoon/how-not-to-be-a-mediocre-developer-c59a49f97fc5>

*免责声明:我不是最好的开发人员，但我欣赏和推理的差异，使一些开发人员脱颖而出。*

![](img/23c2265b8451130642a4571c7f954ff0.png)

Photo by [Joshua Earle](https://unsplash.com/@joshuaearle?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 编写更多代码

如果你想在某件事上做得更好，那么你必须花时间去做那件事，可悲的是，除此之外别无他法。不管你读了多少文章，看了多少遍文件，除非你把你的双手和心灵付诸行动，否则你不会有所提高。这种设计模式在开始时似乎很难实现，但当你在多种环境中实践了它的使用后，它就会变得轻而易举。

## 合并测试

当我第一次开始为自己的代码编写测试时，我惊讶地发现我缺乏编写好测试的思维模式。编写测试将使你能够以你最初想象不到的方式来看待你的代码，因为当进行测试时，你必须考虑这个东西是如何崩溃的，并且你意识到你在你编写的那个函数中做了太多的事情，分成多个函数可能会更好，因为很难对一个做多件事情的函数进行测试。

让我们看看下面的例子

```
function postData(data) {
 boolean valid = true;
 // check if data is defined
 if (data === undefined) {
   valid = false;
 }// check if email is well formed
 if (!regex(data['email']) {
   valid = false;
 }// check if password is atleast 8 chars.
 if (data['password'].length < 8) {
   valid = false;
 }if (valid) {
  http
   .post(`example.com/user/create`, data)
   .then((response) => {
    //append to the list
    this.users.append(response.userid);
   })
   .catch((error) => {
    // show errors.
   });
 } else {
   showValidationError();
 }
}
```

所以方法`postData`要做多件事，比如验证数据，在承诺完成时追加到用户列表，以及处理错误。为`postData`编写单元测试将是困难和混乱的。您可以将它分成多个方法，并分别测试每个方法，例如

```
function postData(data) {
 return http
   .post(`example.com/user/create`, data);
}function validate(data) {
 // check if data is defined
 if (data === undefined) {
   return false;
 }// check if email is well formed
 if (!regex(data['email']) {
   return false;
 }// check if password is atleast 8 chars.
 if (data['password'].length >= 8) {
   return false;
 } return true;
}function appendUsers(userId) {
  this.users.append(response.userid);
}function main() {
 if (validate(data)) {
  postData(data)
   .then(data => appendToList(data.userId))
   .catch(error => handleError(error))
 } else {
  showValidationError();
 }
}
```

您已经可以看到为什么编写测试会产生质量更好的代码，您必须将您的长方法分割成多个更小的单元，并且每个单元都可以被原子地测试。

## 诚实

对自己了解透彻的和不了解的要坦诚。假装你知道一个 API 的来龙去脉永远不会帮助你提高，事实上，如果你碰巧说了一些愚蠢的话，因为你对一个 API 或一个主题缺乏了解，你可能会在讨论中丢面子。

## 为开源做贡献

为开源做贡献会让你接触到工作中可能永远不会出现的场景，从而限制你的视野。您可以了解在分布式场景中运行一个项目需要什么，引入不间断的更改和其他新的开源工具等，好处是无穷的，我们都知道开源如何直接和间接改变了每个人的生活。

![](img/c66dab4c5669d246f467ac7db8551b55.png)

by [rawpixel](https://unsplash.com/@rawpixel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 乐于助人

在你知道的事情上帮助别人会让你成为那个概念/特性/API 的“目标”,从而降低你在团队中的价值和重要性。对你可能不擅长的事情敞开心扉去帮助，你可能最终会从经验中学到一些有价值的东西。

## 选择一个个人项目

个人项目是学习新的框架和技术的好方法，这些你可能在工作中没有体验过。对于你的个人项目，你是产品经理，你是[开发人员](https://hackernoon.com/tagged/developer)和架构师，所以你可以想象你需要做多少决策。你可以利用项目中的经验，在工作中或社区中提出新的框架和工具，像⭐️一样闪耀

## 放下你的自负

不要让你的自我和你的职位妨碍你的学习和进步。少关心你是谁，多关心你每天会变成什么样。一个组织中的架构师可能是其他组织中的软件开发人员。对于你认为自己擅长的事情，要随时接受新的不同的方式。你可能会失去一个更有效的算法或一个更好的功能设计。

don’t be the kanye in your team

## 理解“为什么”

在接受和阻止你对一个新框架、模式或 API 的信念之前，首先尝试理解它为什么会存在。试着获得一个概念存在的直觉。

```
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```

以上是你在 [vue.js](https://vuejs.org/v2/guide/) docs 网站上遇到的第一个代码示例。即使当我看着这个非常基本的例子时，我也试图在我的脑海中推理出以下事情:

*   为什么用`new`关键字来创建组件？为什么他们没有创建对象的工厂模式？
*   貌似`el`属性带元素的`id`，为什么用`#`？这是否意味着我可以添加其他元素选择器，比如属性和类？
*   `data`看起来像是`vue`对象的一个非常普通的属性名，它到底想表达什么呢？

我不是说你应该对每件事都持批评态度，但是养成这个习惯有助于更好地理解事物的哲学，从而提高你的理解力。

## 不要偷懒

懒惰可能会妨碍你展示你的技能或你关心的事情，例如，如果你相信重构会提高性能，那就去做吧，添加注释以节省其他开发人员的时间，记录你构建的新 API。你在代码中增加的时间等于为其他试图理解你所写内容的开发人员节省的时间。

## 解决编码挑战

解决编码挑战迫使你去思考那些我们在日常生活中认为理所当然的事情。我说的是我们代码的空间和时间复杂度。有些人认为解决挑战是不实际的，因为大多数东西都是抽象的，你只需要使用 API。

但是我不同意！它不仅能帮助你批判性地看待代码，还能让你有信心拿出性能方面最好的代码，另一个好处是你可以随时为面试做好准备😉

一些解决挑战的网站有[黑客排名](http://hackerrank.com)、 [leetcode](http://leetcode.com) 、 [topcoder](https://www.topcoder.com/) 和 [spoj](https://www.spoj.com/) 。

## 鼓励好的东西

如果你喜欢你同事的承诺，那就不要犹豫，留下一条信息，欣赏或支持 stackoverflow 上帮助你的答案，或者为 medium 上给你自由智慧的文章鼓掌，或者开始你在 github 上查看的那个有趣的项目。鼓励他人有助于让他们发挥出最好的一面，最终你也会如此。

## 不要躲在图层后面

您发现您在视图中使用的 API 有问题，但是您无法修复它，因为您是“前端开发人员”。在我看来，这是一种不好的态度。[编程的基本原则](https://hackernoon.com/tagged/programming)比如保持代码干燥，如果你看到你的类有多个用例就使用抽象，捕捉所有流程控制路径的异常等等。适用于堆栈的几乎每一层。记住这些基本原则，它可能会帮助你解决那些你认为你不能触及的问题，因为你不在代码库的那个部分工作。

*结论:正如我上面所说的，阅读本指南将帮助你认识到你还没有做过的事情，但是要做到以上几点，你必须付出努力和自律*🙂

如果你喜欢这篇文章，别忘了👏并与其他开发者分享🙂。希望对你有用！