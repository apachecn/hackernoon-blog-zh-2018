# 世界上最强大的两个字母的单词

> 原文：<https://medium.com/hackernoon/the-most-powerful-two-letter-word-in-the-world-ee43545482a9>

![](img/ccc65e49c57a2d61f9cd56801e4a0343.png)

Morpheus offering Leo the Red Pill and the Blue Pill — Copyright [Warner Brothers.](https://www.warnerbros.com/matrix)

这个词代表了引导宇宙和所有生命的因果法则。这是一个很小的词，它本身什么也不做，而是完全由其上下文定义的。没有任何一个词比 IF 这个词包含两个字母的力量更大。

如果唐纳德·特朗普按下他桌子上的一个红色大按钮，一枚核导弹就会发射。如果你犯了谋杀罪，你就得进监狱。如果你的尿液中含有糖分，你(很可能)患有糖尿病。如果你没钱，你就破产。如果你选择红色药丸，你就揭示了母体。如果你喜欢阅读这篇文章，你会喜欢并在社交媒体上分享它。:)

IF 用于语言、数学、科学、法律、商业、健康、金融、政治、体育甚至人类情感的各个方面。我认为 IF 只是一个两个字母的单词的原因是因为我们用得太多了。

我们的父母从小就教导我们因果的普遍规律:如果你吃了你的西兰花，你就可以吃冰淇淋。即使是野生动物也本能地知道因果关系:如果你看到捕食者，快跑。

在设计数字系统和开发软件 20 年后，我是在计算机编程中使用 IF 的大师。但对任何人来说，成为中频各方面的专家都很容易，不需要成为软件编码员。

![](img/ba202c507aef05b296f28faa4d015c4a.png)

