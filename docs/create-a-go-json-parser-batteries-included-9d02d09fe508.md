# 创建一个 Go Json 解析器:包括电池

> 原文：<https://medium.com/hackernoon/create-a-go-json-parser-batteries-included-9d02d09fe508>

![](img/e46320af7ca33a05254c96f8927a8779.png)

这篇文章的灵感来自于工作中的一个项目。我正在构建一个需要比较两个 Json 对象的服务。问题是我需要能够**替换键**，**过滤出路径**，并且**将比较函数**应用到特定的节点。

显然，像`reflect.DeepEqual()`这样的标准库比较函数是行不通的。😧

解决方案是构建一个 **AST** ( *抽象语法树*)模仿 **Json** 对象。树中的每个`Node`代表一个`string`、`integer`、`array`或`object`。

通过这样做，我可以灵活地将[算法](https://hackernoon.com/tagged/algorithms)应用到数据上。

为了构建这个，我们将从生成**令牌**的**词法分析器**开始。然后转移到**解析器**上，解析器将获取令牌并将它们与 **Json** 语法进行匹配。最后，我们将添加 **AST** 钩子来生成树。

最终的目录结构:

```
.
main.go
/lexer
    lexer.go
    lexer_test.go
/token
    token.go
/parser
    parser.go
/ast
    ast.go
```

如果您想查看和运行最终结果:

```
cd $GOPATH/src/github.com/Lebonesco
git clone [https://github.com/Lebonesco/json_parser.git](https://github.com/Lebonesco/json_parser.git)
go run main.go ./examples/test.json
```

# 词法分析程序

lexer 的工作是接收 [json](https://hackernoon.com/tagged/json) 数据，并将其转换成`tokens`流。这些令牌包括:`INVALID`、`EOF`、`COMMA`、`COLON`、`LBRACE`、`RBRACE`、`LBRACKET`、`RBRACKET`、`STRING`和`INTEGER`。

**注:**lexer 也称为扫描仪。

让我们从下面开始👇

```
cd $GOPATH/src/github.com/Lebonesco/json_parser
mkdir token
cd token
touch token.go
```

在如何定义自己的`tokens`方面，你有一些自由。添加到`token`中的数据越多，调试就越容易。

注意:我们将使用一个`rune`数组`[]rune`来存储我们的`token`文字，以支持 **Unicode** 字符。

接下来，让我们跳进我们的`lexer`👍

```
mkdir lexer
cd lexer
touch lexer.go
touch lexer_test.go
```

`lexer`将跟踪我们在输入中的位置，必要的字符向前看。

就功能而言，它需要能够创建一个新的`token`，并在下一个之前达到峰值。

**注意:**该扫描仪不支持**布尔**值，但可以轻松添加。

## 莱克斯试验

这里我们将接受一个 j **son** 字符串，并确保它输出正确的令牌流。

要运行测试:

```
go test -v
=== RUN   TestLexer
--- PASS: TestLexer (0.00s)
PASS
ok      github.com/Lebonesco/json_parser/lexer  0.433s
```

你现在有了一个可以工作的 lexer🎉 🎉 🎉

# 句法分析程序

这是我们获取流并将其与 **json 语法**匹配以产生 **AST** 节点的部分。

如果我们用正则表达式来定义 json，它将由下面定义的语法来表示👇

```
JSON : value
Value :  Object | Array | String | Integer | Bool | Null
Array : '[' [Value] {',' Value}']'
Object : '{' [Property] {',' Property} '}'
Property : String ':' Value
```

在上面的语法中，`[expression]`表示表达式出现一次或多次，`{expression}`表示出现零次或多次。

如果你提供正则表达式，有像`gocc`这样的工具可以生成词法分析器和/或语法分析器。如果您正在处理更复杂的事情，这是推荐的方法。

但是由于 **Json** 相当简单，我们就手工做吧！👐

让我们构造一个 AST `nodes`来表示我们的最终结果。

```
mkdir ast
cd ast
touch ast.go
```

`nodes`相当简单。如果我们更关心错误处理和跟踪`node`散列，就像我的用例，我们可以存储更多的数据。

**注意:**因为 Go 使用`composition`而不是`inheritance`，我们需要将`TokenLiteral()`方法应用于每个`node`类型，以便将每个类型解释为一个`Json`节点。

现在来看解析器！

让我们把这些都集中起来，写我们的驱动，`main.go`。

**注意:** `json.MarshalIndent()`是`json.Marshal()`的一个很好的替代品，可以得到更漂亮的 **json** 输出。

要运行:

```
go run main.go ./exampes/test.json
```

全部完成😄

现在我们可以生成一个 **AST，**很容易向每个节点添加一个滚动散列，并执行所有其他定制操作，例如:

*   **替换节点值**
*   **过滤掉节点**
*   **对节点应用特殊的比较函数**

我将把这些扩展留给读者。欢迎在评论中留下你作品的链接。我很想看看你能想出什么。👍

感谢您花时间阅读这篇文章。

如果你觉得它有帮助或有趣，请让我知道👏👏👏。