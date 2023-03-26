# 如何用 Kotlin 编写你的第一个程序

> 原文：<https://medium.com/hackernoon/how-to-write-your-first-program-in-kotlin-8554cb526158>

我听说你想学编程。

很可能你正在研究 Kotlin，因为你想创建一个 Android 应用程序。或者，也许，你正试图得到你的手在后端 web 开发。

无论是哪种情况，Kotlin 都是一种非常好的入门语言。

您已经在寻找一个好的教程，但是无论如何，它们都需要以前的 Java 知识。

这不是很不幸吗？

从初学者的角度来看——绝对是！

那么，你会发现这个由多个部分组成的教程正是你所需要的，因为它没有假设任何以前的编程经验。

就此而言，本教程不会深入 Android 世界或 web 开发世界，但它将为您开始编程之旅打下坚实的基础。

![](img/583336470317ff28be5a5fa796542b3f.png)

“Your programming journey” (source: [pexels](https://www.pexels.com/photo/landscape-photography-of-rock-formation-near-highway-844167/))

但是在你开始之前:

# 我需要在电脑上安装什么？

*   终端应用。
*   支持 Kotlin 语法突出显示的简单文本编辑器(例如: [Atom](https://atom.io/) + [kotlin 包](https://atom.io/packages/language-kotlin)工作良好)。

> PS:如果你需要在 Atom 中安装 Kotlin 包的帮助，这里是 Atom 官方手册上关于那个的[教程。](https://flight-manual.atom.io/using-atom/sections/atom-packages/)
> 
> PPS:为什么不用 IntelliJ IDEA 或者其他 IDE 呢？
> 
> 尽管我很喜欢 IntelliJ，但我理解这对一个初学者来说可能会很困惑。
> 
> 同样，这也不允许你学习程序开发周期(编辑、编译、运行),因为 IDE 会为你做所有的事情。
> 
> 如果你已经是一个经验丰富的程序员，并且不需要这样的细节，而你只是想学习一些 Kotlin，那么使用任何你喜欢的编辑器或 IDE。但是请记住:本教程将使用`kotlinc`编译器。
> 
> 在我的一个高级教程中，我们充分利用了 IDE。

*   需要 JDK(针对您的平台: [Mac OS X](https://iwillteachyoukotlin.com/start/jdk-macosx/) ， [Linux](https://iwillteachyoukotlin.com/start/jdk-linux/) ， [Windows](https://iwillteachyoukotlin.com/start/jdk-windows/) )来运行 Kotlin 编译器编译的程序。
*   [科特林编译器](https://kotlinlang.org/docs/tutorials/command-line.html) `[kotlinc](https://kotlinlang.org/docs/tutorials/command-line.html)`。正确安装后，您应该能够在终端应用程序中运行命令`kotlinc -version`:

```
$ kotlinc -version
info: kotlinc-jvm 1.2.21 (JRE 1.8.0_152-b16)
```

版本可能会有所不同，因为该语言的作者( [JetBrains](https://www.jetbrains.com/) )经常更新该语言。

# 但是你说我不需要 Java…

当你在安装 JDK 的过程中，你可能想知道，为什么你要安装一些 Java 的东西。

我刚刚告诉过你，这个指南不需要 Java 知识，不是吗？

事情是这样的。

虽然 Kotlin 是一种独立的语言，但它依赖于 Java 的标准库和开发工具包。

你现在不必想太多。在您的第一个应用程序中，它将帮助 Kotlin 构建和运行您的应用程序。适当的时候，我们会解决这个问题。

在这种情况下(目前)，我们应该马上开始在 Kotlin 中创建您的第一个程序！

# 你好世界——无聊…无聊？

我知道这很无聊，你可能已经看过几次了，但是在这篇文章中，我们不仅仅是要创建和运行你的“Hello World”程序，而是要:

*   理解程序的代码和所有组成部分。
*   探索构建和运行应用程序时会发生什么。

此外，作为练习的一部分，您还将学习编程中可能会出现的错误:

*   你要在屏幕上打印一些更有趣的东西。
*   你将会以各种方式破坏程序，这样你就可以体验到开发周期中所有提到的错误。

说到这个…

# 科特林的开发周期

在 Kotlin 中，我们将编程过程分成几个步骤:

1.  在你的文本编辑器中输入程序并保存到`ProgramName.kt`文件中。
2.  通过在终端应用程序中键入`kotlinc ProgramName.kt -include-runtime -d ProgramName.jar`来编译它。我们稍后将讨论这个命令的部分内容。
3.  在终端应用程序中输入`java -jar ProgramName.jar`，运行编译好的程序。

第一步，您将使用 Kotlin 编程语言创建一个人类可读的程序表示。

第二步是将人类可读的程序翻译成适合机器执行的语言。`-d ProgramName.jar`选项指定结果将被写入哪个文件。并且`-include-runtime`告诉 Kotlin 编译器包含它的标准库。如果没有这个选项，您将在下一步看到一个运行时错误。

第三步运行程序。

让我们看看“你好，世界”节目将会是什么样子:

# 在科特林创建你的第一个程序

## 写一个科特林程序。

程序是一系列字符(如字母、数字、空格和特殊符号)。它就像一个句子、段落或一首诗。

要编写一个程序，你需要使用你的文本编辑器输入这些字符，并保存到名为`HelloWorld.kt`的文件中:

## 编译一个 Kotlin 程序

Kotlin 编译器(`kotlinc`)是一个应用程序，它将你用 Kotlin 语言编写的程序翻译成一种更适合在计算机上执行的语言。

它将扩展名为`.kt`的常规文本文件(包含您的程序)作为输入，并创建一个扩展名为`.jar`的文件(适合在计算机上执行的版本)。

要编译 Kotlin 程序`HelloWorld.kt`,请在终端中键入以下文本:

```
kotlinc HelloWorld.kt -include-runtime -d HelloWorld.jar
```

如果您输入的程序正确，您应该看不到任何错误。如果您在某个地方打错了，您会看到类似如下的错误输出:

```
HelloWorld.kt:1:14: error: expecting comma or ')'
fun main(args Array<String>) {
             ^
HelloWorld.kt:1:15: error: parameter name expected
fun main(args Array<String>) {
              ^
HelloWorld.kt:1:10: error: a type annotation is required on a value parameter
fun main(args Array<String>) {
         ^
```

该输出表明存在编译错误。如果你得到类似的东西，确保你正确输入了`HelloWorld.kt`程序。如果有必要，请逐个字符检查。

当您的程序成功编译时，您应该看不到任何输出。通常，Kotlin 编译器的任何输出都表示排序错误或警告。

## 运行一个科特林程序

当你有一个编译好的程序，你可以运行它。当计算机运行您的应用程序时，它会准确地遵循您提供的指令。

这个事实是编程最重要的部分——你可以让你的计算机精确地做你想做的事情。

要运行您的`HelloWorld.jar`程序，请在终端中键入以下命令:

```
java -jar HelloWorld.jar
```

如果一切正常，您将在终端中看到预期的输出:

```
Hello World
```

## 了解该计划

`println()`行是文件`HelloWorld.kt`中最重要的一行。该行是在终端中打印“Hello World”文本的行。我们将处理程序的其他部分，例如`fun`、`args`、`Array<String>`，稍后我们将编写更复杂的应用程序。

`println()`功能是在标准输出上打印提供的文本。标准输出通常与终端中的输出相同，但并不总是如此。有时，标准输出可以被重定向到其他地方，例如到文件中。

文本`"Hello World"`是一个字符串文字。字符串文字是一种文字，表示程序源代码中文本的固定值(`String`类型)。我们稍后会回到类型和文字。

## 创建您的程序

现在，你所有的程序都会很简单，就像`HelloWorld.kt`一样。唯一的区别是你要放入`main()`函数的语句顺序。创建程序的最简单方法是:

*   将`HelloWorld.kt`的内容复制到`MyProgram.kt`(其中“MyProgram”是您的新程序的名称)
*   将`println()`语句替换为您的语句序列:

# 错误

无论做什么，编程时都会有错误。它们可以通过仔细检查程序来修复(就像你在给朋友的邮件中修复语法和拼写错误一样)。您将会遇到几种错误:

## 编译时错误

Kotlin 编译器捕捉这些错误。发生这种错误是因为源代码的结构不太正确，Kotlin 编译器无法理解您试图告诉它的内容。

Kotlin 编译器会告诉你源代码中哪里有错误，以及编译器期望的是什么。Kotlin 编译器将试图解释为什么它不能把代码翻译成机器可执行的语言。

## 运行时错误

当您运行程序时(例如，当您运行`java -jar HelloWorld.jar`时)，执行系统会捕获这些错误。发生这种错误是因为程序试图执行无效的操作，例如:

*   被零除，
*   试图访问不存在的数据，
*   试图读取一个你无权读取的文件，
*   试图访问互联网上不可用的资源，
*   诸如此类。

## 逻辑错误

这些错误也被称为 bug。这些错误通常意味着程序在运行时产生错误的答案或不正确的结果。

例如，如果你有一个程序，它要求用户输入两个数字，并打印出这两个数字的和。当用户输入`7`和`5`时，程序打印的是`13`而不是`12`。这是一个错误的答案，因此程序有一个逻辑错误(bug)。

检查错误的结果和程序的源代码，找出它为什么行为不正确，这完全取决于程序员。作为一名程序员，当你发现“为什么”时，你应该能够修复这个错误。

逻辑错误是最难的。它们可能很微妙，不可复制，很难找到。

作为一名程序员，能够识别您正在处理的是哪种错误，并仔细检查您的源代码以找到失败的原因并修复它是至关重要的。这是你应该通过练习和实践学习的首要技能之一。

经过足够的练习，你应该会越来越好地避免这类错误。写程序是一项需要持续细心和勤奋的活动。

# 输入和输出

编写一个程序，不仅在屏幕上输出文本，还能与用户交互，这不是很好吗？

这是任何程序的典型用例。很少有应用程序不需要任何用户输入，所以你必须学会如何去做。

在命令行应用程序中，有两种简单的方法可以做到这一点。

*(你不会想直接进入图形或移动应用的世界，因为那里的复杂性非常高——首先是基础)*

## 命令行参数

第一种方法是从命令行参数中读取用户输入。例如，要访问第一个命令行参数，您可以使用`args[0]`。为秒— `args[1]`。第三名——`args[2]`。诸如此类。

下面是读取第一个参数的示例程序:`ReadFirstArgument.kt`:

现在尝试编译:

```
kotlinc ReadFirstArgument.kt -include-runtime -d ReadFirstArgument.jar
```

并运行它:

```
java -jar ReadFirstArgument.jar Peter
```

您应该会看到预期的输出:

```
Hello, Peter! How are things going?
```

试着用你的名字代替“彼得”的说法。它应该问候你，问你事情进展如何！

## 从标准输入读取

现在，这种方法更具互动性，但也更复杂。这将涉及到一些概念，我们稍后会讲到。

首先，我们将要求用户输入他们的名字，我们将把用户输入读入一个局部变量`name` *(关于局部变量——稍后)*:

这里最重要的部分是`readLine()`。它一直读取用户输入，直到行尾(当用户按“Enter”时)。

然后我们将使用局部变量`name`，就像我们在之前的程序中使用`args[0]`一样:

要编写这样的程序，创建文件`ReadFromInput.kt`并在文本编辑器中键入以下内容:

然后使用`kotlinc …`命令编译该程序，并使用`java -jar …`命令运行它。当您正确完成后，您应该会在运行程序时看到以下内容:

> 你叫什么名字？玛丽
> 
> 你好，玛丽！事情进展如何？

*(重点是用户输入的内容)*

尝试使用其他名称—它应该会像您预期的那样工作。

# 练习

*   写一个程序`HelloWorldMultipleTimes.kt`，将输出“你好，世界”十五次。
    如果你被卡住了或者想查看答案[在这里找到解决方案](https://iwillteachyoukotlin.com/programming-101-with-kotlin-chapter-1-solutions/?hello-world-multiple-times)。
*   编写一个程序`ReadTwoArguments.kt`，它将读取第一个和第二个命令行参数，并将它们作为用户的名字和名字输出。
    如果你被卡住了或者想检查答案[在这里找到解决方案](https://iwillteachyoukotlin.com/programming-101-with-kotlin-chapter-1-solutions/?read-two-arguments)。
*   写一个程序`ReadMutlipleInputs.kt`，将要求用户的名字，然后要求他们的第二个名字。最后，程序应该通过名字和姓氏的组合来问候用户。
    如果你被卡住了或者想检查答案[在这里找到解决方案](https://iwillteachyoukotlin.com/programming-101-with-kotlin-chapter-1-solutions/?read-multiple-inputs)。
*   在`HelloWorld.kt`中，如果去掉:
    * `main`，
    * `fun`，
    * `println`，
    * `"Hello World"`，
    * `{`，
    * `}`，
    *第一`"`，
    *第二`"`，会发生什么？
    (对于以上每个选项)这是哪种错误？
    如果你被卡住了或者想检查答案[在这里找到解决方案](https://iwillteachyoukotlin.com/programming-101-with-kotlin-chapter-1-solutions/?breaking-hello-world)。
*   编译`HelloWorld.kt`时，如果不指定:
    `-include-runtime`会怎样？
    如果你被卡住了或者想查看答案[，在这里找到解决方案](https://iwillteachyoukotlin.com/programming-101-with-kotlin-chapter-1-solutions/?include-runtime)。
*   编写一个程序`AsciiArtHello.kt`，它将在终端中输出以下内容:

```
##     ## ######### ##        ##        #########
##     ## ##        ##        ##        ##     ##
##     ## ##        ##        ##        ##     ##
######### ######### ##        ##        ##     ##
######### ######### ##        ##        ##     ##
##     ## ##        ##        ##        ##     ##
##     ## ##        ##        ##        ##     ##
##     ## ######### ######### ######### #########
```

如果你被卡住了或者想查看答案[，在这里](https://iwillteachyoukotlin.com/programming-101-with-kotlin-chapter-1-solutions/?ascii-hello-world)找到解决方案。

*   当运行程序`ReadFirstArgument`时，如果你不像这样提供第一个参数:

```
java -jar ReadFirstArgument.jar
```

会出现什么样的错误？

如果您遇到困难或想查看答案[，请在此处找到解决方案](https://iwillteachyoukotlin.com/programming-101-with-kotlin-chapter-1-solutions/?no-first-argument)。

*   我写了下面的程序`HelloWeird.kt`:

这个程序有什么问题？如果你编译并运行它会发生什么？如何修复这个程序？

如果你被卡住了或者想要检查答案[，在这里找到解决方案](https://iwillteachyoukotlin.com/programming-101-with-kotlin-chapter-1-solutions/?hello-weird)。

# 我的相关文章

[](https://hackernoon.com/why-should-i-learn-android-14687b27e062) [## 我为什么要学安卓？

### 你已经下定决心要学习使用 Kotlin 为 Android 创建应用程序。你超级上进…

hackernoon.com](https://hackernoon.com/why-should-i-learn-android-14687b27e062)  [## 当你全职工作时，如何找到时间学习一项新技能？

### 让我猜猜…

hackernoon.com](https://hackernoon.com/how-to-find-the-time-to-learn-a-new-skill-when-you-are-working-full-time-d1957060ced0) [](https://hackernoon.com/how-kotlin-calamity-devours-your-java-apps-like-cancer-f3ce9500a028) [## Kotlin 灾难如何像闪电一样吞噬你的 Java 应用？

### 我听到你在说什么。有传言说 Android 积极采用 Kotlin 作为主要编程工具…

hackernoon.com](https://hackernoon.com/how-kotlin-calamity-devours-your-java-apps-like-cancer-f3ce9500a028)