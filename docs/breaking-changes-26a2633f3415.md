# 重大变化

> 原文：<https://medium.com/hackernoon/breaking-changes-26a2633f3415>

## 比较 JSON、ProtocolBuffers 和 FlatBuffers

![](img/b1047ba32f39416452d885bdf7e70a25.png)

变化是发展过程的必然结果。在开发过程中:新的见解出现，需求变化和特性需求出现。所有这些都有一个结果——我们需要改变数据表示。有些变化是突破性的，有些不是。当数据使用被限制在一个地方时，重大变更的后果是可管理的。当数据从一个独立的系统发送到另一个系统时，情况变得更加复杂。

## JSON 中的附加/非破坏性变化

当我们添加更多的信息而不删除信息时，对结构的改变可以被认为是附加的。假设我们有一个代表一个人的物体:

```
{"name": "Maxim", "email": "maxim@#####.com"}
```

我们给它加上一个电话号码:

```
{"name":"Maxim", "email":"maxim@#####.com", "phone":"+49#####"}
```

这是一个附加的更改，可以被认为是非破坏性的，除非您在自己的应用程序模型中将电话号码定义为必需的属性。

JSON 本身没有必需属性的概念。JSON 对象是键/值对的列表，即使列表的顺序并不重要。然而，当我们在应用程序中定义一个模型类时，我们可能坚持这个属性是必需的。我们这样做，是因为我们想要一个“干净”的应用程序逻辑。然而，这意味着附加的变化也成为突破性的变化。这意味着如果新客户端从旧客户端获取数据，它将会中断，因为旧客户端不知道电话号码属性。BTW:新客户端能够与旧客户端通信被称为**向后兼容**。

另一个额外突破性变化的例子是，如果我们的数据中有类似枚举结构的东西:

```
enum Gender { case male, female }
```

我们将案例名称翻译成字符串:

```
{"name": "Maxim", "email": "maxim@#####.com", "gender":"male"}
```

如果我们决定将更多的案例添加到性别枚举中，这是一个附加的非破坏性变化，但它可能会打破老客户。假设我们像下面这样改变性别枚举:

```
enum Gender { case male, female, other }
```

如果旧客户将收到以下对象:

```
{"name": "Maxim", "email": "maxim@#####.com", "gender":"other"}
```

旧客户端会尝试将性别属性转换为它自己的性别模型表示，在最好的情况下，它会产生一个`nil`并丢失/忽略关于性别的信息。最坏的情况下，它会崩溃试图转换一个未知的关键。顺便说一句。能够与新客户对话的老客户被称为**前向兼容性**。

## 协议缓冲区中的附加/非破坏性变化

协议缓冲区是基于模式定义的二进制数据序列化格式。因此，让我们为第一个示例定义模式:

```
syntax = "proto3";
message Person {
  string name = 1;
  string email = 2;
}
```

从这个模式中，我们可以为我们选择的语言生成数据类。我们将有一个具有`name`和`email`属性的`Person`类。如果我们给某人添加一个电话号码:

```
syntax = "proto3";
message Person {
  string name = 1;
  string email = 2;
  string phone = 3;
}
```

并再次生成，我们的`Person`类将获得另一个属性，新版本的`Person`将能够转换旧版本的`Person`创建的消息，反之亦然。**向后和向前兼容性由设计授予**。

顺便说一句。在`proto3`中，`required`关键字被删除，原因我在*JSON*部分的附加/非破坏性变化中提到过。

## enums 呢？

枚举可以定义如下:

```
enum Gender {
  MALE = 0;
  FEMALE = 1;
}
```

关于向前兼容性，枚举上的附加更改将按以下方式处理:

