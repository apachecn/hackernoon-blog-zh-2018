# clo jure——懒惰的教训

> 原文：<https://medium.com/hackernoon/clojure-lessons-from-laziness-252ca7fc4fa7>

## 懒惰可能是一件好事。也许是最好的东西？

![](img/30fb0e84aa6ed5e1bb753cc26d45ea13.png)

Image source — [https://winnin.com/battle/357860-Cute-Animals-Who%27ll-Make-You-Feel-Less-Lazy?force_lang=es](https://winnin.com/battle/357860-Cute-Animals-Who%27ll-Make-You-Feel-Less-Lazy?force_lang=es)

毫无疑问，在计算的后端，懒惰是非常强大的。 [Clojure](https://hackernoon.com/tagged/clojure) 美在很多方面，[懒](https://hackernoon.com/tagged/laziness)是核心成分。大量关于它、关于它和围绕它的文章。它们涵盖了如何编写这样的代码的各个方面。大多数情况下，例如斐波那契或通用数字生成器。一些问题应该随之而来。我肯定有一些-

*   对于一个新手，或者来自其他编程风格的人来说，它容易理解吗？收养障碍？
*   如何把它和你我正在研究的一个现实问题联系起来？
*   采用并将其转移到“生产”的障碍是什么？
*   有哪些陷阱和谬误？

因此，这里有一个尝试来回答我在变懒时学到的教训。

## 什么是懒惰？

首先是“什么”。懒惰，也就是说，懒惰求值是直到绝对需要它们的返回值时才完成求值的代码部分。把这想象成类似于我们把报税表推迟到最后一刻才提交。与此同时，去做一些实际上有用的事情对我们来说是非常强大的。我们可以早点把它们归档，但是我们没有也不需要归档。绝对让我觉得“高效”。(旁注:为了更好的双关语，查看下面参考资料中的帖子(: )

回到后端，有些表达式直到流程的后面才需要进行完整的计算。在这个层面上说得通。现在是“如何”。对于这一部分，让我们仔细看看 Clojure 的核心原则。懒 evals 绝对是其中之一。特别是，惰性是通过惰性序列构建的，惰性序列可以在函数之间传递，并且只有在发出 eval 命令时才会被评估。这里是我的意思的快速浏览(在[https://clojuredocs.org/clojure.core/lazy-seq](https://clojuredocs.org/clojure.core/lazy-seq)中给出)

```
;; The following defines a lazy-seq of all positive numbers.  Note that 
;; the lazy-seq allows us to make a recursive call in a safe way because
;; the call does not happen immediately but instead creates a closure.

user=> (defn positive-numbers 
	([] (positive-numbers 1))
	([n] (lazy-seq (cons n (positive-numbers (inc n))))))
#'user/positive-numbers

user=> (take 5 (positive-numbers))
(1 2 3 4 5)
```

厉害！就这样，这是我学到的第一课

第一课——我想偷懒，这很好，但是我到底想在什么方面偷懒呢？

## 为什么是懒惰？

![](img/286e36b8b830a15ce36bacaf21a52211.png)

Source - imgflip.com

“为什么？”，你问。总有一些代码直到被要求时才需要完成。即使他们这样做了，我们也不需要评估的所有返回 val，也许只需要从`(count allresults)`中选择`n`，这听起来是一个公平的优势。

让我们拿出`positive-numbers`的例子，不要懒惰和潜在的成本。

```
(defn not-lazy-positive-numbers [n]
  (mapv
    #(let [v (inc %)]
       ; to know when evaluation happens
      (println "executing" v)
      v)
    (range (- n 1) (+ n 10))));returns n to n+10 values starting from n
(not-lazy-positive-numbers 10)
;executing 10
;executing 11
;executing 12
;executing 13
;executing 14
;executing 15
;executing 16
;executing 17
;executing 18
;executing 19
;executing 20
;[10 11 12 13 14 15 16 17 18 19 20]
```

`not-lazy-positive-numbers`立即评估。为了说明这一点，假设我们必须从 10 到 40 之间的正数池中挑选 15 个数字。

```
(take 15 (concat (not-lazy-positive-numbers 10) (not-lazy-positive-numbers 20) (not-lazy-positive-numbers 30)))
;executing 10
;executing 11
;executing 12
;executing 13
;executing 14
;executing 15
;executing 16
;executing 17
;executing 18
;executing 19
;executing 20
;executing 20
;executing 21
;executing 22
;executing 23
;executing 24
;executing 25
;executing 26
;executing 27
;executing 28
;executing 29
;executing 30
;executing 30
;executing 31
;executing 32
;executing 33
;executing 34
;executing 35
;executing 36
;executing 37
;executing 38
;executing 39
;executing 40
(10 11 12 13 14 15 16 17 18 19 20 20 21 22 23)
```

哇，它评估一切，即 30+，即使我们只需要其中的 15 个值。

**第二课——有很多代码段做了多余的事情，寻找它们**

现在，从最初的例子中应用一些懒惰，

```
(defn lazy-positive-numbers [n]
  (println "executing" n) ; to know what's executing, returns a lazy seq of max 1+10 executions
  (lazy-seq (cons n (take 10 (lazy-positive-numbers (inc n))))))(lazy-positive-numbers 10)
;executing 10
;executing 11
;executing 12
;executing 13
;executing 14
;executing 15
;executing 16
;executing 17
;executing 18
;executing 19
;executing 20
;executing 21
;(10 11 12 13 14 15 16 17 18 19 20)
```

`lazy-positive-numbers`总是返回 10 个值，作为惰性序列。(旁注 lazy seq 的丑陋代码可能是线程化的:/)

同样，假设我们必须从 10 到 40 之间的正数中选择 15 个数字。这一次懒洋洋地

```
(take 15 (concat (lazy-positive-numbers 10) (lazy-positive-numbers 20) (lazy-positive-numbers 30)))
;executing 10
;executing 20
;executing 30
;executing 11
;executing 12
;executing 13
;executing 14
;executing 15
;executing 16
;executing 17
;executing 18
;executing 19
;executing 20
;executing 21
;executing 21
;executing 22
;executing 23
;executing 24
;(10 11 12 13 14 15 16 17 18 19 20 20 21 22 23)
```

厉害！使用惰性 eval，执行次数减少到 18 次(对于主数据源的初始化为- 3 次)。

**第三课——懒惰伴随着创建顶层初始化的开销。更多的懒惰源，更多的开销。**

## 与懒惰有关

*(不重要的一课——IRL = "在现实生活中")*

现在让我们举一个真实世界的例子。我们正在呈现一个包含帖子和更新的用户反馈页面。返回的帖子数量基于某些相关性参数，并进行分页。这些帖子来自多个来源，根据收到的不同信号构建实时反馈。就像配器一样。源可以来自数据库、ML 数据模型、缓存，用一些预置数据回填。所有来源都有检索数据的计算和延迟成本。在伪代码中，它应该是这样的

```
posts = [];
until(posts.length >= limit;
  sourcelist = get-data-sources()
  data = get-data-from-sources()
  remdata = data.slice(posts.length - limit - data.length)
  posts.push(remdata))
```

看上去注定要变得懒惰。sourcelist 中的每个条目都是一个生成器，即返回一系列数据，这些数据可以汇集在一起，进行转换并添加到返回的帖子中。应用懒惰原则，看起来所有的方法

```
(defn get-data-sources []
  (lazy-seq
    [#(lazy-positive-numbers 10) ;data generators, can be replaced with actual db calls
      #(lazy-positive-numbers 20)
      #(lazy-positive-numbers 30)]))(defn get-data-from-sources [sourcelist]
  (map #(apply % []) sourcelist))(defn get-posts [limit]
  (->>
    (get-data-sources) ;returns seq of data-sources
    (get-data-from-sources) ;returns a lazy-seq of results
    (apply concat) ;concat all lazy-seq before taking
    (take limit)));Executing should call other data sources only after exhausting the current one(get-posts 15)
;executing 10
;executing 20
;executing 30
;executing 11
;executing 12
;executing 13
;executing 14
;executing 15
;executing 16
;executing 17
;executing 18
;executing 19
;executing 20
;executing 21
;executing 21
;executing 22
;executing 23
;executing 24
;(10 11 12 13 14 15 16 17 18 19 20 20 21 22 23)
```

呜哇！想象一下，执行 40 个调用来检索 15 个条目，唷。

第四课——懒惰有利于从不同的来源填充一个序列。

另一个不明显的重要注意事项是，每个动作都被分解成伪代码中的函数。不用功能就用懒惰会难很多。

**第五课——如果没有功能性，就很难偷懒。**

## 懒惰有多快？

懒惰通常给人一种速度慢的感觉。但事实是这样吗？向输出添加一个微小的仪器

```
(defn eval-not-lazy []
  (time
    (let [result (take 15 (concat (not-lazy-positive-numbers 10) (not-lazy-positive-numbers 20) (not-lazy-positive-numbers 30)))]
      (println result))))(defn eval-lazy []
  (time
    (let [result (take 15 (concat (lazy-positive-numbers 10) (lazy-positive-numbers 20) (lazy-positive-numbers 30)))]
      (println result))))(eval-not-lazy)
;(10 11 12 13 14 15 16 17 18 19 20 20 21 22 23)
;"Elapsed time: 0.571285 msecs"
;nil;(eval-lazy)
;(10 11 12 13 14 15 16 17 18 19 20 20 21 22 23)
;"Elapsed time: 0.415577 msecs"
;nil
```

绝对和非懒版本速度在一个数量级。

![](img/d0ce0b16ba9e0730b3a03db4798b5c63.png)

Source — imgflip.com

**第六课——懒惰不会对速度产生负面影响，它可以和你一样，甚至更好**

## 并发和懒惰

现在，让我们看看懒惰对并发执行的反应，以及并发是否甚至可以使用。对于我们的例子，使用`pmap`来进行并行执行。(Clojure 的乐趣:)。

```
(defn parallel-get-data-from-sources [sourcelist]
  (pmap #(apply % []) sourcelist))(defn parallel-get-posts [limit]
  (->>
    (get-data-sources) ;returns seq of data-sources
    (parallel-get-data-from-sources) ;returns a lazy-seq of results
    (apply concat) ;concat all lazy-seq before taking
    (take limit)))(parallel-get-posts 15)
;executingexecuting  1020

;executing 30
;executing 11
;executing 12
;executing 13
;executing 14
;executing 15
;executing 16
;executing 17
;executing 18
;executing 19
;executing 20
;executing 21
;executing 21
;executing 22
;executing 23
;executing 24
;(10 11 12 13 14 15 16 17 18 19 20 20 21 22 23)
```

是的，它可以被使用，但是它没有意义，因为我们正在填充一个序列，除非有可以并行发生的初始化。

**第七课——并发不影响懒惰，顺序按顺序填充**

## 明白了

像所有的锤子一样，这是一把为特殊的钉子服务的锤子，理解它什么时候不应该被使用是很好的。许多核心 Clojure 函数，如`take`、`map`、`repeat`等。返回懒惰序列。在整组表达式都要求值的情况下，懒惰要短路求值全部。

在我们的例子中，短路可以由`doall`触发

```
(->>
  (concat
    (lazy-positive-numbers 10)
    (lazy-positive-numbers 20)
    (lazy-positive-numbers 30))
  (doall)
  (take 15))
;executing 10
;executing 20
;executing 30
;executing 11
;executing 12
;executing 13
;executing 14
;executing 15
;executing 16
;executing 17
;executing 18
;executing 19
;executing 20
;executing 21
;executing 21
;executing 22
;executing 23
;executing 24
;executing 25
;executing 26
;executing 27
;executing 28
;executing 29
;executing 30
;executing 31
;executing 31
;executing 32
;executing 33
;executing 34
;executing 35
;executing 36
;executing 37
;executing 38
;executing 39
;executing 40
;executing 41
;(10 11 12 13 14 15 16 17 18 19 20 20 21 22 23)
```

如果我们错过了`doall`，所有的表达将不会被评估。

**第八课—警惕核心 Clojure 函数—** `**map != mapv**` **、** `**filter != filterv**` **等等**

**第九课——用** `**doall**` **短路懒惰，评估所有**

总之，“懒”，你是牛逼的。

上述经验来自 Swym 公司的一次内部开发/工程讲座。这个演讲对 Clojure 的功能模式有一个更广泛的概述，我希望在我的下一篇文章中讨论。

特别感谢 [Saumitra](https://medium.com/u/112fc50018ce?source=post_page-----252ca7fc4fa7--------------------------------) 、 [Supritha](https://medium.com/u/f533d3b186c4?source=post_page-----252ca7fc4fa7--------------------------------) 和 Shivam 帮助使这篇文章看起来很好。希望你喜欢阅读它，就像我们喜欢一起破解它一样。请随意发表你的想法。感谢您的评论和问题！:)

页（page 的缩写）也许是最好的东西？不抱歉。有点像“希望”的意思。我没这么说。安迪·杜弗兰说过:)

## 参考

1.  https://clojuredocs.org/clojure.core/lazy-seq——关于如何写“懒惰的生产者”的大量例子
2.  [https://clojure.org/reference/lazy](https://clojure.org/reference/lazy)
3.  [http://clojure-doc.org/articles/language/laziness.html](http://clojure-doc.org/articles/language/laziness.html)
4.  [https://medium . com/lazy-eval/lazy-seq-in-clo jure-da 06 f6d 35971](/lazy-eval/lazy-seq-in-clojure-da06f6d35971)
5.  [https://Stuart Sierra . com/2015/08/25/clo jure-donts-lazy-effects](https://stuartsierra.com/2015/08/25/clojure-donts-lazy-effects)
6.  [https://github . com/danielmiladinov/joy-of-clo jure/blob/master/src/joy-of-clo jure/chapter 6/lazy . clj](https://github.com/danielmiladinov/joy-of-clojure/blob/master/src/joy-of-clojure/chapter6/laziness.clj)
7.  [https://noobtuts.com/clojure/being-lazy-in-clojure](https://noobtuts.com/clojure/being-lazy-in-clojure)
8.  [https://practical Li . github . io/clo jure/thinking-functional/lazy-evaluation . html](https://practicalli.github.io/clojure/thinking-functionally/lazy-evaluation.html)
9.  [http://www . thesoftware simpleton . com/blog/2014/09/08/lazy-seq/](http://www.thesoftwaresimpleton.com/blog/2014/09/08/lazy-seq/)