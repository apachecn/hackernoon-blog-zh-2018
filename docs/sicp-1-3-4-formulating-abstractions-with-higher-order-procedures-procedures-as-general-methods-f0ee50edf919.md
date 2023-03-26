# SICP 1.3.4:“用高阶过程公式化抽象:作为一般方法的过程”

> 原文：<https://medium.com/hackernoon/sicp-1-3-4-formulating-abstractions-with-higher-order-procedures-procedures-as-general-methods-f0ee50edf919>

## (计算机程序的结构和解释)

我的 1.3.4 习题解答也在 Github 这里:[https://Github . com/bold UCP/SICP/tree/master/exercises/chapter _ 01/1.3 _ exercises](https://github.com/bolducp/SICP/tree/master/exercises/chapter_01/1.3_exercises)

本节关于高阶过程(1.3)的最后一小节(1.3.4)关注的是不仅将过程用作参数，而且将其用作过程的返回值的有用性。我们通过前面章节中的`sqrt`和`newtons-method`函数的转换，使用返回其他过程的过程来实现。这种比较说明的一个主要优点是，从这种风格的[编程](https://hackernoon.com/tagged/programming)构建的代码变得非常清晰。

![](img/ad1f120e49e2513fae15ed2098a215a9.png)

本节还花了一些时间解释在实现平方根和牛顿法的过程中寻找导数和平均值的数学方程。它还包含了一个有用的、简明的术语定义，这个术语在讨论编程语言时仍然经常被抛出，即关于元素类型的“一级状态”(pgs 76–77)。

最后，我想指出这一段的精华，我认为它很好地阐明了为什么这一整章对高阶过程的关注，实际上对我们每天编写的代码很重要:

> 作为程序员，我们应该注意识别程序中底层抽象的机会，并在此基础上构建和概括它们以创建更强大的抽象。这并不是说一个人应该总是尽可能用最抽象的方式编写程序；专业程序员知道如何选择适合他们任务的抽象层次。但是，能够根据这些抽象进行思考是很重要的，这样我们就可以准备好在新的环境中应用它们。高阶过程的意义在于，它们使我们能够将这些抽象明确地表示为编程语言中的元素，这样它们就可以像其他计算元素一样被处理。(第 76 页)

# 练习

我的答案在每个练习题下面的灰色文本块中提供。

## 练习 1.40

> 定义一个[程序](https://hackernoon.com/tagged/procedure) `cubic`，它可以和`newtons-method`程序一起用于(牛顿法(立方 a b c) 1)形式的表达式中。为了逼近三次 x^3 + ax^2 + bx + c 的零点

```
(define (fixed-point f first-guess)
  (define (close-enough? v1 v2)
    (< (abs (- v1 v2))
       0.0001))
  (define (try guess)
    (let ((next (f guess)))
      (if (close-enough? guess next)
          next
          (try next))))
  (try first-guess))(define dx 0.00001)(define (deriv g)
  (lambda (x)
    (/ (- (g (+ x dx)) (g x))
       dx)))(define (newton-transform g)
  (lambda (x)
    (- x (/ (g x)
            ((deriv g) x)))))(define (newtons-method g guess)
  (fixed-point (newton-transform g)
               guess))(define (cube x)
   (* x x x))(define (cubic a b c)
   (lambda (x)
     (+ (cube x)
        (* a (square x))
        (* b x)
        c)))(newtons-method (cubic 2 -5 7) 1)
```

## 练习 1.41

> *定义一个过程* `*double*` *，该过程以一个参数的过程为参数，返回一个应用原过程两次的过程。例如，如果* `*inc*` *是一个为其参数加 1 的过程，那么* `*(double inc)*` *应该是一个加 2 的过程。*返回什么值
> 
> *(((double(double double))Inc)5)*

```
(define (inc n) (+ n 1))(define (double f)
        (lambda (x) (f (f x))))(((double (double double)) inc) 5) = 21
```

## 练习 1.42

> 设 f 和 g 是两个单参数函数。g 后的*合成* f 定义为函数 x ↦ f(g(x))。定义一个实现组合的过程`compose`。例如，如果`inc`是一个参数加 1 的过程:((compose square inc) 6)

```
(define (inc n) (+ n 1))
(define (square n) (* n n))(define (compose f g)
    (lambda (x) (f (g x))))((compose square inc) 6) = 49
```

## 练习 1.43

> 如果 f 是一个数值函数，n 是一个正整数，那么我们可以形成 f 的第 n 个重复应用，定义为在 x 处的值为 f 的函数(f(…(f(x))…))。例如，如果 f 是函数 x↦x+1，那么 f 的第 n 次重复应用是函数 x↦x+n.如果 f 是平方一个数的运算，那么 f 的第 n 次重复应用是将其自变量提升到 2n 次幂的函数。编写一个过程，将计算 f 和正整数 n 的过程作为输入，并返回计算 f 的第 n 次重复应用的过程。您的过程应该能够如下使用:((重复平方 2) 5)
> *625*

```
(define (compose f g)
  (lambda (x) (f (g x))))(define (repeated f n)
  (if (> n 1)
    (compose f (repeated f (- n 1)))
    f))((repeated square 2) 5) = 625
```