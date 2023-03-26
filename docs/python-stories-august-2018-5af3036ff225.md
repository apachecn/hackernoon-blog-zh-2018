# Python 故事，2018 年 8 月

> 原文：<https://medium.com/hackernoon/python-stories-august-2018-5af3036ff225>

![](img/af1aa3f33e25c069b2a008bf912a012b.png)

我在跑 [@pythonetc](https://t.me/pythonetc) ，一个关于 [Python](https://hackernoon.com/tagged/python) 和[编程](https://hackernoon.com/tagged/programming)的电报频道一般。以下是 2018 年 8 月最好的帖子。

# 工厂方法

如果你在你的`__init__`中创建新的对象，最好将它们作为参数传递，并拥有一个工厂方法。它将业务逻辑与如何创建对象的技术细节分离开来。

在本例中，`__init__`接受`host`和`port`来构建数据库连接:

```
class Query:
    def __init__(self, host, port):
        self._connection = Connection(host, port)
```

可能的重构是:

```
class Query:
    def __init__(self, connection):
        self._connection = connection

    @classmethod
    def create(cls, host, port):
        return cls(Connection(host, port))
```

这种方法至少有以下优点:

*   它使得依赖注入变得容易。你可以在测试中做`Query(FakeConnection())`。
*   该类可以根据需要拥有任意多的工厂方法；不仅可以通过`host`和`port`构建连接，还可以通过克隆另一个连接、读取配置文件或对象、使用默认值等来构建连接。
*   这样的工厂方法可以转换成异步函数；这对于`__init__`来说是完全不可能的。

# 超级 VS 下一个

`super()`函数允许引用基类。当一个派生类想要*添加*一些东西到方法实现中，而不是完全重写它时，这是非常有用的:

```
class BaseTestCase(TestCase):
    def setUp(self):
        self._db = create_db()class UserTestCase(BaseTestCase):
    def setUp(self):
        super().setUp()
        self._user = create_user()
```

该功能的名称并不意味着*优秀*或*非常好*。*超*这个词在这个语境中暗示*在*之上(就像*中的警司*)。尽管我之前说过，`super()`并不总是引用基类，它可以很容易地返回一个兄弟。正确的名称应该是`next()`,因为根据 MRO 的下一个类被返回。

```
class Top:
    def foo(self):
        return 'top'class Left(Top):
    def foo(self):
        return super().foo()class Right(Top):
    def foo(self):
        return 'right'class Bottom(Left, Right):
    pass# prints 'right'
print(Bottom().foo())
```

注意`super()`可能会产生不同的结果，因为它们依赖于原始调用的 MRO。

```
>>> Bottom().foo()
'right'
>>> Left().foo()
'top'
```

# 用于创建类的自定义命名空间

创建一个类包括两大步骤。首先，评估类体，就像任何函数体一样。其次，元类(默认为`type`)使用结果名称空间(由`locals()`返回的名称空间)来构造实际的类对象。

```
class Meta(type):
    def __new__(meta, name, bases, ns):
        print(ns)
        return super().__new__(
            meta, name,
            bases, ns
        )class Foo(metaclass=Meta):
    B = 2
```

上面的代码打印了`{'__module__': '__main__', '__qualname__': 'Foo', 'B': 3}`。

显然，如果你做了类似于`B = 2; B = 3`的事情，那么元类只知道`B = 3`，因为只有那个值在`ns`中。这个限制是基于这样一个事实，即元类在主体评估之后工作。

然而，您可以通过提供*自定义名称空间*来干预评估。默认情况下，使用一个简单的字典，但是您可以使用元类`__prepare__`方法提供一个定制的类似字典的对象。

```
class CustomNamespace(dict):
    def __setitem__(self, key, value):
        print(f'{key} -> {value}')
        return super().__setitem__(key, value)class Meta(type):
    def __new__(meta, name, bases, ns):
        return super().__new__(
            meta, name,
            bases, ns
        )@classmethod
    def __prepare__(metacls, cls, bases):
        return CustomNamespace()class Foo(metaclass=Meta):
    B = 2
    B = 3
```

输出如下所示:

```
__module__ -> __main__
__qualname__ -> Foo
B -> 2
B -> 3
```

这就是`enum.Enum`如何被[保护免受复制](https://docs.python.org/3/library/enum.html#duplicating-enum-members-and-values)。

# matplotlib

`matplotlib`是一个复杂灵活的 Python 绘图库。它受到多种产品的支持，包括 Jupyter 和 Pycharm。

这就是你如何用`matplotlib`:[https://repl.it/@VadimPushtaev/myplotlib](https://repl.it/@VadimPushtaev/myplotlib)绘制一个简单的分形图形，见文章开头的图片。

# 时区支持

Python 提供了强大的库来处理日期和时间:`datetime`。有趣的是，`datetime`对象有支持时区的特殊接口(即`tzinfo`属性)，但是这个模块只对它的接口有有限的支持，剩下的工作留给不同的模块。

这份工作最受欢迎的模块是`pytz`。棘手的是，`pytz`并没有完全满足`tzinfo`接口。`pytz`文档在第一行中陈述了这一点:“这个库不同于所记录的用于 tzinfo 实现的 Python API。”

您不能使用`pytz`时区对象作为`tzinfo`属性。如果你尝试，你可能会得到绝对疯狂的结果:

```
In : paris = pytz.timezone('Europe/Paris')
In : str(datetime(2017, 1, 1, tzinfo=paris))
Out: '2017-01-01 00:00:00+00:09'
```

看那个`+00:09`偏移。`pytz`的正确用法如下:

```
In : str(paris.localize(datetime(2017, 1, 1)))
Out: '2017-01-01 00:00:00+01:00'
```

此外，在任何算术运算之后，您应该`normalize`您的 datetime 对象以防偏移改变(例如在 DST 周期的边缘)。

```
In : new_time = time + timedelta(days=2)
In : str(new_time)
Out: '2018-03-27 00:00:00+01:00'
In : str(paris.normalize(new_time))
Out: '2018-03-27 01:00:00+02:00'
```

从 Python 3.6 开始，建议用`dateutil.tz`代替`pytz`。它与`tzinfo`完全兼容，可以作为属性传递，不需要`normalize`，尽管工作起来有点慢。

如果你对为什么`pytz`不支持`datetime` API 感兴趣，或者你希望看到更多的例子，可以考虑阅读关于这个主题的体面的[文章](https://blog.ganssle.io/articles/2018/03/pytz-fastest-footgun.html)。

# 停止迭代魔法

除非出现异常，否则每次调用`next(x)`都会从`x`迭代器返回新值。如果这是`StopIteration`，这意味着迭代器已经耗尽，无法提供更多的值。如果一个生成器被迭代，它会在主体结束时自动引发`StopIteration`:

```
>>> def one_two():
...     yield 1
...     yield 2
...
>>> i = one_two()
>>> next(i)
1
>>> next(i)
2
>>> next(i)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

`StopIteration`由调用`next`的工具自动处理:

```
>>> list(one_two())
[1, 2]
```

问题是，在生成器中引发的任何意外的`StopIteration`都会导致它安静地停止，而不是实际引发一个异常:

```
def one_two():
    yield 1
    yield 2def one_two_repeat(n):
    for _ in range(n):
        i = one_two()
        yield next(i)
        yield next(i)
        yield next(i)print(list(one_two_repeat(3)))
```

这里的最后一个`yield`是一个错误:`StopIteration`被引发，使`list(...)`停止迭代。结果是`[1, 2]`，出人意料。

然而，这在 Python 3.7 中被改变了。此类外来`StopIteration`现替换为`RuntimeError`:

```
Traceback (most recent call last):
  File "test.py", line 10, in one_two_repeat
    yield next(i)
StopIterationThe above exception was the direct cause of the following exception:Traceback (most recent call last):
  File "test.py", line 12, in <module>
    print(list(one_two_repeat(3)))
RuntimeError: generator raised StopIteration
```

从 Python 3.5 开始，您可以通过`from __future__ import generator_stop`启用相同的行为。