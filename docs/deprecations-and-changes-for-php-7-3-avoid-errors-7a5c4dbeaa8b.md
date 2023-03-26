# PHP 7.3 的弃用和变更[避免错误]

> 原文：<https://medium.com/hackernoon/deprecations-and-changes-for-php-7-3-avoid-errors-7a5c4dbeaa8b>

![](img/65ac23d3e513421b28c59bce23120af8.png)

随着 [PHP](https://hackernoon.com/tagged/php) 7.3 的发布，你准备好迎接新的变化了吗？

在这篇博文中，我将指导你完成更新代码的**过程**，这样你就可以避免错误并尽可能顺利地工作。

# 介绍

我们最喜欢的[编程语言](https://hackernoon.com/tagged/programming)的新版本 PHP 7.3 的官方发布日期已经不多了，

作为一名 Web 开发人员，我既兴奋又担忧。

如果你阅读了我发表的另一篇文章，你就会知道，在新版本中，新的特性已经被添加，这将有助于工作并提高代码的质量，但同时，当你是最新的时，你必须**关注那些不再兼容的特性，这些特性会给已经存在的代码带来问题**。

在这篇文章中，

我分享的正是您开始成功迁移应用程序所需了解的内容，

包括:

**删除所有不推荐使用的功能**，如果保留这些功能会导致错误，而**会让您了解新的改进**，比如散列密码和创建常数的新系统

听起来像个计划吗？让我们开始吧！

# 跟随系列…

这篇博文是“ [**PHP 7.3 及其圣诞礼物**](https://amzn.to/2OfCZej)**”**的第三部分

如果你还没有阅读其他部分

你可以点击下面的链接查看其他博客文章
[PHP 7.3 简介和新语法](http://anastasionico.uk/blog/php-73)
[PHP 7.3 的新特性](http://anastasionico.uk/blog/php-7-3-new-features)
[弃用和变化](http://anastasionico.uk/blog/php-7-3-deprecations-changes)

# 贬值

![](img/d4de2974e19c5eef5f657f63c92c2fe3.png)

# 正在删除 image2wbmp()

让我们从分解这个函数的名字开始！

图像— 2 — wbmp()

我必须说这个函数的目的很简单。

**Wbmp 格式也称为无线位图，是针对移动设备优化的单色图像。**

```
bool imagewbmp(resource $image [, mixed $to [, int $foreground]]);
```

该函数接受 3 个参数(资源、带有保存文件路径的字符串和整数格式的前景色),并根据所提供图像的 wbmp 版本的保存输出返回一个布尔值。

这个函数之所以被弃用，并且在 PHP 的未来版本中肯定会被删除，是因为已经有一个函数具有完全相同的功能。

它是 imagewbmp()，

从快速研究 GitHub [*imagewbmp*](https://github.com/search?l=PHP&q=imagewbmp&type=Code) 和[*image2 BMP*](https://github.com/search?l=PHP&q=image2wbmp&type=Code)。

你会看到 imagewbmp()的使用率要高得多，所以创建者所做的只是消除了较少使用的。

从 PHP 的下一个版本开始，对 image2wbmp()的每次调用都会抛出一个弃用警告，而在接下来的版本中，当它肯定会被删除时，它会抛出一个致命错误。

为了更新您的脚本，如果您正在使用 image2wbmp()，您需要做的只是用 imagewbmp()替换它，

代码会做同样的事情，并且不会有警告或错误。

# 已弃用 FILTER_FLAG_SCHEME_REQUIRED 和 FILTER_FLAG_HOST_REQUIRED

这两个标志属于 filter_var()函数。

**filter_var()使用 web 开发者指定的标志过滤变量。**

```
mixed filter_var(mixed $variable [, int $filter = FILTER_DEFAULT [, mixed $options ]]);
```

**第一个参数是需要过滤或验证的变量，**

**第二个参数称为 flag，包含需要应用的过滤器的 ID。**

这里列出了 PHP 中所有可用的 [*过滤器类型。*](https://secure.php.net/manual/en/filter.filters.php)

**第三个参数 its 是强制的，需要以数组或标志位析取的形式出现。**

如果您不熟悉方括号，它们的意思是里面的参数不是强制的，您可以避免插入它们，以确保不会出现任何错误。

对于 filter 参数，原因是默认情况下 PHP 将使用 FILTER_DEFAULT。

这是 PHP 7.3 的变化。

从 PHP 5.2.1 开始，这两个标志被包含在 FILTER_VALIDATE_URL 中，即使你没有指定它们。

总而言之，

**这两个常量是无用的**，正如 RFC 中所写的，它们已经造成了错误的印象，即方案和主机都可以被禁用。

如果你有使用这两个标志的代码，把它们移除就可以了。

# 弃用不区分大小写的常量

目前，PHP 7.2 版本支持区分大小写和不区分大小写的常量。

这些字母不经常使用，但使用时会导致不一致。

```
bool define(string $name , mixed $value [, bool $case_insensitive = FALSE ]);
```

函数的最后一个参数是一个布尔值，如果设置为 TRUE，常量将被定义为不区分大小写。

也就是说圣诞老人和圣诞老人会代表不同的价值观。

如其名，

**常量必须是常量**，也就是说声明后不能更改。

对于不区分大小写的常量，此规则不成立。

```
define('SANTA', 42);
var_dump(SANTA); // int(42)
define('SANTA', 24); // Notice: Constant FOO already defined
var_dump(SANTA); // int(42)
```

如果区分大小写和不区分大小写的常量混合使用:

```
define('santa', 42, true);
var_dump(SANTA); // int(42)
define('SANTA', 24);
var_dump(SANTA); // int(24)
```

前面的代码实际上改变了已定义常量的值，这不是我们想要的行为。

PHP 7.3 中会有各种不同:

*   将第三个参数定义为 TRUE 的可能性已被否决，并将在 8.0.0 版中明确删除；
*   不可能再用不同于 define 函数中使用的格式来访问不区分大小写的常量。

```
define('Santa', 'Claus', true);
```

前面的代码会抛出“*已弃用:define():不区分大小写的常量声明已弃用*”，

如果您尝试使用另一种大小写来访问刚刚定义的常量，将会导致“不推荐使用:不区分大小写的常量不推荐使用。

该常量的正确大小写是“Santa”

与其他更新不同，

要修复代码中所有可能出现的问题，您需要参考一些资源。

**你一定在寻找所有不区分大小写的常量。**

好消息是，你必须直接声明常量，只需检查第三个参数设置为真的 define function。

# 变化

# PCRE 到 PCRE2 迁移

[*Perl 兼容正则表达式*](http://www.pcre.org/pcre.txt) ，也叫 PCRE，是 PHP 用来处理正则表达式的库。

**PCRE 由一组函数组合而成，这些函数实现了与 Perl 5 相同的语法和语义相匹配的正则表达式模式，**

它不能再被禁用了。

当您意识到 PHP 仍然使用 1.0 版的未被利用的库时，问题就来了，

与此同时，新版本 PCRE2 于 2005 年发布。

这主要是 PHP 的核心功能，因此在大多数情况下，它不会影响该语言的用户，但你需要注意一些可能影响你当前使用 PHP 的修改。

以下是一些不同之处

*   默认情况下，修改器 S 现在是打开的。PCRE 做了一些额外的优化。
*   默认情况下，选项 X 是禁用的。它使 PCRE 比以前做更多的语法验证。
*   使用的是 Unicode 10，而它是 Unicode 7。这意味着更多的表情符号，更多的字符，和更多的集合。Unicode 正则表达式可能会受到影响。
*   一些无效模式可能会受到影响。

简单来说，

PSRE2 在模式验证方面更加严格，因此在升级之后，一些现有的模式无法再编译。

以下是在 php.net 使用的简单片段

```
preg_match('/[\w-.]+/', ''); // this will not work in PHP7.3
preg_match('/[\w\-.]+/', ''); // the hyphen need to be escaped
```

正如你从上面的例子中看到的，这两行之间有一点点但是实质性的不同。

**现在需要对连字符进行转义，**

这可能是你会遇到的最常见的不兼容原因之一。

此外，PHP 在林挺时间期间不 lint regex，

建议检查你在应用程序中使用的所有正则表达式，并用 PHP 7.3 测试它们。

Exakat.io 提供了一个 [*完整的 regex 清单*](https://phpa.me/exakat-regex-ci)

实现这个新特性可能比之前看到的其他特性更有问题，

事实上，一些对 *preg_match()* 的调用可能会停止工作。

在把你的 PHP 升级到 7.3 版本之前，我建议检查一下，如果需要的话，更新或者重写你需要的模式。

还有，

运行单元测试以查找编译期间的错误。

# compact()中未定义的变量

compact()是一个神奇的数组函数，它允许通过使用一个或多个变量的名称作为参数来创建数组。

```
$name  = "Claus";$title = "Santa";$residency = "North Pole";$result = compact("name", "title", "residency", "reindeer");Array[ [name] => Claus [title] => Santa [residency] => North Pole];
```

在前面的示例中，变量 *$result* 是一个数组，包含以键的形式选择的变量的名称和以值的形式选择的变量的值。

在过去的十年中，由于 PHP 框架的指数级使用，以及后者在 MVC 模型上使用模板引擎的事实，

这个功能的使用已经增加了很多。

**在 PHP 5.3 版本之前，该方法提供的一个特性是，如果我们插入一个引用不存在的变量的字符串，脚本将简单地忽略该参数。**

从长远来看，特别是对于注意力分散的 web 开发人员，这通常会导致视图中缺少变量(在 MVC 的情况下)或缺少数据。

有了出色的 32 vs 5，最后，在安装了新版本的编程语言之后，compact()将开始用下面的通知来报告这些丢失的变量。

```
// Notice: compact(): Undefined variable: reindeer
```

请小心，因为当更新您当前的项目时，您可能会遇到大量刚才提到的通知。

如果修复所有实例导致压倒性的结果，一个好主意可能是通过使用 *@操作符*并一个接一个地修复案例来回到旧的情况。

# Argon2 密码哈希增强

开发人员总是致力于增强安全性和密码哈希，这是一件好事。

![](img/26924bd4ba21359821d6dc3d78c4cc3e.png)

努力实现更好的保护级别至关重要，尤其是在讨论我们的数据时。

PHP 7.2 实现了一种叫做 Argon2 的新散列算法，这是 Bcrypt 的替代方案。

Argon2 有三种不同的版本

它们是:

*   Argon2d 是其中最快的，它使用数据依赖的内存访问，最大限度地抵抗 GPU 攻击。
*   Argon2i 则使用与数据无关的内存访问，这是密码哈希的首选方法，但速度较慢，因为为了防止攻击，会对内存进行哈希处理
*   然后我们有 Argon2id，它是结合了其他两个版本的版本

在 PHP7.3 中，您可以添加常量 PASSWORD_ARGON2ID 作为 *password_hash()* 函数的标志。

```
password_hash('password', PASSWORD_ARGON2ID);
```

关于这种新方法有几个细节，如并行度因子、内存成本和时间成本计算，但我会将它们保存下来，并简单地向您展示在您想要选择这种类型的哈希时可以使用的语法。

```
$options = ['memory_cost' => 1<<11, 'time_cost' => 4, 'threads' => 2];
password_hash('password', PASSWORD_ARGON2ID, $options);
```

接受这个新算法的另一个函数是 *password_needs_rehash()* ，它检查提供的散列是否实现了算法和提供的选项，并相应地返回一个布尔值。

示例:

```
$hash = password_hash('password', PASSWORD_ARGON2ID);
password_needs_rehash($hash, PASSWORD_ARGON2ID, ['memory_cost' => 1<<17]);
```

# 结论

在这篇文章中，

我已经提供了所有相关变化和改进的深度摘要，

如果一切按计划进行，再过几天这些改动就会正式发布(PHP 7.3 预计在 12 月 13 日发布)。

与此同时，你正在等待这些更新(这可能会令人沮丧，我现在)有几个权宜之计，你可以申请，以便为他们做好准备。

首先，

**你可以更新你的代码，检查并消除与新版本的所有不兼容之处，**

你可以在每一个新特性的每一部分看到是否有任何向后兼容的问题，一个好主意是把这篇文章的所有部分都扔掉(我提醒你，它分为 3 个主要部分),看看它们在你的代码中是否受支持。

一个例子是获取代码中的所有正则表达式，并用新的 PCRE2 林挺它们。

你可以为 PHP 7.3 使用**poly fill**

此外，运行 Exakat 并查找问题。

最终，

当然，**运行你的单元测试。**

如果您的应用程序中没有任何测试，并且您没有使用自动化测试，

编写测试，然后运行它们！。

最后一点:RFC 上 PHP 7.3 提案的完整列表和 GitHub 的 PHP 7.3 注释可以在这些链接上找到。

[![](img/81f240e8dd073aab6ad31da0e0d28d60.png)](http://eepurl.com/dIZqjf)

回到我的圣诞节，

奇怪的是，当你还是个孩子的时候，偷偷溜到圣诞树下，希望得到一双新靴子，在下一场足球比赛中炫耀，或者在试图杀死老板的最后一场视频游戏中让你彻夜难眠。

而现在，你要等到 12 月份才能在你最喜欢的编程语言上获得新功能。

但是你知道。

作为一个成年人，

我相信你明白，学习这些东西会让我们的工作更轻松，减少办公室内外的压力，这意味着与你所爱的人共度更幸福的生活，

这才是真正独特的圣诞节精神。

所以，

尽管还不是时候*圣诞快乐，新法典快乐。*

![](img/bd9c1a1abd55e6b8e4a6c7a83826e162.png)

*这篇博文是 Kindle book“*[*PHP 7.3 及其圣诞礼物*](https://amzn.to/2PpbxeS)*”*的一段

*点击提供的链接获取完整版本*