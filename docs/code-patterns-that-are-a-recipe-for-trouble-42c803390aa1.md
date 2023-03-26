# 导致麻烦的代码模式

> 原文：<https://medium.com/hackernoon/code-patterns-that-are-a-recipe-for-trouble-42c803390aa1>

![](img/8f7bad86d3871be0f2e176208fae3125.png)

有时我会注意到我已经浪费了一个小时左右，因为我的[代码](https://hackernoon.com/tagged/code)中的一些错误，或者我做错了什么。当这种情况发生时，我会试着退一步，理解我做了什么，并尝试改变我的工作方式，以防止这种情况再次发生。

有趣的是，多年来我已经收集了一些我使用的指导方针，有时当我的队友问我关于它们的问题时，我实际上不记得*为什么*。我只是*知道*这是最佳实践，我应该遵循它。但是在这篇文章中，我尝试了我所遵循的一些实践，并试图证明它们是正确的。

还有一个第 2 部分[，有更多的模式](https://engineering-management.space/post/code-patterns-that-are-a-recipe-for-trouble-2/)，主要与测试、可读性和风格有关。使用这些模式可能会把一个项目变成没有人想做的事情。

# 没有上下文的异常

每当我看到代码抛出一个没有附加信息的异常，我都会皱眉。类似于:

```
def operate
  raise "Ups something went wrong" if damage?
end
```

除此之外，我们得到一个堆栈跟踪和出错的信息。但是大多数时候我们缺乏理解问题的背景。像当前用户、当前对象的数据、我们试图完成什么以及我们期望什么等数据。根据经验，所有自定义异常都应该有描述发生了什么的数据。

```
def operate(operator)
  raise OperationDamagedException.new(self, operator) if damage?
end
```

这将使我们有更多的信息。

当出现问题时，有信息来排除故障是很有用的。在一些语言中(Ruby，JavaScript ),对日志没有太多的关注，而在其他语言中，日志是非常重要的事情(Java)。

例如，在 Java 中，我习惯于看到这样的代码:

```
try { 
  // ... 
} catch (final IOException ex) {
  LOGGER.error("Something bad happening and here you have info.", ex);
  throw new BusinessException(ex); 
}
```

这实际上要好得多。但问题是，我们必须在日志中搜索这是何时发生的，这可能并不容易。实际上，如果我们无法访问生产环境，获取日志可能会非常复杂。

但是我们总是有例外。因此，我总是推动更好的错误报告，并试图出售主要针对应用程序行为的日志记录。

# 带有大量默认参数的构造函数

这是我在 Java 代码库中见过的。Java 代码和工具允许容易的重构，有时允许这种[模式](https://hackernoon.com/tagged/pattern)。例如，假设我们有一个在很多地方使用的 POJO，在单元测试中我们开始有:

```
@Test public void test1() {
  final Entity entity = new Entity(1, null, null, false, "", Optional.empty());
  // ... 
}@Test public void test2() { 
  final Entity entity = new Entity(1, null, null, false, "", Optional.empty()); 
  // ... } // And so on
```

当我们以任何方式改变参数时，我们将需要更新所有我们想要一个虚拟`Entity`的地方。这些代码可能容易重构，但不容易修改。

相反，我们应该有这样的内容:

```
class Entity { 
  public static Entity empty() { 
    // Even better if this can be a static immutable instance
    new Entity(1, null, null, false, "", Optional.empty()); 
  }
}
```

现在，如果我们改变`Entity`的构造函数，所有使用它的代码都不需要改变。注意，我们可能有一种更“Java 化”的方式来做这件事，通过创建一个*构建器*类。而那个构建器类可以有那个*“空”*方法。

老实说，这可能是我对 Java 编码最大的抱怨。创建这些构建器类总是很麻烦。是的，我们的 IDE 可能会有所帮助，但是如果我们想要正确记录的代码，我们仍然需要去处理所有构建器的方法。

无论如何，我们可以通过创建一个不带参数的构造函数来简化这个过程。但是无论我们采取什么方法，一个问题仍然存在:我们只是为了测试而添加代码吗？是的，我们是。我们只是因为测试而改变了这个类的 API。现在，在这个场景中，我宁愿专注于*可测试性*，也不会介意。

但是有更好的选择，主要是通过创建一个工厂类来进行测试，例如，可以使用一些 *faker* 逻辑。

# 状态数组

这在没有`enum` s 的语言中很常见。它是这样的:

```
class Invoice
  validate :status, inclusion: { in: [:draft, :sent, :paid] }
enddef pay!(invoice)
  return if invoice.status == :paid
end
```

问题是这些状态开始泄露。我们将在几个地方开始使用它们的名字。它们容易出现错别字，很难维护。假设您添加了一个新的`:rejected`状态。最好把这些概念抽象在具体的常数上。

```
class Invoice
  STATUSES = ([:draft, :sent] + PAID_STATUSES).freeze 
  PAID_STATUSES = [:paid].freeze   validate :status, inclusion: { in: STATUSES }
enddef pay!(invoice)
  return if invoice.status.in?(Invoice::PAID_STATUSES)
end
```

现在`Invoice`类是*状态*概念的所有者，不用担心使用它的代码就可以很容易地改变。

但是我们也可以做得更好。因为将来我们会有这样的变更请求:

> *我们想要一个被拒绝的状态，但只针对葡萄牙语用户。*

现在仅仅用常量是不行的，我们到处都有常量。我们可以重构，但是最好从一开始就做好准备。

```
class Invoice
  STATUSES = ([:draft, :sent] + PAID_STATUSES).freeze
  PAID_STATUSES = [:paid].freeze   def self.statuses(context = {})
    return STATUSES + [:rejected] if portuguese?(context)
    STATUSES
  end
end
```

这样我们就有了一个好的缺省值，但是可以用附加信息来增加它。

# 具有封闭 API 的高级类

我说的是代表服务、交互器、用例以及整体高级接口的类。形成 API 的类是公共的，用作其他层的边界。在动态语言中，我们可能有任意的参数，这对可维护性很好。如果我们定义一个严格的 API，我们将有如下场景:

```
class ProcessPayment
  def initialize(account, amount)
    # ... 
  end   def call
    # ... 
  end
endProcessPayment.new(account, 10.0).call
```

现在想象一下，这个类到处都在使用，我们有一个包含它的库，甚至外部客户也在使用它。现在我们想以不中断的方式改变它的 API。我们希望添加金额的货币。我们可以添加另一个可选参数。但是如果我们想为发票添加数据呢？另一个可选参数？

我们最终可能会:

```
class ProcessPayment
  def initialize(account, amount, currency, invoice_data, include_taxes)
    # ... 
  end 
end ProcessPayment.new(account, 10.0, :EUR, { vat: 1234 }, false).call
```

这将很难维持。当我们在这种情况下，如果我们想改变一个论点的顺序或语义，这可能会很复杂，但不会有突破性的改变。我们总是可以创建额外的工厂方法。但是这需要维护和测试更多的代码。

另一种选择是假设参数列表将被改变。

```
class ProcessPayment
  def initialize(args)
    # ... 
  end 
end ProcessPayment.new({
  account: account, 
  amount: 10.0, 
  currency: :EUR, 
  invoice_data: { vat: 1234 }, 
  include_taxes: false 
}).call
```

这样可读性更好，也更容易维护。但是我们需要考虑适当的文档和验证。在类型化语言中，这可能是一个仅用于参数的 POJO。

# 不使用可选参数的散列

这与之前的模式有关。有时我们有一些方法有一些选项，我们只是为它们添加更多的参数。实际上，我有时会这样做，而且通常会后悔。

示例:

```
function getSettingsFor(user, bypassCache) { 
  // ... 
} 
const settings = getSettings(user, true);
```

看到已经有气味了。因为我们永远不会知道那个*布尔*做了什么，除非我们阅读文档。对于这些场景，更好的方法是增加接收选项的功能:

```
function getSettingsFor(user, options) {
  // ... 
}
const settings = getSettings(user, { 
  bypassCache: true, 
  includeHolidays: false 
}); // Or just: const settings = getSettings(user)
```

# 使用哈希而不是类型化对象

根据前面的例子，如果我们使用强类型语言，使用映射/散列/字典实际上可能不利于维护。我们可能会开始得到这样的东西:

```
final Map<String, Object> options = new ImmutableMap.Builder<String, Object>()
  .put("bypassCache", true) 
  .put("includeHolidays", false) 
  .put("ignore", ImmutableList.of("Profile", "Account")) 
  .build(); final Settings settings = getSettings(user, options);
```

Java 开发人员可能已经因为看到原始的`String`而不寒而栗。在这种情况下，我们可以用所有这些信息创建一个 POJO，它将更加精彩。

```
final Options options = Options.Builder() 
  .setBypassCache(true) 
  .setIncludeHolidays(false) 
  // ... 
  .build();final Settings settings = getSettings(user, options);
```

有趣的是，我发现这种类型化的方法在类型化语言中更好，但例如在 Clojure 中，我并不怀念它。拥有一个包含信息的数据图和一个规范来验证我得到了我期望的东西，对我来说会更好。

# 轻松访问重量级功能

这在 Rails 应用程序中很常见。ActiveRecord 使得获取数据并将其保存在数据库中变得非常容易。你可以在任何地方使用它。例如，您可以在 PORO 的一个简单属性上看到这样的内容:

```
def can_get_drivers_license?
  requirements = self.user.country.driver_settings.license.requirements
  age > requirements.min_age if requirements.present? age >= 18 
end
```

我故意加了一个大*车次*。在不知道代码的情况下，我不知道它是会执行一个包含多个连接的查询，还是会执行多个查询。每个对象可能都是满载的，即使我们并不需要它。该代码还将从池中获得一个连接，并连接到某个地方。

发生了很多事情。当这种情况发生时，这意味着改变某些东西的可能性更大，并且很难维护这些代码。

在这些情况下，我实际上主张有一些约束。例如，如果我们有一个执行接收连接池的查询的类，我们就不能在这里使用它，因为我们没有连接。这将迫使我们考虑我们的数据模型以及从哪里获取所需的数据。

而且它会使`can_get_drivers_license?`成为*纯*的方法，那是非常容易测试的。

```
def can_get_drivers_license?(requirements = nil) 
  age > requirements.min_age if requirements.present?
  age >= 18 
end
```

使用 ActiveRecord 可能很快，但我们需要考虑这样做的成本，并尽量减少我们对它的依赖。

# 大方法

有一个规则说[每个方法应该只有 5 行](https://robots.thoughtbot.com/sandi-metz-rules-for-developers)。这在某些语言上更难实现。虽然这可能过于极端，但我已经觉得有必要实施它。

但主要的一点是，我们应该把事情拆分成更小的方法/函数，最好是*纯*。例如，我认为*分支*中的大多数代码可能是一个特定的方法。这可能很麻烦。我们需要提取它，为它编写文档，并添加一个测试。示例:

```
function operate(users) { 
  return users.filter(user => { 
    return user.isSomething && user.isReady; 
  }) 
}
```

反对:

```
function isUserReasy(user) { 
  return user.isSomething && user.isReady; 
} function operate(users) { 
  return users.filter(isUserReady); 
} 
```

这更容易阅读和更改。使用`filter(isUserReasy)`可以直接传达该行正在做什么。我们可能在 lambda 中有简单的逻辑，但我们仍然需要在头脑中读取、解析和解释它，有时真的*理解*发生了什么并不那么容易。

# 紧密耦合代码

那些*比它应该知道的*更多的代码是很难改变和适应改变请求的。举一个 Rails 应用程序中非常常见的例子:轻松发送电子邮件的能力。

```
def process_order 
  # ... 
  UserNotifier.order_placed(user.email, order).deliver_later 
end
```

为了理解这一行看起来如此无辜的问题，让我们考虑一些更改请求。第一个是:**用户可以在首选项中定义他们希望收到的电子邮件**。

现在，我们不得不去所有我们使用邮件程序的地方，我们需要以某种方式拥有用户偏好，这可能是我们甚至没有的。显然，我们现在应该创建一个抽象来处理电子邮件并隐藏那些细节。

然后另一个变更请求:**我们将不再做电子邮件，我们将使用一个工具来做这个**。营销和产品团队希望对电子邮件拥有所有权，并希望构建模板和管理一切。为此，他们有一个接收事件的应用程序，然后他们可以自己做任何事情。

好了，现在我们必须将所有这些邮件程序调用转换成如下形式:

```
def process_order 
  # ... 
  publish(:order_created, { user: user, order: order }) 
end
```

我们可以为此创建一个很好的*事件总线*，让一切都解耦。但是这种重构可能是痛苦的。`UserNotifier.order_placed`用的是什么数据？我们不知道。我们必须找到那个方法和相关的模板，看看我们是否还需要发送我们不期望的信息。

这是一个简单的场景，其中耦合看起来没什么问题。但是从经验来看，重构起来却很麻烦。

# 使变量变异的几个条件或循环

我认为这是更接近意大利面条代码概念的配方。基本上是因为当你以这种方式开始时，后退一步并进行重构会更加困难。按照这种格式，你会有新的变化，事情会变得混乱。

```
let status; 
let error; 
if (condition1) { 
  status = 404; 
  error = "Error 404 detail"; 
} else if(condition2) { 
  status = 403; 
  error = "Error 403 detail"; 
} else { 
  if (condition4) { 
    status = 402; 
    error = "Error 402 detail"; 
  } 
} // use status and error
```

这往往会变得很大，并且很难维护。通常，每当我们有一个变异的变量时，我们应该意识到并检查我们是否真的需要它。可用变量的数量以及它们是否可以变异，对代码的维护造成了很大的困难。

# 开关逻辑

有时`switch`名声不好，但我发现它在一些场合很有用。我认为最大的问题来自于使用一个`switch`来处理逻辑。

```
switch(operation) { 
  case 'add': 
    validate(); 
    performAdd(); 
    break; 
  case 'sub': 
    validateMinus(); 
    performSub(); 
    registOp(); 
    break; 
  default: 
    error(); 
}
```

当我们有了这个，我们知道几件事会发生:

*   要更改这段代码，我们可能需要阅读上下文的完整方法
*   找到换衣服的地方不容易
*   当我们需要改变几个分支时会发生什么？
*   在这里添加其他类似于`operation`但又不完全相同的东西可能会有用。但是因为我们已经准备好了上下文，所以我们只是复制粘贴然后继续。
*   这将变得越来越大

很难全面了解情况并理解变化。我们可以使用多态层次结构或者从*操作*到功能的映射。通过拆分逻辑，我们可以更好地了解全局，同时也能够深入了解特定的操作逻辑。

# 摘要

编写易于阅读和修改的代码非常困难。我们通常不会考虑这些，因为我们只关注当下，想要建立一些东西。默认情况下，有一些实践和模式可以帮助我们在第一次尝试时写出更好的代码。但是我们需要经常练习，努力提高我们的编码技能。

*原载于 2018 年 5 月 8 日*[*engineering-management . space*](https://engineering-management.space/post/code-patterns-that-are-a-recipe-for-trouble/)*。*