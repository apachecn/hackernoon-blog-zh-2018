# 坚实的原则

> 原文：<https://medium.com/hackernoon/solid-principles-530b2cc2badf>

## 和他们背后的基础

![](img/b75aadd8ed607296485bc8f8ab85dd5e.png)

SOLID design is like a wall made of lots of small, cohesive and loosely coupled bricks. Photo by [Jeremy Galliani](https://unsplash.com/@jeremyforlife?utm_source=medium&utm_medium=referral)

## 让我们从极限编程开始

我喜欢将坚实的原则与 XP 原则联系起来。提醒你一下，完整的 XP 链是由价值观、原则和实践组成的——按照这个顺序，从最基本的元素到衍生产品。价值观驱动着我们，定义着我们和我们的行为。原则是符合价值观的某些规则。实践是一些基于原则的活动。没有价值观，原则和实践都没有意义。

## 如果固体只是原则，那么价值观是什么？

在深入这个哲学讨论之前，我想说，当我听到“程序代码”这个词时，我看到了什么心理图像——至少，当我参与领域逻辑实现时。对我来说，程序代码是某种编程语言中业务流程的表示。理想情况下，它们之间有一个[双射体](https://en.wikipedia.org/wiki/Bijection)。这是一种[的业务-IT](https://hackernoon.com/why-you-should-split-the-monolith-e946f57db38c) 对齐，它在比 [SOA](/@wrong.about/how-to-implement-soa-dc6bf08fba9a) 更低的层次上表现出来——在程序代码中。这就是为什么 OOP 比过程化编程更适合这一点:它拥有所有工具来表示现实生活领域中的实体。对，我说的是[对象](https://www.yegor256.com/2014/11/20/seven-virtues-of-good-object.html)。所以[建模正确的抽象](https://hackernoon.com/on-good-domain-decomposition-385ee8ce5a3)，代表现实生活中的过程，显然是面向对象软件开发中最重要的部分。我会说是它的价值观。

## 坚实的原则是必要的和充分的吗？

让我们依次来看一看它们。

**单一责任原则** 比较模糊，[基本上是告诉](https://hackernoon.com/the-secret-behind-the-single-responsibility-principle-e2f3692bae25)对象应该是内聚的。如果一个类是内聚的，如果有一个单一的更高层次的目的，如果它的职责符合它的名字，SRP 就会自然地出现。

**开闭原则**
应用 [OCP](https://hackernoon.com/the-open-closed-principle-c3dc45419784) 的实际结果是，如果任何行为(它是某个类的一部分)可以改变，那么它的实现应该隐藏在一个接口后面，这样你就不必修改那个类的任何代码。这已经有了一个名字——它就是[松耦合](https://en.wikipedia.org/wiki/Loose_coupling)。

**利斯科夫替代原理** 它基本上是一个[多态性](https://en.wikipedia.org/wiki/Polymorphism_(computer_science))的定义，更准确地说是——[分型](https://en.wikipedia.org/wiki/Subtyping)。我两者都喜欢，但是我也喜欢封装和可组合性。那么为什么固体缩写中没有“e”和“c”字母呢？
总而言之，是关于松耦合的，[又是](https://hackernoon.com/liskov-substitution-principle-a982551d584a)。

**接口分离原理** [根据定义](https://hackernoon.com/interface-segregation-principle-bdf3f94f1d11)，它不利于胖接口，那些有很多很多实现类的接口。所以这又是关于松耦合的。

**依赖反转原理** 好了，[这里的](/@wrong.about/dependency-inversion-principle-e402e5b69e70)，除了松耦合，还有一个具体的(和机械的)关于如何模块化你的软件的指导方针。

所以，我在固体中看不到任何革命性的东西。这都是关于低耦合和高内聚的。

## 固体原理和封装

高内聚和低耦合是足够宽泛的概念，因此我认为可以从它们中派生出封装。为了以防万一，我说的[封装](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming))通常也指[信息隐藏](https://en.wikipedia.org/wiki/Information_hiding)，这不是普遍存在的[接受的](http://wiki.c2.com/?EncapsulationIsNotInformationHiding)。
看看下面的代码:

```
**interface** IB
{
    **public function** call(IC $c);
}

**interface** IC
{

}

**class** A
{
    **private $b**;
    **private $c**;
    **private $d**;

    **public function** __construct(IB $b, IC $c, int $d)
    {
        $this->**b** = $b;
        $this->**c** = $c;
        $this->**d** = $d;
    }

    **public function** someBehavior()
    {
        $this->**d**++;
        **return** $this->**b**->call($this->**c**);
    }
}
```

是什么让类 *A* 的对象和接口 *IB* 和 *IC* 高度封装？它们都没有公开它们的内部结构或纯数据:没有对实例变量的公共访问，[没有 getter](https://www.yegor256.com/2014/09/16/getters-and-setters-are-evil.html)，没有常量，没有公共静态属性。相反，所有的内部数据都在内部使用，导致由类 *A* 创建的对象非常[内聚](https://en.wikipedia.org/wiki/Cohesion_(computer_science))。此外，对象 *A* 不需要其他对象的数据。相反，它只依赖于接口 *IB* 和 *IC* 所公开的行为。而类 *A* 并不关心它们的具体实现，这显然驻留在某个其他模块中。这使得整个系统非常松散耦合。

我认为一个类被封装的程度是衡量这个类好坏的一个更实际的标准——以防高内聚和松耦合看起来有点抽象的概念。至少在我看来，封装是一个比实体少得多的模糊不清的概念。

## 坚实的原则和可重用性

最初，OOP 从来都不是关于可重用性的。同样，当您的模型被正确分解时，它自然会出现。可重用性是这种分解的结果。把它放在第一位很难导致正确的抽象和可维护的代码。并且你可以通过阅读一些关于 Smalltalk 的好书来了解 OOP 最初是什么样子的。别担心，它们更多的是关于好的对象设计，而不是 Smalltalk。

## 最后的想法

SOLID 并不是整个 OOP 所基于的一套完整的公理。这是对已经构建好的设计的一个很好的衡量，但可以说不是在实际构建它时要遵循的最佳原则。我认为高内聚和松耦合是更清晰的标志。

此外，人们不应该忘记，每一个都意味着一个*原则*，而不是好软件的核心价值。因此，在没有确定正确的抽象的情况下，完全机械地应用它们是没有任何意义的。