Image Source — [infografx / 123RF Stockfotos](https://nl.123rf.com/profile_majdansky)

在这篇文章中，我要让你成为世界上最有力的两个字母的大师。我们将自下而上，从布尔代数到电子学，到低级编程，到高级编程，到软件建模，到《我的世界》，甚至到人类关系，探索所有形式的 IF。

# 二元数学

在我大学学习之初，我正在学习电气工程，并参加了我在大学的第一堂数字系统课。我还在从前一天晚上的校园聚会的宿醉中恢复过来，这时我们的讲师走进教室，发现学生们一片混乱，这是大多数大学讲座的前奏。

他站在教室的前面，耐心地等待我们开始注意，这花了一段时间……但一旦有了沉默，他又推迟了一些，只是看着我们，然后平静地用一句史诗般的，清晰的声明开始了我们的学期，我永远不会忘记:

“开始的时候，什么都没有，就有了。”

然后，他用粉笔在黑板上写下数字 1 和 0，并告诉全班同学，这两个数字是我们整个学期将要学习的全部内容。

他没有开玩笑，我们整个学期都在讲数系和 1 和 0 的二进制逻辑，它们比看起来要多得多。在二进制数字系统中，只有两个数，一和零，真和假，一切数字的基础。

最基本的逻辑功能是“非”、“与”和“或”门。

*   如果某事是假的，那么它就不是真的。
*   如果某个值或另一个值为真，则结果为真。
*   如果某个和另一个值为真，则结果为真。

这是一个包含 3 个最基本的逻辑门符号和真值表的图表。

![](img/433a912718443918797a1998c3b6b0c8.png)

Image Source — [https://vicgrout.files.wordpress.com](https://vicgrout.files.wordpress.com/)

使用演绎推理，可以从逻辑中构建算法 IF。

*   因为 TRUE 和 TRUE 等于 TRUE，所以使用 TRUE 和 AND，可以测试未知值是否为 TRUE。
*   因为 FALSE 或 TRUE 等于 TRUE，所以使用 FALSE 和 OR，可以测试未知值是否为 TRUE。

我承认在我的宿醉状态下，我在课堂上很痛苦，然而，信息是如此简单，我设法跟上；事实上，我感觉我已经知道了这一切，我做到了，因为所有人都知道这一点，这是我们基因构成的一部分。

布尔代数是因果的普遍法则，以 1 和 0 的数学形式表达。从本质上讲，所有软件程序本质上都是决策树，是深度嵌套的 IF 语句的结果。

# 电子逻辑

![](img/3f47527161c70306e48a377a3f2cd6e7.png)

Image Source — [Wikipedia — History of the Transistor](https://en.wikipedia.org/wiki/History_of_the_transistor)

在电子学中，电阻-晶体管逻辑用于在直流应用中构建逻辑门。通常，零用 0 伏表示，真用 5 伏表示。

晶体管的物理特性是，当你在(NPN)晶体管的基极施加足够高的正电压时，它允许电流从集电极流向发射极。电阻器的物理特性会在电流流过时导致电压下降(欧姆定律)。

这是一个与门的电路图。

![](img/fe4aac908010c9b270cc0cb132cc78c5.png)

Image Source — [electronics-tutorials.ws](https://www.electronics-tutorials.ws/logic/logic_2.html)

电子、半导体和电阻晶体管逻辑的详细理论以及它们的工作原理是一个非常大的信息量，这里不做介绍。

# 电子算法

通过组合大量的电子逻辑门，可以构造出加、减、乘、除等数学函数。

算法逻辑单元(ALU)是一种复杂的电子逻辑门结构，提供软件程序经常使用的计算。

存在于所有类型的计算机和设备中的每个中央处理单元(CPU)都包含 ALU 以提供算法的集合，这些算法被暴露给在 CPU 上执行的软件程序。

CPU 也是大规模的逻辑门网络，但其目的是处理指令并在内部组件(如 ALU)和外部组件(如硬盘驱动器和随机存取存储器(RAM))之间移动数据。

![](img/c2185cd813b5d6e8e4f37dc320e2de55.png)

Image Source — [thebeginnerspoint.com](http://www.thebeginnerspoint.com/write-block-diagram-computer-write-processes-computers)

CPU 和用来控制它们的软件程序是驱动所有计算机软件和网络的电子基础。

# 低级编程

在编程中，IF 语句由三部分组成:

*   表达式(确定下一步做什么的测试)
*   然后(表达式为真时怎么办)
*   ELSE(当表达式为假时做什么)。

注意，ELSE 是可选的，因为结果也可能是什么都不做。

在 CPU 内部的机器语言中，全是 1 和 0，不适合人类直接使用，所以我不在这里讨论，相反，我将跳到适合人类的最低层次的编程，即汇编。

![](img/1f0f20f2959d081e8656e80a5249ae48.png)

Image Source — [microchip.com](https://www.microchip.com/wwwproducts/en/PIC16F84)

使用一个简单的微芯片 8 位 PIC16F84 微控制器的汇编指令集，如果看起来像这样:

```
1: BTFSS FLAGS, 3
2: BSF PORTB, 0
3: BCF PORTB, 0
```

简单地说，这相当于:如果标志寄存器的位 3 被置位，则设置端口 b 的位 0，否则清除端口 b 的位 0，其中:

*   BTFSS =位测试文件(寄存器，位)跳过(IF)设置
*   FLAGS =字节 RAM 存储器的地址名称
*   BSF =位设置文件(寄存器，位)
*   端口 B =连接到芯片输出端口(引脚)的寄存器，用于为 LED 等外部电子器件供电或与之通信。

这三条指令检查名为 FLAGS 的寄存器中的第三位是否为真。如果为真，芯片 B 端口的输出引脚 0 设置为 5V，否则设置为 0V。如果 LED 连接到输出引脚，它将根据标志的值打开或关闭 LED。

CPU 使用指令在永久存储器中的位置逐一处理每条指令，在这个(假设的)示例 RAM 存储器位置 1–3 中，地址 1 的指令包含 IF 和表达式，地址 2 的指令包含 THEN，地址 3 的指令包含 ELSE。

汇编直接类似于机器码，需要对 CPU 的工作原理有详细的了解。它很难读和写，并且容易出错，因此随着时间的推移，高级编程语言发展得使人们使用起来更简单。

# 高级编程

高级编程语言旨在使编程对人类来说更容易。人类代码在被发送到 CPU 之前被编译成机器代码。

![](img/0a9c98f0cff2ff9a21568be2bd87c41f.png)

Image Source — [Wikipedia — Java Programming Language](https://en.wikipedia.org/wiki/Java_(programming_language))

在 Java、JavaScript、C/C++/C#中，IF 操作看起来是这样的:

```
if (someValue === true)
 doSomething()
else 
 doSomethingElse()
```

这比汇编容易理解多了，即使你以前没见过计算机代码。简单的英语表达就是:如果某个值为真，那么做点什么，否则做点别的。

# 软件建模

软件建模是软件应用程序的设计。统一建模语言(UMLTM)是一种广泛使用的设计软件和流程的国际图表标准。

UML 活动图(又名流程图)包括决策节点，这些节点在视觉上表示为一个菱形(如下)，有一个输入和两个或更多的输出。

![](img/5cce2c61b36b6dfb1e30c5cd295a4516.png)

Image Source — [infografx / 123RF Stockfotos](https://nl.123rf.com/profile_infografx)

# 电脑游戏

在《我的世界》，流行的电脑游戏 IF 可以由玩家在虚拟世界中使用方块和通过使用红石建造门来建造。

![](img/37a044514dd29a068b9260e21d5742e6.png)

Image Source — [Wikipedia — Minecraft](https://en.wikipedia.org/wiki/Minecraft)

雷石东使自动化在《我的世界》成为可能。它可以用来控制活塞、门和灯，广泛用于自动农业、陷阱、控制铁路和控制你的基地的各个方面。

红石改变导电行为，基于它被放置的块的位置。使用 Redstone 有一种简单但不总是直观的信号传输方式。这里有一个视频，我在《我的世界》用 Redstone 解释二进制逻辑的基础。

Redstone Logic In Minecraft

# 人际关系

在人类语言中，如在商业关系中使用的，IF 通常被表达为作为协议的一部分做某事的条件。如果你每月给我 1000 美元，你就可以租这个公寓。

![](img/1c5c5503e46933880c3baae913d5390a.png)

Image Source — [oneinchpunch / 123RF Stock Photo](https://www.123rf.com/profile_oneinchpunch)

如果能经常在私人关系中找到。如果你今晚做饭，我就洗碗。如果你爱我，你会…好吧，嗯…也许最好从你的个人经历中吸取教训。

到目前为止一切顺利。这足以涵盖经典 IF 背后的所有实用理论。如果有那么简单就好了，那么你现在可以停止阅读了…

# 嵌套 IF

需要注意的是，经典的 IF 编程语句只执行一个测试来确定两个结果之一，THEN(表达式为真时做什么)和 ELSE(表达式为假时做什么)。

然而，在实践中，单个 IF 通常不足以解决复杂的问题，可能需要多个嵌套和/或顺序 IF 来确定结果或流程。在高级计算机语言中，IF 操作也有一些高级形式的 IF，如开关和分支。

SWITCH 操作是一个 IF-ELSE 链，用于测试文字值，比如数字和字符串，BRANCH 操作是一个 IF-ELSE 链，用于测试多个表达式的真值。

![](img/9159a8f2d60f29cefda0ac56050c8a8c.png)

Image Source — Flowchart created on [Gliffy](https://www.gliffy.com/)

经过多年的软件开发，我得出结论，分支是最有用和最通用的条件逻辑模式。原因是 BRANCH 支持文字值和表达式，可以有两个以上的结果，但仍然可以作为简单的 IF 和开关。

![](img/7a683ceb557d42980c89269da0023fc1.png)

Image Source — Flowchart created on [Gliffy](https://www.gliffy.com/)

一旦你理解了一个分支是如何工作的，你将很快意识到你可以如何构建任何决策树。因为它的实用性，BRANCH 比简单 IF 更适合作为我们拖放逻辑设计语言的核心组件。

![](img/5a4190bded76a867b4ff51137f6cc3e8.png)

Image Source — Created on [ExpertBox.com](https://www.expertbox.com/)

虽然 IF 是世界上最强有力的词，BRANCH 是其最普遍的模式，但它本身除了表明条件存在之外没有任何意义。没有表达式和文字，就没有意义。

正是学科领域知识的逻辑和嵌套赋予了这个词以力量，强大的力量带来了巨大的责任。如果逻辑错了，那么结果就是错的。

在全球化、标准化、监管和自动化的时代，知识正在变得统一，社会中的许多职业角色，如律师、会计师、医生和金融顾问，将部分甚至完全被由 IF 和另一个强大的两个字母缩写 AI(人工智能)驱动的机器所取代。

如果你是 IF 的大师，如果你是任何结构化知识领域的专业人士，那么你应该考虑让你的知识自动化，否则大型跨国公司的人工智能可能会抢走你的工作。

> [*专家框*](https://www.expertbox.com/) *是一个逻辑驱动的知识自动化平台，专为知识工作者和业务流程自动化而设计。免费注册，并使用您的 IF 掌握自动化，货币化和分享您的知识。*

这篇文章最初发布在[ExpertBox.com](https://www.expertbox.com/blog/the-most-powerful-two-letter-word-in-the-world)