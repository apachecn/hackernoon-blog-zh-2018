# 用 sed 做一个更快乐的程序员

> 原文：<https://medium.com/hackernoon/be-a-happier-programmer-with-sed-cc540e0b58d3>

好吧，我承认这个标题有点争议。然而，在这个经典的命令行工具为我节省了几个小时的重复工作之后，我觉得有必要说一下。

## 理想的用例

在早期的 TypeScript 中，字符串枚举是不存在的。然而，有几种不同的解决方法可以实现类似的目标。我的公司在内部使用的变体包括创建一个具有许多返回字符串值的静态 getters 的类。举个例子，

```
export class BillingMethod {
  static get Cash() { return 'cash'; }
  static get Check() { return 'check'; }
  static get CreditCard() { return 'credit card'; }
}// Usage:
const billingMethod = BillingMethod.Cash;
```

这个方法提供了传统枚举的大部分好处，但是它错过了枚举的最大优势:为数据结构提供适当的类型支持。上述每个 getter 函数都返回一个字符串，而不是一个自定义类型。

当 TypeScript 2.4 宣布[字符串枚举支持](https://blogs.msdn.microsoft.com/typescript/2017/06/27/announcing-typescript-2-4/)时，很明显重构对于在我们的应用程序中实现正确的类型是必要的。但是我们如何将数百个导出的类替换为它们的枚举对应类呢？

答案当然是 sed。通过一个简单的脚本，我们可以将原始的、模仿的 enum 变成真实的 enum:

```
sed \
 -i \
 -e 's/class/enum/' \
 -e 's/static get //' \
 -e 's/() { return/ =/' \
 -e 's/; }/,/' \
 ./billing-method.ts
```

产出:

```
export enum BillingMethod {
  Cash = 'cash',
  Check = 'check',
  CreditCard = 'credit card',
}
```

通过将目的地更改为`./constants/*.ts`将脚本应用于 TypeScript 文件的文件夹，我们已经成功地重构了应用程序中的每个枚举。

## 分解它

如果我们只是快速浏览一下，Sed 的语法看起来难以理解。然而，只需要大约十五分钟的实验，就可以把它变成最强大的重构工具之一。

以下是上述程序中使用的关键词的概要:

*   `sed`:可执行程序的名称(流编辑器的简称)。
*   `\`:换行字符。上面的脚本可以全部写在一行中——用反斜杠将它分开可以提供更好的可读性。
*   `-i`:“在文件中”的简称。如果提供了这个标志，sed 将用您的编辑替换它所解析的文件的内容。通过关闭此选项，我们可以在编辑实际影响生产代码之前查看它的“预览”。
*   `-e`:“表达式”的简称，提供了我们的编辑操作。因为我们想要多次编辑同一个文件，所以我们使用`-e`来提供多次编辑。
*   `'s/class/enum/'`:编辑的肉，传入`-e`标志。解释为:`'s/<replace this content>/<with this content>/'`。请注意，您可以将内容指定为纯文本(正如我在这里所做的那样)，也可以指定为正则表达式。
*   `./billing-method.ts`:我们正在编辑的文件。这也可以是一个目录。例如，`./constants/*.ts`编辑`constants/`目录中的所有类型脚本文件。

现在让我们来看看每个表情:

*   `‘s/class/enum/’`:将`class`替换为`enum`
*   `‘s/static get //’`:移除`static get`
*   `‘s/() { return/ =/’`:将`() { return`更换为`=`(间隔适当)
*   `'s/; }/,/'`:将`; }`替换为`,`

当 sed 的语法被分解时，它既简单又富于表现力。尽管这是一个简单的用例，但它可以应用于各种各样的代码重构。

我希望这个工具能为你节省和我一样多的时间。

## 进一步阅读

*   [GNU 手册](https://www.gnu.org/software/sed/manual/sed.html)
*   [互动 sed 细分](https://explainshell.com/explain?cmd=sed+-i+-e+%E2%80%98s%2Fclass%2Fenum%2F%E2%80%99+.%2Fbilling-method.ts)