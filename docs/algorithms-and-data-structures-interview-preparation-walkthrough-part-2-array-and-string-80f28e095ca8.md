# 算法和数据结构面试准备和演练—第 2 部分，数组和字符串

> 原文：<https://medium.com/hackernoon/algorithms-and-data-structures-interview-preparation-walkthrough-part-2-array-and-string-80f28e095ca8>

![](img/4e2120f482652d07ef06e763b12fcbaf.png)

Arrrrr…. Ray!(Source: Gorgaiphotography/iStock)

在我之前的帖子: [**算法与数据结构面试准备&演练—第一部分**](/@victorlin_38374/algorithms-and-data-structures-interview-preparison-questions-part-1-a23aec17daaf) 中，我们谈到了如何做复杂度时空分析，也看到了常见的 Big-O 因子，并附有实例。

在这篇文章中，我将开始深入讨论阵列，并回答一些[面试](https://hackernoon.com/tagged/interview)问题，希望在阅读结束时，你会对阵列有一个很好的了解。一旦我们熟悉了数组，我就来说说字符串，以及如何用数组解决字符串问题。

数组是一个包含一组元素的数据结构。数组最基本的实现是一个静态数组。之所以称之为*静态*是因为大小是固定的。对某个位置的读/写访问是 O(1)。

## 静态数组的实现

```
class StaticArray
  def initialize(length)
    self.store = Array.new(length)
  end# O(1)
  def [](index)
    self.store[index]
  end# O(1)
  def []=(index, value)
    self.store[index] = value
  endprotected
  attr_accessor :store
end
```

我们从静态数组创建一个动态数组，如下所示。读/写访问也是 O(1)。让我们为它实现一些通用的方法，即 pop()、push()、shift()和 unshift()。这里的关键是，当我们达到数组大小时，我们想要调整它的大小并加倍它的空间，以便向数组推送()或取消推送()新元素。

## 动态数组的实现

```
require_relative "static_array"class DynamicArray
  attr_reader :lengthdef initialize
    [@length](http://twitter.com/length) = 0
    [@capacity](http://twitter.com/capacity) = 8
    [@store](http://twitter.com/store) = StaticArray.new(8)
  end# O(1)
  def [](index)
    check_index(index)
    [@store](http://twitter.com/store)[index]
  end# O(1)
  def []=(index, value)
    check_index(index)
    [@store](http://twitter.com/store)[index] = value
  end# O(1)
  def pop
    check_index(0)
    [@length](http://twitter.com/length) -= 1
    [@store](http://twitter.com/store)[length + 1]
  end# O(1) **amortized**; O(n) worst case.
  def push(val)
    resize! if [@length](http://twitter.com/length) == [@capacity](http://twitter.com/capacity)
    [@store](http://twitter.com/store)[[@length](http://twitter.com/length) + 1] = val
    [@length](http://twitter.com/length) += 1
  end# O(n): has to shift over all the elements.
  def shift
    check_index(0)
    idx = 0
    first_el = [@store](http://twitter.com/store)[0]
    while idx < [@length](http://twitter.com/length) - 1
      [@store](http://twitter.com/store)[idx] = [@store](http://twitter.com/store)[idx + 1]
      idx += 1
    end
    [@length](http://twitter.com/length) -= 1
    first_el
  end# O(n): has to shift over all the elements.
  def unshift(val)
    resize! if [@length](http://twitter.com/length) == [@capacity](http://twitter.com/capacity)
    idx = [@length](http://twitter.com/length)
    while idx > 0
      [@store](http://twitter.com/store)[idx] = [@store](http://twitter.com/store)[idx - 1]
      idx -= 1
    end
    [@store](http://twitter.com/store)[0] = val
    [@length](http://twitter.com/length) += 1
    [@store](http://twitter.com/store)
  endprotected
  attr_accessor :capacity, :store
  attr_writer :lengthdef check_index(index)
    raise "out of bounds" if ([@length](http://twitter.com/length) < index + 1 || index < 0)
  end# O(n): has to copy over all the elements to the new store.
  def resize!
    new_store = StaticArray.new([@capacity](http://twitter.com/capacity) * 2)
    idx = 0
    while idx < [@length](http://twitter.com/length)
      new_store[idx] = [@store](http://twitter.com/store)[idx]
      idx += 1
    end
    [@store](http://twitter.com/store) = new_store
    [@capacity](http://twitter.com/capacity) *= 2
  end
end
```

## 什么是*摊销？*

如果您足够仔细地阅读，您会注意到代码片段中有一个关键字“摊销”。那是什么意思？当我们想要向数组中添加(或推送)一个新元素，并且它达到了它的大小限制时，我们想要将大小加倍。但是，`resize!`方法会分配一个更大的区域，移动整个数组，并删除之前的。这是一个`O(n)`操作。但是如果我们只是每隔`O(1/n)`时间做一次，那么平均*到*它仍然会出来`O(n * 1/n) = O(1)`。那叫做*摊余成本*。

## 动态阵列的时间复杂度和空间复杂度

在一般和最坏的情况下，

`Access O(1)`

`Search O(n)`

`Insertion O(n)` *(数组末尾为 O(1)摊销，数组开头或中间为 O(n)*

`Deletion O(n)`

`Space O(n)`

我们现在知道访问数组中的元素很快(`O(1)`)，而搜索/添加/移除相对较慢(`O(n)`)，有时需要遍历整个数组。

## 环形缓冲区

它是一种使用静态数组的数据结构，就像它是端到端连接的一样。

```
require_relative "static_array"class RingBuffer
  attr_reader :lengthdef initialize
    [@length](http://twitter.com/length) = 0
    [@capacity](http://twitter.com/capacity) = 8
    [@start_idx](http://twitter.com/start_idx) = 0
    [@store](http://twitter.com/store) = StaticArray.new([@capacity](http://twitter.com/capacity))
  end# O(1)
  def [](index)
    check_index(index)
    ring_index = (index + [@start_idx](http://twitter.com/start_idx)) % [@capacity](http://twitter.com/capacity)
    [@store](http://twitter.com/store)[ring_index]
  end# O(1)
  def []=(index, val)
    check_index(index)
    ring_index = (index + [@start_idx](http://twitter.com/start_idx)) % [@capacity](http://twitter.com/capacity)
    [@store](http://twitter.com/store)[ring_index] = val
  end# O(1)
  def pop
    check_index(0)
    [@length](http://twitter.com/length) -= 1;
    val = [@store](http://twitter.com/store)[([@length](http://twitter.com/length) + [@start_idx](http://twitter.com/start_idx)) % [@capacity](http://twitter.com/capacity)]
    [@store](http://twitter.com/store)[([@length](http://twitter.com/length) + [@start_idx](http://twitter.com/start_idx)) % [@capacity](http://twitter.com/capacity)] = nil
    val
  end# O(1) amortized
  def push(val)
    resize! if [@length](http://twitter.com/length) == [@capacity](http://twitter.com/capacity)
    [@store](http://twitter.com/store)[([@length](http://twitter.com/length) + [@start_idx](http://twitter.com/start_idx)) % [@capacity](http://twitter.com/capacity)] = val
    [@length](http://twitter.com/length) += 1
  end# O(1)
  def shift
    check_index(0)
    val = [@store](http://twitter.com/store)[[@start_idx](http://twitter.com/start_idx)]
    [@store](http://twitter.com/store)[[@start_idx](http://twitter.com/start_idx)] = nil
    [@start_idx](http://twitter.com/start_idx) = ([@start_idx](http://twitter.com/start_idx) + 1) % [@capacity](http://twitter.com/capacity)
    [@length](http://twitter.com/length) -= 1
    val
  end# O(1) amortized
  def unshift(val)
    resize! if [@length](http://twitter.com/length) == [@capacity](http://twitter.com/capacity)
    [@start_idx](http://twitter.com/start_idx) = ([@start_idx](http://twitter.com/start_idx) - 1) % [@capacity](http://twitter.com/capacity)
    [@store](http://twitter.com/store)[[@start_idx](http://twitter.com/start_idx)] = val
    [@length](http://twitter.com/length) += 1
    val
  endprotected
  attr_accessor :capacity, :start_idx, :store
  attr_writer :lengthdef check_index(index)
    raise "index out of bounds" if (index < 0 || index > [@length](http://twitter.com/length) - 1)
  enddef resize!
    new_store = StaticArray.new([@capacity](http://twitter.com/capacity) * 2)
    idx = 0
    while idx < [@length](http://twitter.com/length)
      new_store[idx] = [@store](http://twitter.com/store)[([@start_idx](http://twitter.com/start_idx) + idx) % [@capacity](http://twitter.com/capacity)]
      idx += 1
    end
    [@store](http://twitter.com/store) = new_store
    [@start_idx](http://twitter.com/start_idx) = 0
    [@capacity](http://twitter.com/capacity) *= 2
  end
end
```

要掌握数组数据结构和问题，我们至少需要非常熟悉:

1.  循环操作。
2.  使用指针记录位置的感觉。
3.  交换技术。
4.  基础数学。
5.  常见的数组方法及其时间复杂度，即 pop()、push()、shift()、unshift()、forEach()、sort()、slice()、splice()、reverse()、concat()、filter()、map() …等。

使用数组的一些优势:

●恒定时间访问并允许随机访问

●将相似的项目分组

还有一些缺点…

●对于大型阵列来说，插入和删除的成本可能很高

●动态数组调整大小是有成本的，并且受到分配大小的限制

说够了，这里有一些流行的面试问题供你练习:

1.  移动零—给定一个数字数组，编写一个函数将所有的`0`移动到数组末尾，同时保持非零元素的相对顺序。( [**思想过程和解决方案**](/@victorlin_38374/coding-interview-move-zeros-array-a15149fe5065) )
2.  股票 101——何时买入/卖出股票？给定一个包含股票每日价格的数组。努力寻找最大利润。( [**思想过程和解决方案**](/@victorlin_38374/coding-interview-stocks-101-array-6d1ca6145f83) )
3.  查找重复项—给定一个数字数组，如果任意数字在数组中出现多次，则返回 true，否则返回 false。( [**思想过程和解决方案**](/@victorlin_38374/coding-interview-find-duplicates-array-d772040eaebb) )

## 线

现在我们知道了数组，让我们来谈谈字符串——它**只不过是一个基于字符的数组**。解数组题只需要学习技巧，你天生就是个串高手！

在深入研究与字符串相关的问题之前，我们需要熟悉:

1.  与字符串相关的方法，如 charAt()、includes()、trim()、concat()、slice()、split()、substring()、toUpperCase()、toLowerCase()、toString()…等等。
2.  两点。
3.  交换数组中的元素。
4.  递归。

在我的 [**下一篇文章**](/@victorlin_38374/algorithms-and-data-structures-interview-preparation-walkthrough-part-3-linked-list-queue-63e5e71f76a6?source=friends_link&sk=d987d5ca06f537dc6db808f6a32ef44b) **，**中，我将讨论队列、堆栈和链表的数据结构。

## 在你走之前—

没有比在 Medium 上给我一个 follow([**Victor Lin**](/@victorlin_38374))更好的支持我的方式了。让我知道我应该多写！

你知道你可以放弃 50 吗👏通过按下👏按钮？如果你*真的*喜欢这篇文章，那就试试吧！