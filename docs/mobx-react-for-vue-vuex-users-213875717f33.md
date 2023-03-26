# 面向 Vuex 用户的 MobX 和 React

> 原文：<https://medium.com/hackernoon/mobx-react-for-vue-vuex-users-213875717f33>

## 或者只是一个不错的 MobX 教程

![](img/ab04910bc7cb2a860b2698babc302111.png)

Photo by [Karsten Würth (@inf1783)](https://unsplash.com/@inf1783?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 框架战争…不是😜

我从 Laracast 的 [Vue](https://vuejs.org/) 和[this](https://laracasts.com/series/learn-vue-2-step-by-step)***amazing***系列开始了我的框架仙境之旅。我听说过 React，但当时当我看着 JSX 时，它对我来说似乎是一种憎恶！把 html 标签和 JS 逻辑混在一起？他们没听说过设计模式和关注点分离吗？看在上帝的份上，为什么不使用 JQuery 制作一些闪烁的文本呢？

但是后来我学习了 JS，不是 *JQuery JS* (* 用 DOM 耍把戏)，而是 JS 作为一种开发语言，一种优雅的表达工具来操纵数据，一种用漂亮的方法来处理数组、字符串和对象的语言。你不能用香草 JS 做的事情很可能有一个 [Lodash](https://lodash.com/) 来做，或者*肯定有一个 NPM 包。(`npm install make-me-a-sandwich` Lol！)*

在这一切之后，我又看了一眼 React 我立刻爱上了它！其中 Vue 有点像添加了功能的 html(`v-if, v-for...`)JSX 是普通的 JS，恰好生成 DOM。实际上，我并不想在这里挑起一场框架之战。我目前在生产中使用 Vue 和 React 应用程序，并且我已经能够在这两个框架上找到我工作所需的所有工具。这个选择实际上取决于个人偏好或者你的公司/团队的偏好。

## 但是反应过来…

对于一个 React 初学者来说，似乎 Redux 是 Vue 的默认商店，因此我决定学习 Redux……在看了完整的 egghead 系列大约 3 遍并浏览了同样多遍文档后，我仍然觉得我不知道自己在做什么。除了微不足道的缩减器之外，任何东西都是令人头疼的。规范化数据、关系表、不变性和 DB 技术，它们看起来都是 Redux 的先决条件！我确实学到了很多，但还不足以让我觉得有所收获。然后是 Redux 和高阶函数的包装器，以生成其他函数和 reducer 配方…我感觉自己又像一个实习生，无法完成我的项目，因为我不知道如何拥有一个简单的商店，并让我的应用程序*对它做出反应*！😥

我敢肯定，有很多比我更聪明的开发人员使用 Redux 非常高效。我尊敬你，老师！

我怀念 Vuex 的日子，在那里一切都可以正常工作，可变状态不是罪恶，无论我在哪里调用商店操作，整个应用程序总是同步的。但是我也很喜欢 React，所以我开始尝试在 React 上使用 Vuex，(显然不能)，但是在搜索中我遇到了 MobX。它肯定有大胆的主张:

> MobX 通过解决根本问题使状态管理再次变得简单:它使得不可能产生不一致的状态。 [*](https://mobx.js.org/getting-started.html)

我再次前往 egghead 观看视频教程。他们很好，但运气不好！不支持装饰者，没有装饰者你不会有很多例子来说明如何做事。然后还有`mobx-state-tree`声称很有主见(那是轻描淡写！)而且，我不知道如何做事，也不知道发生了什么！这些视频教我如何制作一个相当不错的柜台，T2 感觉我正在编写一个企业银行应用程序，但是我找不到中等难度的。

我真的很喜欢 React，所以我回到 Redux，但无法让它工作，所以，我再次不情愿地给了 MobX 另一个尝试...*我终于看到了光明；就像雾已经消散了一样*

![](img/3613ce4d777f0945fc633765e2df6dc0.png)

Shameless Disney plug!

我想让你看看 MobX 比你想象的更容易使用。我们将使用 MobX 做更多的事情，而不仅仅是向上和向下计数，并且比`mobx-state-tree`实现更简单。

## 商店

我们将制作一个简单的商店，从我目前正在做的项目中提取。在其中，我们将有一个学生列表和一个课程列表，我们将能够从一个课程中注册和取消注册学生。

起点是一个干净的`create-react-app` app，这里是`store.js`:

🙄不要让长度吓到你，它实际上很简单，学生和课程的方法是一样的。

在 MobX 术语中，有**可观察的**，观察变化的*，以及观察*变化的**观察者**，观察的*。接下来就是魔法了相当神奇！*

![](img/ac2ce5554e34f2d26df006591fd8d55b.png)

首先我们简单地将`const store`声明为`observable()`的第一个参数。* *这就是你如何声明一个不使用修饰语法的可观察对象(* `*@observable*` *)。*

在传递给`observable()`的对象中，有三个地方可以存放数据:*学生*、*课程、*和*注册、*以及那些被定义为`observable.map()`的。如果这是你第一次遇到[地图](https://mobx.js.org/refguide/map.html)，不要担心，它只是一个奇特的对象，有一个很好的方法来访问其中的内容(setters 和 getters)。在普通的 JS 对象中，数据是这样访问的:`object.key1.key2`或`object['key1']['key2']`，在地图上，你可以这样访问:`object.get('key1').get('key2')`。这有几个好处，在这个特别的项目中，它让我不用使用大量的`_.set()`和`_.assign()`(来自 Lodash)。

然后是添加、更新和删除学生和课程的存储方法。它们只是简单的带有一些错误处理的`.set()`。

下一个有趣的地方是注册处理。这个想法确实有一种冗余的味道([关系和表](https://redux.js.org/recipes/structuring-reducers/normalizing-state-shape#relationships-and-tables))，在这种情况下很有用。我正在创建一个对象，它的键等于课程 ID，值是注册学生 ID 的数组。看起来是这样的:

```
{
   course1: ['student1', 'student2', 'student3'],
   course2: ['student4', 'student5', 'student6'],
}
```

动作发生在`enrolledStudents`方法中。我们来分解一下:它返回一个`computed(() => {...})`函数，这是一个 MobX 表达式，用来观察其内部元素的变化，并自动更新其值。没什么特别的。接下来，它获取注册映射中与作为参数传递的课程 ID 相匹配的元素，这将返回学生 ID 的数组。然后通过数组中的一个`.map()`用每个学生的 ID 替换整个学生对象。请注意，最后一个单独的`.get()`应用于`computed()`并获得实际数据。
因此`enrolledStudents`方法的结果是一个数组，包含了所有注册了特定课程的学生的对象，抱歉，是一个*可观察数组*，因此函数中数据的任何变化都会更新其返回值并进行适当的重新渲染！🤩

下面是一个简单的 React 组件来展示这一点:

这是一个非常标准的 React 组件，除了`observer`和`store`。

首先，定义几个学生和一门课程，并将其添加到存储中。然后`student1`注册了这个课程。接下来，我们使用我想要的学生列表的课程 ID，通过来自商店的`enrolledStudents`方法进行映射，结果与预期一致。

当你按下“注册学生 2”按钮时，有趣的部分发生了。
在商店中，学生 ID 被添加到相应课程地图的数组中，MobX 负责保持所有内容的同步，正确地*计算*`enrolledStudents`*、*的值，并进行适当的重新渲染。所有这一切都是因为 L39，我们将`StudentsList`包裹在`observer()`中！

请注意，您的组件可以通过多种方式访问存储。您可以从文件中导入组件，就像前面的例子一样，您可以从父容器中将它作为道具传递下去，或者您可以使用`mobx-react` provider/inject 实用程序(在我的例子中，我可以在需要时导入`store`)。

## 结论

MobX 感觉确实有点不可思议，当我最终理解它时，它就像一股新鲜空气冲进了我的 React 开发中！(那是对上图的引用)。希望你在探索它的过程中获得乐趣，并继续制作酷的、棒的东西！😎