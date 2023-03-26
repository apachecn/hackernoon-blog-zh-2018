# Clojure 宏——未言符号的教训

> 原文：<https://medium.com/hackernoon/clojure-macros-lessons-from-unspoken-symbols-c4945d8ed8bf>

在
(macroexpand `(not-really-macro "test"))
;test
;"test"

(not-really-macro "test")
;test
;"test"

`*not-really-macro*`只是一个 println 代码，只要你调用它就会执行。所以不是真正的宏观。继续前进

**第 0 课——不是所有用** `***defmacro***` **定义的都可以称为宏。**

## 也许是一个宏

```
;; without list, using syntax quoting
(defmacro wrong-macro [a] `(do (println a) a))

(macroexpand `(wrong-macro "test"))
;(do (clojure.core/println user/a) user/a)

(wrong-macro "test")
;CompilerException java.lang.RuntimeException: No such var: user/a, ...
```

如果成功的话可能是个宏。错误很明显，在*用户*名称空间中没有定义 *a* 。这很奇怪，因为我认为它来自宏的输入参数，对吗？嗯，如果我转换名称空间并尝试扩展会怎么样

```
(ns outerspace)
;nil
;in outerspace now

(macroexpand `(user/wrong-macro "test"))
;(do (clojure.core/println user/a) user/a)
```

这很有趣。`*a*`的命名空间没变。所以宏展开并没有获得传递的参数。

## 进入未引用状态—变量捕获

由于我*引用了*代码，我需要`*unquote*`来访问`*quote*`外的[符号](https://hackernoon.com/tagged/symbols)。

```
(in-ns 'user)
(defmacro ok-macro [a] `(do (println ~a) ~a))
;#'user/ok-macro

(macroexpand `(ok-macro "test"))
;(do (clojure.core/println "test") "test")

(ok-macro "test")
;"test"
;test
```

一双**呜呜**！瞬间，无论是宏观扩张还是实际产出都是正确的。

**第 1 课—在*引用*代码时，使用*取消引用*访问外部引用。**

## 更多的不引用——比喻性的和“字面上的”

于是，就有了 clojure 函数`*quote*`、`*unquote*`和`*unquote-splicing*`。试图在宏中使用它们，

```
(defmacro forcequote-macro [a] (quote (do (println ~a) ~a)))
;#'user/forcequote-macro

(macroexpand `(forcequote-macro "test"))
;(do (println (clojure.core/unquote a)) (clojure.core/unquote a))

(forcequote-macro "test")
;CompilerException java.lang.RuntimeException: Unable to resolve symbol: a in this context...
```

啊？为什么不呢？

**第二课——“不是*引用*的字面捷径。**

为了证实第二课是真的，

```
(= 'a (quote a))
;true

(= `a (quote a))
;false
```

成功！算是吧。

**第 3 课—‘是*引用*的文字捷径。**

现在，为`*unquote*`做同样的事情。

```
(defmacro forceunquote-macro [a] `(do (println (unquote a)) (unquote a)))
;#'user/forceunquote-macro

(macroexpand `(forceunquote-macro "test"))
;(do (clojure.core/println (clojure.core/unquote user/a)) (clojure.core/unquote user/a))

(forceunquote-macro "test")
;CompilerException java.lang.RuntimeException: No such var: user/a, compiling...
```

嗯，这种“疯狂”似乎有一种模式(或者我这么称呼它)。

第 4 课— ~不是*的字面捷径。*

因此，当引用调用时，取消引用和引用不起作用，这意味着识别宏中使用的符号已经太晚了。必须使用文字，这是毫无疑问的。

## 符号生成

如果问题出在符号上，为什么不在宏中按需生成一个符号并引用它呢？开始了

```
;; gensym
(defmacro sym-gen-macro [a]
  (let [dyn-a (gensym a)]
    `(let [~dyn-a ~a]
       (println ~dyn-a)
       ~dyn-a)))
;#'user/sym-gen-macro

(macroexpand `(sym-gen-macro "test"))
;(let* [test1663 "test"] (clojure.core/println test1663) test1663)

(sym-gen-macro "test")
;test
;"test"
```

为了营救，宏起作用了！但这似乎不对。这是可行的，但代价是什么。每次调用宏时，都会创建一个新的符号，并且引用被更新为 *let* 中的 *a* 。所以不，绝对不是。

**第五课—** `***gensym***` **无法解决你的不引用问题。**

## 使用源

在确认无法摆脱这些神秘的文字之后，我们转向了拼接。在使用整个要传递的参数时非常强大

```
(defmacro expand-body [& body]
   `(println ~@body))
;#'user/expand-body

(macroexpand `(expand-body "test1" "test2"))
;(clojure.core/println "test1" "test2")

(expand-body "test1" "test2")
;test1 test2
;nil
```

工作得很好，现在找到窍门了。现在我尝试使用这里给出的定义。如果没有文字，它不会像预期的那样工作，但我会感到一种粗鲁但有趣的震惊。

```
(source unquote)
;(def unquote)
;nil

(source unquote-splicing)
;(def unquote-splicing)
;nil

(source macroexpand))
;(defn macroexpand
;  "Repeatedly calls macroexpand-1 on form until it no longer
;  represents a macro form, then returns it.  Note neither
;  macroexpand-1 nor macroexpand expand macros in subforms."
;  {:added "1.0"
;   :static true}
;  [form]
;    (let [ex (macroexpand-1 form)]
;      (if (identical? ex form)
;        form
;        (macroexpand ex))))
;nil

(source quote)
;Source not found
;nil
```

如你所见，*不引用*和*不引用拼接*是`*def*` *'* s 只是符号，不像其他的`*defn*`s。所以，我当然不能用它们代替文字，咄。

第六课——不是所有的字面量都有对应的 `***defn***` **。**

**第 7 课—使用~@获取宏内部展开的参数列表**

额外的 —试试`(source defn)`，这是一本有趣的读物。

## 进入内部循环—创建内部参数

我尝试添加一个新的符号，它将被添加到输入字符串的前面。

```
(defmacro innersym-macro [a]
  `(let [dyn-a# (str "Prepend-" ~a)]
     (println dyn-a#)
     dyn-a#))
;#'user/innersym-macro

(macroexpand `(innersym-macro "test"))
;(let* [dyn-a__1749__auto__ (clojure.core/str "Prepend-" "test")] (clojure.core/println dyn-a__1749__auto__) dyn-a__1749__auto__)

(innersym-macro "test")
;Prepend-test
;"Prepend-test"
```

这简直太棒了。

**第 8 课—使用# —在*引号* -d 代码块内创建符号，也称为 *autogensym* 。**

## 请给我来点破坏性的。拜托了。

得寸进尺，我试着去破坏内层参数。

```
(defmacro innerdestructure-macro [a]
  `(let [{:keys [prepend#] :as aprepender#} {:prepend "Prependtext" :append "Appendtext"}
         dyn-a# (str prepend# ~a (:append aprepender#))]
     (println dyn-a#)
     dyn-a#))
;#'user/innerdestructure-macro

(macroexpand `(innerdestructure-macro "test"))
;(let* [map__1853 {:prepend "Prependtext", :append "Appendtext"} map__1853 (if (clojure.core/seq? map__1853) (clojure.lang.PersistentHashMap/create (clojure.core/seq map__1853)) map__1853) aprepender__1844__auto__ map__1853 prepend__1843__auto__ (clojure.core/get map__1853 :prepend__1843__auto__) dyn-a__1845__auto__ (clojure.core/str prepend__1843__auto__ "test" (:append aprepender__1844__auto__))] (clojure.core/println dyn-a__1845__auto__) dyn-a__1845__auto__)

(innerdestructure-macro "test")
;testAppendtext
;"testAppendtext"
```

析构不起作用，因为`*prepend#*`被当作零处理，但是直接的 get 键工作得很好。

**第九课——析构在第一级引用代码块中不起作用**

## 利用实习生

现在，使用到目前为止学到的令人敬畏的宏技能，我冒险在执行宏时在名称空间内创建动态符号。

```
(defmacro interning-macro [a]
  `(let [{:keys [prepend#] :as aprepender#} {:prepend "Prependtext" :append "Appendtext"}
         dyn-a# (str prepend# ~a (:append aprepender#))]
     (intern
      *ns*
      '~'ooh-fn
      (fn [oohargs#]
        (println oohargs# dyn-a#)
        oohargs#))))
;#'user/interning-macro

(macroexpand `(interning-macro "test"))
;(let* [map__1995 {:prepend "Prependtext", :append "Appendtext"} map__1995 (if (clojure.core/seq? map__1995) (clojure.lang.PersistentHashMap/create (clojure.core/seq map__1995)) map__1995) aprepender__1985__auto__ map__1995 prepend__1984__auto__ (clojure.core/get map__1995 :prepend__1984__auto__) dyn-a__1986__auto__ (clojure.core/str prepend__1984__auto__ "test" (:append aprepender__1985__auto__))] (clojure.core/intern clojure.core/*ns* (quote ooh-fn) (clojure.core/fn [oohargs__1987__auto__] (clojure.core/println oohargs__1987__auto__ dyn-a__1986__auto__) oohargs__1987__auto__)))

(ns outerspacestar)
(user/interning-macro "star")
;#'outerspacestar/ooh-fn

(ns outerspaceplanet)
(user/interning-macro "planet")
;#'outerspaceplanet/ooh-fn

(in-ns 'user)
(outerspacestar/ooh-fn {:a 10})
;{:a 10} starAppendtext
;{:a 10}

(outerspaceplanet/ooh-fn {:b 20})
;{:b 20} planetAppendtext
;{:b 20}
```

太棒了。当宏被实例化时有一些内部引用。这对于创建配置变化的可重复模块非常方便。这里面有很多教训

**第 10 课—** `***ns***` **—指代码正在执行的当前名称空间。**

**第十一课—** `***(intern somens '~'symname <<symdefinition>>)***` **相当于在那个 *somens* namespace *里面加了*** `***(def symname symdefinition)***` **。**

**第十二课—** `***(def x (fn []))* = *(defn x [])***` **。**

**额外的** —如果你还没有检查[协议](https://clojure.org/reference/protocols)，请在某个时候检查。

## 命名空间内部实习生命名空间内部宏内部

现在，就我的理解来说，在宏生成的当前命名空间的内部加载一个命名空间怎么样

```
(defn resolvable-fn1 []
  (println "resolved1"))
;#'user/resolvable-fn1

(defn resolvable-fn2 []
  (println "resolved2"))
;#'user/resolvable-fn2

(defmacro interning-resolve-macro [a]
  `(let [{:keys [prepend#] :as aprepender#} {:prepend "Prependtext" :append "Appendtext"}
         dyn-a# (str prepend# ~a (:append aprepender#))]
     (intern
      *ns*
      '~'resolvens-fn
      (fn [rargs#]
        ((ns-resolve (symbol "user") (symbol "resolvable-fn1")))
        ((ns-resolve '~'user '~'resolvable-fn2))
        (println rargs# dyn-a#)
        rargs#))))
#'user/interning-resolve-macro

(macroexpand `(interning-resolve-macro "test"))
;(let* [map__2276 {:prepend "Prependtext", :append "Appendtext"} map__2276 (if (clojure.core/seq? map__2276) (clojure.lang.PersistentHashMap/create (clojure.core/seq map__2276)) map__2276) aprepender__2266__auto__ map__2276 prepend__2265__auto__ (clojure.core/get map__2276 :prepend__2265__auto__) dyn-a__2267__auto__ (clojure.core/str prepend__2265__auto__ "test" (:append aprepender__2266__auto__))] (clojure.core/intern clojure.core/*ns* (quote resolvens-fn) (clojure.core/fn [rargs__2268__auto__] ((clojure.core/ns-resolve (clojure.core/symbol "user") (clojure.core/symbol "resolvable-fn1"))) ((clojure.core/ns-resolve (quote user) (quote resolvable-fn2))) (clojure.core/println rargs__2268__auto__ dyn-a__2267__auto__) rargs__2268__auto__)))

(ns outerspacestar)
(user/interning-resolve-macro "star")
#'outerspacestar/resolvens-fn

(ns outerspaceplanet)
(user/interning-resolve-macro "planet")
#'outerspaceplanet/resolvens-fn

(in-ns 'user)
(outerspacestar/resolvens-fn {:a 100})
;resolved1
;resolved2
;{:a 100} starAppendtext
;{:a 100}

(outerspaceplanet/resolvens-fn {:b 200})
;resolved1
;resolved2
;{:b 200} planetAppendtext
;{:b 200}
```

除了前面提到的神奇之处，解析符号可以用有趣的方式来获得成功的结果。

**第十三课—**`***(symbol "xyz")***`***=***`***'xyz***`**。**

**第十四课—在宏观世界—**`***(symbol "xyz")***`***=***`***'~'xyz***`***。***

**第 15 课—在调用** `***ns-resolve***` **之前，需要加载 ns。所以在使用** `**ns-resolve**` **之前最好先做一个** `***(require 'nssymbol)***` **。**

一口气看到所有这些动态符号有点混乱。让我试试`*macroexpand-1*` *。*

```
(macroexpand-1 `(interning-resolve-macro "test"))

;(clojure.core/let [{:as aprepender__2266__auto__, :keys [prepend__2265__auto__]} {:prepend "Prependtext", :append "Appendtext"} dyn-a__2267__auto__ (clojure.core/str prepend__2265__auto__ "test" (:append aprepender__2266__auto__))] (clojure.core/intern clojure.core/*ns* (quote resolvens-fn) (clojure.core/fn [rargs__2268__auto__] ((clojure.core/ns-resolve (clojure.core/symbol "user") (clojure.core/symbol "resolvable-fn1"))) ((clojure.core/ns-resolve (quote user) (quote resolvable-fn2))) (clojure.core/println rargs__2268__auto__ dyn-a__2267__auto__) rargs__2268__auto__)))
```

更整洁。

**第 16 课—** `**macroexpand-1**` **进行 1 级扩展，macroexpand 进行各级扩展，每级扩展。单身。宏观。**

## 推断

从经验教训中总结出的一些良好做法

1.  在引用的块内保持析构到一个极限，以避免混淆。
2.  将核心功能放在宏之外，放在宏之外。不需要在每次加载宏时都创建这些函数
3.  对于在名称空间上初始化一群实习生的重量级宏，将实习生和实际实例化的数量保持在一个合理的限度内。这个数字越大，启动时间可能会变得越慢。
4.  另一方面，如果启动时间增加，看看强制加载宏的*需求*，并帮助减少到唯一强制的启动名称空间。
5.  如果你正在做大量的实习 ns 世代，可能是时候给[协议](https://clojure.org/reference/protocols)看一看，看看那是否工作得更好。有一点点可能你会喜欢:)

宏在很多方面都很强大，允许数据变成代码、可执行文件等等。但是(当然有“但是”)围绕它的文档有点神秘，而且(因为没有更好的词)不简单。希望从这篇文章中得到的教训能帮助那些未入门的人揭开一些神秘的面纱。

我确信我遗漏了一两点，所以请在下面的评论区随时纠正我。此外，请在下面分享您使用 Clojure 宏的经验，我们很乐意了解它们！

***参考资料和有用链接***

1.  [https://www.braveclojure.com/writing-macros/](https://www.braveclojure.com/writing-macros/)—面对宏音乐(即来自宏的错误)时深刻而有用
2.  [https://stack overflow . com/questions/3667403/defn-and-def macro 的区别是什么](https://stackoverflow.com/questions/3667403/what-is-the-difference-between-defn-and-defmacro)
3.  [http://stack overflow . com/questions/4571042/can-someone-explain-clojures-un quote-splice-in-simple-terms](http://stackoverflow.com/questions/4571042/can-someone-explain-clojures-unquote-splice-in-simple-terms)
4.  [https://clojure.org/reference/special_forms](https://clojure.org/reference/special_forms)
5.  [https://clojure.org/reference/protocols](https://clojure.org/reference/protocols)
6.  [https://www . braveclojure . com/multi methods-records-protocol/](https://www.braveclojure.com/multimethods-records-protocols/)——又一篇来自 brave clojure 的文章，里面充满了 clo jure 的美好！