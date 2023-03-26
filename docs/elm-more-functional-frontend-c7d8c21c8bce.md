# Elm:功能前端！

> 原文：<https://medium.com/hackernoon/elm-more-functional-frontend-c7d8c21c8bce>

![](img/2a92fd2482d05f6ffc7ca0e08739f7ee.png)

我们的 [Haskell Web 系列](https://www.mmhaskell.com/haskell-web)涵盖了许多在制作 Web 应用程序时可以使用的酷库。但是有一件事我们在这个博客上还没有涉及，那就是使用 Haskell 进行前端 web 开发。有许多库和框架。 [Yesod](https://www.yesodweb.com/) 和 [Snap](http://snapframework.com/) 浮现脑海。另一种选择是[反射玻璃钢](https://github.com/reflex-frp/reflex)，它在引擎盖下使用 GHCJS。

但是对于这个新系列，我决定采取不同的方法。接下来的几周，我们将关注榆树！

我喜欢榆树有几个原因。Elm 建立在我的坚定信念之上，即我们可以将函数式编程的原则付诸实践。语言是严肃的，文档也很好。Elm 有一些语法上的怪癖。它还缺少一些 Haskell 的关键特性。然而，我们仍然可以用它做很多事情。

本周我们将了解基本安装、区别和用法。下周，我们将在 Elm 中编写一个简单的 Todo 应用程序。这将让我们感受到我们是如何设计 Elm 应用程序的。最后，我们将探索如何为我们的应用程序添加更多效果，以及如何将 Elm 类型与 Haskell 集成。

当然，前端只是故事的一部分。要了解更多关于使用 Haskell 进行后端 web 的信息，请查看我们的 Haskell Web 系列[！您也可以下载我们的](https://www.mmhaskell.com/haskell-web)[生产清单](https://www.mmhaskell.com/production-checklist)了解更多创意！

# 基本设置

和任何语言一样，第一次在我们的机器上使用 Elm 时会有一些设置。对于 Windows 和 Mac，你可以运行这里提供的安装程序。Linux 有单独的指令，但是它们足够简单。您获取二进制文件，`tar`它，并移动到您的`bin`。

一旦我们安装了`elm`可执行文件，我们就可以开始了。当您使用了足够多的包管理程序后，这个过程就变得更容易理解了。`elm`命令与`stack`和`npm`有一些基本的共同点。

首先，我们可以运行`elm init`来创建一个新项目。这将为我们创建一个`src`文件夹和一个`elm.json`文件。这个 JSON 文件类似于 Node.js 的`.cabal`文件或`package.json`,在这里我们将指定所有不同的包依赖关系。这个的默认版本将提供您的大多数基本 web 包。然后我们将在`/src`中制作我们的`.elm`源文件。

# 运行基本页面

Elm 开发看起来不同于我使用过的大多数普通 Javascript 系统。当我们编写代码时，我们不需要指定应用程序的特定入口点。我们制作的每个文件都是我们可以查看的潜在网页。因此，我们可以从最简单的应用开始:

```
import Browser
import HTML exposing (Html, div, text)type Message = Messagemain : Program () Int Message
main =
  Browser.sandbox { init = 0, update = update, view = view }update : Message -> Int -> Int
update _ x = xview : Int -> Html Message
view _ = div [] [text "Hello World!"]
```

Elm 使用模型/视图/控制器系统。我们在`main`函数中定义我们的程序。我们的`Program`型有三个参数。第一个与我们可以传递给程序的标志有关。我们暂时忽略这些。第二个是我们程序的模型类型。我们从一个简单的整数开始。最后一种类型是消息。我们的视图将通过发送这种类型的消息来引起更新。`sandbox`函数意味着我们的程序很简单，并且没有副作用。除了传递初始状态，我们还传递了一个`update`函数和一个`view`函数。

`update`功能允许我们获取新消息，并在必要时更改我们的模型。然后`view`是一个接受我们的模型并决定 HTML 组件的函数。您可以将`view`的类型理解为“发送`Message`类型消息的 HTML 组件”。

我们可以运行`elm-reactor`命令，将浏览器指向`localhost:8000`。这将我们带到一个仪表板，在那里我们可以检查任何我们想要的文件。我们只想看看有`main`功能的那些。然后，我们将在屏幕上看到带有 div 的简单页面。(如果我们选择一个纯库文件，它会奇怪地旋转)。

按照 [Elm 教程](https://guide.elm-lang.org/)的说法，我们可以通过在模型中使用`Int`来让它变得更有趣。我们将改变我们的`Message`类型，这样它既可以代表`Increment`也可以代表`Decrement`。然后我们的更新函数将根据消息改变模型。

```
type Message = Increment | Decrementupdate : Message -> Int -> Int
update msg model = case msg of
  Increment -> model + 1
  Decrement -> model - 1view : Int -> Html Message
view model = div [] [String.fromInt model]
```

作为最后的改变，我们将在界面上添加`+`和`-`按钮。这将允许我们向我们的类型发送`Increment`和`Decrement`消息。

```
view model = div []
  [ button [onClick Decrement] [text "-"]
  , div [] [ text (String.fromInt model) ]
  , button [onClick Increment] [text "+"]
  ]
```

现在我们有一个界面，我们可以按下每个按钮，屏幕上的数字会改变！这是我们的基本应用！

# Make 命令

`elm reactor`命令构建了一个虚拟界面，供我们使用和检查页面。但是我们的最终目标是让它能够从我们的 elm 代码中生成 HTML 和 Javascript。然后，我们将导出这些资产，以便我们的后端可以将它们作为资源。我们可以用`elm make`命令来完成。这里有一个例子:

```
elm make Main.elm --output=main.html
```

我们希望使用脚本将所有这些元素放在一起，并将其转储到 assets 文件夹中。几周后，当我们完成一个完整的 Elm + Haskell 项目时，我们将会在这方面获得一些经验。

# 与 Haskell 的区别

在比较 Elm 和 Haskell 时，有一些语法上的问题。我们不会涵盖所有这些，但这里有一些基本的。

我们已经可以看到导入和模块语法有一点不同。我们在导入定义中使用`exposing`关键字来从该模块中挑选出我们想要的特定表达式。

```
import HTML exposing (Html, div, text)import Types exposing (Message(..))
```

当我们定义自己的模块时，我们也将使用`exposing`关键字来代替模块定义中的`where`:

```
module Types exposing
  (Message(..))type Message = Increment | Decrement
```

我们还可以看到，Elm 在 Haskell 中使用`data`的地方使用了`type`。如果我们想要一个类型同义词，Elm 提供了`type alias`组合:

```
type alias Count = Int
```

从上面的类型操作符可以看出，Elm 反转了`:`操作符和`::`。单个冒号指的是类型签名。双冒号表示列表附加:

```
myNumber : Int
myNumber = 5myList : [Int]
myList = 5 :: [2, 3]
```

Elm 还缺少 Haskell 的一些更好的语法元素。例如，Elm 在功能和防护上缺乏模式匹配。Elm 也没有`where`子句。只有`case`和`let`语句存在。对于函数组合，您可以使用`<<`而不是`.`操作符。data-preserve-html-node="true "以下是这些要点的几个示例:

```
isBigNumber : Int -> Bool
isBigNumber x = let forComparison = 5 in x > forComparisonfindSmallNumbers : List Int -> List Int
findSmallNumbers numbers = List.filter (not << isBigNumber) numbers
```

作为本节的最后一点，Elm 是严格评估的。Elm 编译成 Javascript，因此可以在浏览器中运行。而且用严格的语言生成合理的 Javascript 要容易得多。

# 榆树唱片公司

Elm 的另一个关键区别是记录语法的工作方式。它榆树，一个“记录”是一个特定的类型。这些模拟 Javascript 对象。在这个例子中，我们为一个记录定义了一个类型同义词。虽然我们通常没有模式匹配，但我们可以对记录使用模式匹配:

```
type alias Point2D =
  { x: Float
  , y: Float
  }sumOfPoint : Point2D -> Float
sumOfPoint {x, y} = x + y
```

为了让我们的代码更像 Javascript，我们可以使用`.`操作符以不同的方式访问记录。我们可以使用类似 Javascript 的语法，或者像普通函数一样使用句点和字段名。

```
point1 : Point2D
point1 = {x = 5.0, y = 6.0}p1x : Float
p1x = point1.xp1y : Float
p1y = .y point1
```

我们还可以轻松地更新记录的特定字段。这种方法适用于许多领域:

```
newPoint : Point2D
newPoint = { point1 | y = 3.0 }
```

# 类型类和单子

Haskell 和 Elm 之间更有争议的区别在于这两个概念。Elm 没有 typeclasses。对于像我这样的 Haskell 老手来说，这是一个很大的限制。正因为如此，Elm 也缺少`do`语法。记住`do`语法依赖于`Monad`类型类存在的想法。

这些遗漏是有原因的。榆树的创造者写了一篇关于它的有趣的文章。

他的主要观点是(不像我)，大多数 Elm 用户来自 Javascript 而不是 Haskell。他们往往没有多少函数式编程和相关概念的背景知识。所以对于 Elm 来说，获取这些结构并不是一个优先考虑的事情。那么有什么替代方案呢？

说到类型类，每种类型都有自己的函数定义。就拿`map`这个简单的例子来说吧。在 Haskell 中，我们有`fmap`函数。它允许我们在不知道容器是什么的情况下对容器应用函数:

```
fmap :: (Functor f) => (a -> b) -> f a -> f b
```

无论我们有一个列表还是一个字典，我们都可以应用这个函数。但是在 Elm 中，每个库都有自己的`map`函数。所以我们必须限定它的用法:

```
import List
import Dictdouble : List Int -> List Int
double l = List.map (* 2) ldoubleDict : Dict String Int -> Dict String Int
doubleDict d = Dict.map (* 2) d
```

Elm 使用了一个名为`andThen`的函数，而不是单子。这很像 Haskell 的`>>=`操作符。我们在 Java 等面向对象语言中更经常看到这种模式。作为文档中的一个例子，我们可以看到这如何与`Maybe`一起工作。

```
toInt : String -> Maybe InttoValidMonth : Int -> Maybe Int
toValidMonth month =
    if month >= 1 && month <= 12
        then Just month
        else NothingtoMonth : String -> Maybe Int
toMonth rawString =
    toInt rawString `andThen` toValidMonth
```

所以 Elm 并没有给我们 Haskell 中那么多的功能。也就是说，Elm 首先是一个前端语言。它表达了如何显示我们的数据，以及我们如何将组件组合在一起。如果我们需要复杂的功能元素，我们可以使用 Haskell 并将其放在后端。

# 结论

我们暂时就此打住。下周我们将通过编写一个更复杂的程序来扩展我们对 Elm 的理解。我们将编写一个简单的待办事项列表应用程序，并看看 Elm 的架构在运行。

要想听到更多来自周一早晨哈斯克尔的消息，请确保[为](https://www.mmhaskell.com/subscribe)订阅我们的时事通讯！这也将让您访问我们令人敬畏的[资源](https://www.mmhaskell.com/resources)页面！