> 在反序列化过程中，无法识别的枚举值将保留在消息中，尽管反序列化消息时如何表示这些值取决于语言。([https://developers . Google . com/protocol-buffers/docs/proto 3 # enum](https://developers.google.com/protocol-buffers/docs/proto3#enum))

这并不完美，但与 JSON 中枚举的未定义行为相比，这是一个很大的改进。

## 平板缓冲器的附加/非破坏性变化

FlatBuffers 也是一种基于模式定义的二进制数据序列化格式。然而，它的目标略有不同。FlatBuffers 的主要好处是随机值访问，我们可以将数据存储为 DAG。

在 FlatBuffers 中，人员定义如下所示:

```
table Person {
  name: string;
  email: string;
}
```

如果我们想添加一个电话号码，我们可以定义如下:

```
table Person {
  name: string;
  email: string;
  phone: string;
}
```

FlatBuffers 提供了与协议 Buffers 相同的向后和向前兼容性保证，但是有一个小的区别。在这两种格式中，有效负载中的属性不是通过属性键存储的，而是通过其索引/id 存储的。在协议缓冲区中，我们必须明确设置 id`string phone = 3;`。在平板缓冲器中是不需要的。这意味着 id 基于属性索引。因此，如果我们将`Person`定义如下:

```
table Person {
  name: string;
  phone: string;
  email: string;
}
```

我们会引入一个突破性的改变。你可能会说有这样脆弱的隐含约定是个坏主意。你完全正确。这就是为什么在 FlatBuffers 中，我们可以向属性添加一个显式属性，定义它的 id:

```
table Person {
  name: string (id:0);
  phone: string (id:2);
  email: string (id:1);
}
```

现在我们的新人又和老人合得来了。

## 平板缓冲器结构

在 FlatBuffers 中，我们也可以定义结构。结构是固定大小的值类型。我们不能把一个人表示成一个结构体，person 有字符串属性，而字符串的大小是不固定的。然而，我认为重要是要提到结构是固定的，不支持进化。因此，我建议只对简单的事情使用结构，这些事情由标准定义，在下一个版本中不会改变，例如 IPv4 地址。

## enums 呢？

FlatBuffers 中的枚举定义如下所示:

```
enum Gender : byte { male, female }
```

它们比协议缓冲区更加严格。它们都被存储为数字，但是在协议缓冲区的情况下，数字是一个 [VLQ](https://en.wikipedia.org/wiki/Variable-length_quantity) ，所以我们不需要关心大小。在 FlatBuffers 中，我们必须定义大小——在我们的例子中是 1 字节(byte ),如果事例的数量超过 127，这也可能成为一个不太可能但仍然可能的突破性变化。

在 FlatBuffers 的情况下，未知的情况(向前兼容)也受到特定语言实现的支配。

## 破坏性更改—删除属性

假设我们有一个删除`email`属性的新需求。

对于 JSON 来说，这没什么大不了的，除非我们根据需要定义了`email`。如前所述，就数据进化而言，`required`不是一个好主意。否则，如果一个新的客户端接收到一个包含`email`属性的 JSON，它将会忽略它。

对于协议缓冲区和平面缓冲区，可以将属性设置为`deprecated`。这意味着您将无法在新版本的`Person`类中设置该属性。

在协议缓冲区中，弃用是零成本的，因为消息只包含存储的值。在 FlatBuffers 中，由于随机值访问，存储的对象有所谓的 vTables，它指向值。如果某个值被弃用，它仍会出现在 vTable 中。这意味着每个 vTable 的废弃仍然会花费我们 2 个字节(vTable 可以在对象之间重用，但是我不会在本文中详细讨论它)。

proto3 中另一个很好的补充是，我们甚至不必在模式中保留不赞成使用的属性的属性定义，我们可以使用带有 id 的关键字`reserved`来确保这个特定的 id 不会在新版本中使用。

事实上，属性名对于平面缓冲区和协议缓冲区并不重要。因为属性是通过 id 而不是名称来存储和查找的。因此，我们还可以重命名不推荐使用的属性:

```
table Person {
  name: string;
  __phone: string (deprecated);
  email: string;
}
```

## 重命名属性名称

*计算机科学只有两个硬东西:缓存失效和事物命名。*

*—菲尔·卡尔顿*

假设我们意识到`name`不是对属性的准确描述，我们应该将其改为`fullName`或`full_name`。这是 JSON 的一个突破性变化。老客户会向 person 对象询问“姓名”。因此，如果一个新客户端开始发送“全名”,双方将无法再进行通信。

如前所述，FlatBuffers 和 Protocol Buffers 不按名称存储属性，而是按 id/index 存储属性。只有生成的代码反映了我们放入模式中的名称。下面是一个简化的示例，说明了生成的代码的作用:

```
var name: String {
  return self[0] as! String
}
```

因此，更改模式中的属性名称会影响代码，但不会影响底层数据表示。

```
var fullName: String {
  return self[0] as! String
}
```

这意味着重命名属性和枚举用例并不是 FlatBuffers 和协议 Buffers 中的突破性变化。澄清一下——重命名枚举事例并不是 FlatBuffers 和协议 Buffers 中的一个突破性变化，因为事例在内部也存储为 int 而不是 strings。

## 更改属性类型

有时，我们意识到我们为物业选择的类型是不够的。假设我们想要保留属性`name`，但是它现在应该存储一个包含名字和姓氏的对象:

```
{"name": {"firstName" : "Maxim", "lastName": "Zaks"}}
```

这将是 JSON、FlatBuffers 和协议缓冲区的突破性变化。

然而，在 FlatBuffers 和 ProtocolBuffers 中，我们可以执行以下技巧。我们可以废弃旧的`name`属性，将其重命名为`oldName`，并引入一个类型为`FullName`的新属性`name`。

```
table Person {
  oldName: string (deprecated);
  email: string;
  name: FullName;
}
```

这是一系列附加的/非破坏性的变化，提供了数据表示的清晰演变。

## 结论

变化是不可避免的，但是用正确的工具和一点创造力，是有可能让它不被打破的。

感谢您的阅读，我很欣赏鼓掌或两个。