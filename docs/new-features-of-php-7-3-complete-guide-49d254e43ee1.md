# PHP 7.3 的新特性[完整指南]

> 原文：<https://medium.com/hackernoon/new-features-of-php-7-3-complete-guide-49d254e43ee1>

![](img/c6e015b0e35c83cd7298b57e9fe61bdb.png)

# 介绍

几天前，

我的一个同事给我看了几年前在电视上播出的一个电视广告。

这个地方属于一家著名的英国连锁超市，讲述了一个真实发生在第一次世界大战圣诞节的故事。

经过几个月的冲突，英国和德国士兵走出战壕，在欢笑和足球比赛中休战

我必须承认，那几秒钟足以让我的一天振奋起来，并让我思考那个时代的人是如何度过一年中这个时候的日子的。

在视频之后的对话中，我意识到，在那些时候，尽管我不需要提及明显的原因，

技术进步令人难以置信，想想莫滕·泰杜姆的电影《模仿游戏》就知道了。

相信 [PHP](https://hackernoon.com/tagged/php) 也是如此，

**对语言、语法和代码的改进，即使是很小的改进，总的来说都会带来好的幽默和希望，事实是在这几个月里，**

即使在未来几年，

他们可以让我们成为更好的程序员，并创建让我们和我们的客户满意的应用程序。

今年，负责更新的团队实现了一些决定性的变化，

有些新功能已经被要求了好几年了，

其他的是令人愉快的惊喜，就像我们在节日期间从我们所爱的人那里收到的那些，就像在圣诞节那天士兵们收到的那些。

在这篇文章中，你将会了解到新的 PHP 7.3 将会实现哪些特性，以及你可以从它的发布中展示哪些轴。

# 跟随系列…

这篇博文是“ [**PHP 7.3 及其圣诞礼物**](https://amzn.to/2OfCZej) 的第二部分

如果你还没有阅读其他部分

你可以点击下面的链接查看其他博客文章
[PHP 7.3 简介和新语法](http://anastasionico.uk/blog/php-73)，
[PHP 7.3 的新特性](http://anastasionico.uk/blog/php-7-3-new-features#)，
[弃用和变更— *尚未发布，如果你现在想阅读的话可以获取电子书*](http://anastasionico.uk/blog/php-7-3-new-features#)

# 新功能

# JSON _ THROW _ ON _ 错误

很长一段时间以来，在使用 JSON 时没有足够的方法来处理错误一直是一个问题，全世界的 web 开发人员都认为这是该语言的一个巨大缺点，

PHP 7.3 的 RFC 以 23 比 0 的投票结果接受了这个更新，这表明了这个特性被请求的程度。

在 PHP v7.2 之前，我们需要使用一种变通方法来从 JSON 获得一个错误，它既不可靠，也不精通它的工作；

这里有一个例子:

```
json_decode("{");json_last_error() === JSON_ERROR_NONE // the result is falsejson_last_error_msg() // The result is "Syntax error"
```

是的，

我们可以确定 JSON 是否有错误，

但是，这显然不是最好的方法。

我将要展示的新标志是一个很好的选择，因为它让程序员有机会使用可以在“try-catch”代码块中管理的异常。

让我们看一个实际的例子，好吗？：

```
use JsonException;

try {
    $json = json_encode("{", JSON_THROW_ON_ERROR);
    return base64_encode($json);
} catch (JsonException $e) {
    throw new EncryptException('Could not encrypt the data.', 0, $e);
}
```

从前面的代码中可以看出，json_encode 函数现在有了一个可选参数 **JSON_THROW_ON_ERROR，它将捕获错误并使用以下异常方法显示错误:**

```
$e->getMessage(); // like json_last_error_msg()$e->getCode(); // like json_last_error()
```

PHP7.3 对现有代码的默认修改是中性的，因为它是一个可选参数，所以在你更新 PHP 后，一切都将正常工作。

这是此次更新最重要的特性之一，所以如果你想深入了解更多，请看一下[官方的 JSON _ THROW _ ON _ ERROR](https://wiki.php.net/rfc/json_throw_on_error)RFC

# 是 _ 可数的

代码中的可数元素可以是数组格式的变量，也可以是其类实现可数接口的对象。

**去年，PHP 7.2 增加了一个警告，每当 web 开发人员计算或试图循环一个不可数元素时，就会出现这个警告。**

解决这个问题是可能的，目前使用的最佳解决方案之一是应用类似下面代码片段的检查:

```
if (is_array($santa) || $santa instanceof Countable) {
    // $santa is countable
}
```

代码检查变量是数组还是可数接口的实例。

它可以工作，但看起来有点“拥挤”,因为你们中的许多人长时间工作，过一会儿看到这种线条会累坏你的眼睛。

开发新版本的团队考虑到了这一点，并添加了一个新功能，这将极大地帮助 web 开发人员。

**is _ countable 函数将一个变量作为参数，然后根据该函数是否可数返回一个布尔值。**

参数的格式没有限制，当然，如果你放了一个不可数的变量，返回将是 false。

让我们在实践中看到它

```
if (is_countable($santa)) {
    // $santa is countable
}
```

这段代码基本上做了和上一段相同的事情，但是更容易记忆，对我来说最重要的是可读性更好。

更不用说您可以使用这个函数，或者在一个三元条件操作符中使用它，这样看起来会更令人满意。

[*更多关于 is_countable 的信息请看官方 RFC*](https://wiki.php.net/rfc/is-countable)

# array_key_first()，array_key_last()

根据 PHP 5.6 版本，有超过 75 个属于数组类别的内置函数。

尽管有大量的工具可用，但目前，如果我们需要检索数组的第一个或最后一个键，我们必须使用 *array_keys()* 获取所有的键，然后只获取数组的第一个或最后一个值。

另一种方法是选择*结束()*或*复位()*。

正如你可能知道的，所有的方法都描述了修改数组指针，这是你可能不想做的事情(除了消耗资源)。

下一版本的 RFC 提出引入 4 种全新的方法来解决这个问题。

这四种方法是:

*   array_key_first()
*   array_key_last()
*   数组值优先()
*   数组值最后一个()

在这四组中，只有取出钥匙的那一组以 18 比 14 的票数被接受。

它们适用于数值数组和关联数组。

下面是一个数字的例子:

```
$reindeers = [1 => "Rudolph",2 =>  "Dasher",3 =>  "Dancer",4 =>  "Prancer",5 =>  "Vixen",6 =>  "Comet",7 =>  "Cupid",8 =>  "Donner",9 =>  "Blitzen"];$first = array_key_first($reindeers); // $first is equal to 1$last = array_key_last($reindeers); // $last is equal to 9
```

这是一个关联数组的例子:

```
$reindeers = ["Rudolph" => 1,"Dasher" => 2,"Dancer" => 3,"Prancer" => 4,"Vixen" => 5,"Comet" => 6,"Cupid" => 7,"Donner" => 8,"Blitzen" => 9];$first = array_key_first($reindeers); // $first is equal to "Rudolph"$last = array_key_last($reindeers); // $last is equal to "Blitzen"
```

这同样适用于本章 array_value_*中说明的其他两个函数

澄清一下，让我重复一遍，

这些功能以 18 个“否”和 15 个“是”被拒绝。

在我看来，这两个函数也很有用，但是根据一些 web 开发人员的说法，在某些情况下，返回的值可能是不明确的。

原因如下:

```
$reindeers = [];$first = array_value_first($reindeers ); // $first is equal to null$last = array_value_last($reindeers );// $last is equal to null
```

我在浏览论坛和与其他 web 开发人员交谈时遇到的另一个选项是返回一个类似[$key => $value]的元组。

尽管这个选项在新版本中不可用，但是看到了积极的响应，它可能会随以下 RFC 一起出现。

由于这是一个以前不存在的函数，所以不会有任何向后兼容性问题，唯一的问题可能会出现，如果您已经创建并使用了自己版本的 *array_key_first()* 和 *array_key_last()* 。

# 相同站点 cookie

部署安全的应用程序必须始终是每个程序员的主要关注点。

我们每个人每天都面临的一项任务是减少 CSRF 和信息泄露攻击的风险。

**同站点烹饪声明 cookies 必须仅在从同一个域发起请求时发送。**

这不是一个官方标准，但是 Google Chrome 和几个最好的 PHP 框架已经实现了它，而 Firefox 和其他框架的新版本证实了他们正在计划这样做。

[这是来自 caniuse.com 的相同站点 cookie 的支持模式](http://https//caniuse.com/#search=samesite)

目前，

cookie 由 set-cookie 报头发布，

web 开发人员可以在浏览器的标志旁边设置一个键值对，以便就 cookie 是否可用达成一致。

这种做法使得 CSRF 容易受到攻击。

新的 RFC 增加了一个新的参数，并修改了该语言的四个主要功能，从而解决了非中断模式的问题。

*   setcookie
*   setrawcookie
*   会话集 cookie 参数

```
setcookie($name [,$value [,$expire [,$path [,$domain [, $secure [,$httponly [,$samesite]]]]]]]);
```

提出了两种方法。

向函数中添加一个新的参数，或者允许一个选项数组来移动 cookies 中的所有选项。

它将如何工作？

可以将两个值添加到上述函数中的同一个站点标志中

他们宽松而严格。

使用 Lax 的 Cookies 在来自另一个域的 Get 请求中是可访问的，而相反，Strict 在 GET 请求中是不可访问的。

例如，当使用一个更宽松的标志时，标题将如下所示:

```
Set-Cookie: key=value; path=/; domain=example.org; HttpOnly; SameSite=Lax
```

在代码中包含增加安全性的特性似乎总是显而易见的，但是在决定将它们应用到我们的脚本中之前，我们需要正确地评估我们选择的利弊

使用相同的站点标志作为这些功能的补充参数所隐含的主要风险是，它可能永远不会成为官方标准。

这意味着最终浏览器将降低旗帜。

如果这种情况发生，并且你已经实现了它，它将导致你的应用程序塞满了需要删除的垃圾代码。

我个人的建议是等等看会发生什么。

如果这个标志会被更多地使用，并最终被定义为一个标准，那就去做吧！

说到向后更改，就像本文中看到的大多数以前的更改一样，在您的代码中实现它不会有任何问题。

![](img/81f240e8dd073aab6ad31da0e0d28d60.png)

# 结论

正如我所料，这个新版本不涉及突破性的变化，

既然不是新版本，就这样吧。

**PHP 正在采取小而持续的步骤，允许它随着时间的推移不断进步和改进**，尽管多年来人们已经转向更时髦的[编程语言](https://hackernoon.com/tagged/programming)。

在下一篇文章中，您将看到其他杂项变化和不再使用的功能列表，这些功能最终被弃用，以便为新项目腾出空间。

如果你喜欢这些帖子的内容，并且不想等待，那么在下面的链接中，你可以在亚马逊的 kindle 上找到单一格式的完整文章。

![](img/e7e4f400e1d8420cfbee51c604e266d4.png)

*这篇博文是 Kindle book "* [*PHP 7.3 及其圣诞礼物*](https://amzn.to/2PpbxeS)*的一段*

# *现在轮到你了*

*现在我想听听你的意见:*

*你对这种语言带来的新特性满意吗？
你打算更经常使用什么？*

*请在下面留言告诉我。*