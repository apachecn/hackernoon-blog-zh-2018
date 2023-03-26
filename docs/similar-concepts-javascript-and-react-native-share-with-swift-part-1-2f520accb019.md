# 类似概念 JavaScript 和 React 与 Swift 的本地共享:第 1 部分

> 原文：<https://medium.com/hackernoon/similar-concepts-javascript-and-react-native-share-with-swift-part-1-2f520accb019>

![](img/3bd255eed936f3c2a95b256ae35a0a19.png)

我在 React Native 上读到的大多数博客帖子都是从一个 JavaScript 开发人员的角度来看移动设备的。相反，这将是从一个 iOS 开发人员的角度来看 React Native & JavaScript。

我希望从 iOS & Swift 进入 React Native 的任何其他开发人员会发现这很有用。同样，任何对 Swift 感兴趣的 JS 开发人员也会发现这份参考资料很有用。这样做的目的不是为了强调哪种方式更好或更差，而是为了突出相同点和不同点，以便任何其他开发人员可以方便他们学习。

这也不会涵盖*每一个单独的*的相似性和差异性。本来我是打算接近那个的(对，对！)，但我走上了写百科全书和从不出版的道路，而不是写博客。相似之处也不会是 100%的相似或者 100%的不存在，但大部分是。

# 从 JS，到 iOS，再回到未来

在感觉像是上辈子的时候，我从 CS 专业毕业后，主要在[【MAMP】](https://en.wikipedia.org/wiki/MAMP)&[阿贾克斯](https://en.wikipedia.org/wiki/Ajax_(programming))开发了两年。然后从 2010 年末开始，我几乎只在原生 iOS (Objective-C & Swift)和一点点原生 Android 上工作。直到我决定拓宽我在移动开发各个方面的知识，并在 2018 年年中加入了 [**Highline BETA**](https://highlinebeta.com) 。

起初，回到 JavaScript 就像马蒂·小飞侠回到了 1985 年毕夫帝国的平行世界。这有点像…我依稀认得这个世界，只是不确定自己在哪里。不是说它不好(不像比夫的帝国)，只是没有什么是有意义的，尽管它和我以前在的世界是一样的。

所以在第一天，我的 Highline BETA 工程师同事建议，在深入研究 React Native 和 FRP 之前，我应该研究一下 JavaScript 中的 ES6。

对于任何不知道 ES6 的移动开发人员，这里有一个不用谷歌的快速分解:*基本上 ECMAScript 标准(JS 所基于的)很少更新。这包括从 1999 年到 2009 年的一个很长的空白和/或犹豫，然后是从 2011 年到 2015 年的另一个很长的空白。就好像他们默默地希望 JS 会消失一样…当它显然不会消失的时候，他们最终想出了如何在不引入跨 WWW 的突破性变化的情况下发展标准。他们将标准改名为 ES6，并在连续几年内自由开创了 ES7、ES8、& ES9 的先例。*

也许 JavaScript 中需要习惯的一个核心概念，并不是专门的核心编程概念，而是一个哲学概念，任何事情都可以发生。React Native 的价值观之一是它试图标准化这一点——它试图[标准化一个*练习*](https://code.fb.com/android/react-native-bringing-modern-web-techniques-to-mobile/) 。我将尝试触及与 Swift 类似的 ES6 概念(使用 [Axel Rauschmayer 的列表](http://2ality.com/2015/07/favorite-es6-features.html)作为指南，排名不分先后)，然后我将在后续的博客文章中介绍 React Native。

# 相似的概念

## 1.箭头功能

基本上像*速记语法*，但是针对函数。

声明一个函数，如:

`function foo(param) {}`

相当于在 ES6 中将函数声明为常量:

`const arrowFoo = (param) => {}`

据我所知，arrow 函数源于传统 JavaScript 函数的限制，传统函数阻止您在回调中通过`this`访问对象的范围，即所谓的上下文。更复杂的是执行上下文，它适用于作用域而不是上下文，这阻止了被执行的原始函数绑定到不同的执行上下文。

它令人困惑，我不想深入解释，因为它会劫持这篇已经很长的博文，但是[这个链接很好地解释了它。](http://ryanmorr.com/understanding-scope-and-context-in-javascript)

*因为 Swift 是一种支持多线程平台的语言，所以它在执行上下文方面没有同样的问题。但是它仍然通过小心使用它的等价物来维护线程安全。对于 Swift 版本的* `this` *，* `self` *，我们还是要在闭包中使用* `[weak self]` *来保证可以安全访问。但是这更多的是与安全有关，而不是与任何无法从技术上访问实例方法&属性有关。*

*此外，因为箭头函数通常返回一个函数(或者更好地解释它，箭头函数是以函数为值的常量)，所以它们在 Swift:* 中将常量 [*声明为闭包*](/ios-os-x-development/introduction-to-closures-in-swift-3-1d46dfaf8a20) *时是相似的*

`let arrowFoo = (param: ParamType) -> ReturnType`

## 2.可变和常量变量

JS 曾经有`var`，但是现在它已经被弃用了，取而代之的是`let`(可重赋值)和`const`(可单次赋值)。

`const setOnce = 101`

`let setWhenever = 202`

JS 的`var`和`let`之间存在范围差异，虽然这不值得在快速比较的上下文中深入讨论，但还是值得稍微讨论一下。`let`默认情况下，变量被限制在声明它们的范围内，无论是函数还是`for`循环等等。

*Swift 的等价物是 JS 的逆。* `let` *用于常量，* `var` *关键字用于可重分配变量:*

`let setOnce = 101`

`var setWhenever = 202`

这是一个微小的区别，但却是一个很难打破的习惯，就像驾驶汽车从左舵驾驶到右舵驾驶一样。

*关于 JS 的* `*let*` *的限定范围，在 Swift 函数中* `*var*` *和* `*let*` *都受到范围的限制，或者在闭包中等同于没有在变量前加上* `*__block*` *，或者在声明为私有的对象中。*

## 3.对象文字

在 JavaScript 中，你从来没有真正能够创建对象，至少在某种意义上，你可以在强类型 OOP 语言中用类、构造函数等创建对象。JS 中的对象默认情况下不从一个对象继承，尽管它们可以使用原型继承来通过工厂模式实现继承，比如`create()`。

所有的对象文字本质上都是键值结构。定义对象时，用冒号将键和值分隔开，例如定义咖啡杯:

```
const CoffeeMug = {
	millileters: 300,
	artworkPath: "./images/coffee-mug-logo.png",
	coffeeType: "espresso",
	onRefill: refill()
}
```

正如您在对象中所期望的，您可以为属性定义任何类型，包括其他对象、数组或函数。

*在 Swift 中，这相当于两件事。*

首先，定义对象文字在语法和功能上等同于声明一个字典。此外，Swift 中的枚举或结构可以用来提供比字典更大的灵活性，而且不需要显式的构造器、获取器或设置器。

*其次，隐式设置一个对象，不需要通过类型推断定义其类型。这从一开始就是 JS 的一个特性，但是在 Swift 中，任何被推断的类型都将在其生命周期中保持该类型*。*这在 Swift 中完全有效，如果我们要将它声明为一个字典:*

```
let CoffeeMug = [
	"millileters": "300",
	"artworkPath": "./images/coffee-mug-logo.png",
	"coffeeType": "espresso",
	"onRefill": "refill"
]
```

如果你想将这些值保留为任何类型，你就必须包含类型注释。下面是修改后的版本，将 `millileters` *值设置为 int，* `coffeeType` *设置为枚举类型，* `refill` *设置为闭包:*

```
let refill: () -> () = { /** refill code here… */ }
enum type { case espresso, drip, pourover }let CoffeeMug: [String: Any] = [
	"millileters": 300,
	"artworkPath": "./images/coffee-mug-logo.png",
	"coffeeType": type.espresso,
	"onRefill": refill
]
```

## 4.模板文字

字符串插值最近在 JS 中变得可能，消除了向变量追加+字符串的多余需要。

模板文字是 JavaScript 的字符串插值所需的语法，由``${...}``实现。例如，下面的字符串引用了一个人的身体质量指数:

```
const bmi = weight / heightconst bmiReading = "Your BMI is: `${bmi}`"
```

*Swift 中的对应词是:*

```
let bmi = weight / heightlet bmiReading = "Your BMI is: \(bmi)"
```

## 5.模块

这是 JS 鼓励代码模块化重用的一种方式。我不确定模块之前是什么(script 标签看起来有点相似，但又有些不同)，但对我来说，这似乎是任何现代编程语言的一个要求。或者，也许我在 Swift 的世界里呆得太久了，这是理所当然的。

例如，您可以在它自己的文件中声明和定义一个常量，目的是成为一个外部模块。

```
const ButtonModule = () => { ... }export default ButtonModule
```

`export`关键字也是一种指定内部模块或使用它的其他模块的私有或公共内容的方式。虽然这是你用结构或类得到的那种封装，但是请记住，JS 最好的服务是当它没有被塑造成基于类的语言的时候。这就是为什么模块服务于这里的工作，它适合 JS！

*话虽如此，Swift 并没有完全相同的功能。如果我们按照我之前的假设运行，模块是 JavaScript“鼓励”代码模块化重用的方式，Swift 并没有以同样的方式指导您。*

*Swift 中最接近的比较是框架，但这并不完全符合 JS 模块的用途。尽管框架可能很轻，但它们通常是完整的库，而不是单一的抽象。框架也必须显式导入，尽管这是比较的终点。*

与单一抽象最接近的比较可能是协议。协议定义了行为或外观可能需要什么，而不像结构或类那样存储状态。您创建的任何协议也不必显式导入(除非处理 Objective-C，它从 C 的头文件导入中获得提示)。

## 6.默认& Rest 运算符

**默认**参数在 Swift 中有对应的参数。它们的工作方式是一样的，因为调用者可以省略任何带有默认值的参数。

```
function find(searchTerm, sort=true) {...}//You are able to call this as...
find(“toronto”)
```

*Swift 中的对应词是相同的:*

```
func find(_ searchTerm: String, _ sort: Bool=true) -> Bool 
{...}//You are able to call this as...
find(“toronto”)
```

Rest 允许不确定数量的参数。当一个参数被`…`前置时，这意味着许多参数已经作为数组传入。

```
function outputArgLength(...groupedArguments) {
  return 'output: ' + groupedArguments.length
}outputArgLength(2, 4, 6)
//output: 3
```

更新:

*在 Swift 中，其实有一个和这个等价的叫做 Variadic parameters(我只是不太倾向于用它)。语法略有不同，只是将* `*…*` *附加到类型上，而不是添加到参数名的前面。*

*请记住，Swift 中的参数确实需要类型，因为它是一种强类型语言。虽然这可以采取泛型的形式，而不是原语或对象，但这是这篇博文之外的主题。*

```
func outputArgLength(_ groupedArguments: Int...) -> String {
  return "output: \(groupedArguments.count)"
}outputArgLength(2, 4, 6)
//output: 3
```

## 7.承诺

承诺存在的原因很大程度上是我多年前从 JS 逃到 iOS 的主要原因——以减轻后来被称为(但最初令人沮丧地被接受)回调地狱的痛苦。

承诺是异步回调，可以方便地封装用于捕获或继续流。任何返回承诺的东西都为您提供了使用带有`.then`(传入成功结果)或`.catch`(传入错误)的链接的能力。像这样定义一个承诺:

```
function refill() {
  return new Promise(
    function (resolve, reject) {
	  resolve(result)
	  reject(error)
    }
  )
}
```

如果随后执行了两个或更多的异步调用(嵌套的承诺等价物)会怎样？很方便，错误会一直传播下去，直到错误被处理:

```
refill()
.then(refillResult => { 
  return requestMoreBeans()
})
.then(moreBeansResult => {})
.catch(error => {});
```

*在 Swift 中，闭包填补了异步执行功能的空白。一个闭包本身不是异步的，但是这个需要可以用*[*【GCD】*](https://developer.apple.com/documentation/dispatch)*来填充。下面是一个如何异步执行闭包的基本例子:*

```
DispatchQueue.global().async {
  refill()
  DispatchQueue.main.async {
	//Perform UI related code on the main thread...
  }
}
```

虽然在这个实现中也有可能成为回调地狱的牺牲品，但是回调主线程的习惯，以及遵循良好的设计模式和坚实的原则，缓解了这个问题。

*GCD 本质上是 FIFO，但是如果你需要更多地控制你的队列的依赖和状态，以及暂停和取消操作的能力，还有*[*operation queue*](https://developer.apple.com/documentation/foundation/operationqueue)*。虽然这听起来可能不太直观，因为 OperationQueue 在技术上使用 GCD，而 GCD 是一个较低级别的 API，所以使用 OperationQueue 通常是多余的。因为承诺拥有 3 种状态(待定、已完成、已拒绝)，所以它可能在本质上更符合 OperationQueue，但在语法上更类似于较低级别的 GCD。*

*默认情况下，OperationQueue 中的操作在后台线程上执行，这与 GCD 不同，在 GCD 中您必须指定这一点。两个 GCD & OperationQueue 都提供了直接创建线程的抽象(在 GCD 中，异步和同步的决策留给您)。有了这些抽象，线程管理已经够复杂了，更不用说没有它们了，但它在 mobile 中是必不可少的。如果你在 Swift 工作，你会经常使用这两个库或者其中之一。*

## 8.作为返回类型的函数类型

这需要值得一提，因为声明返回函数的函数的模式在 React & React Native 中非常常见。在 Swift 中可以实现类似的目标，虽然我没有经常使用它，但 React 的启发可能会成为我下次深入 Swift 时尝试类似方法的基础。

JavaScript 中唯一的区别是，您可以将一个常量声明为一个函数，而无需先定义函数(尽管这可以通过 Swift 中的闭包来实现，如前所述):

```
const refillDone = () => {
  //do stuff: refill the coffee, then return how long it took...
  return 0
}function refillToFull(full) {
  return refillDone
}
```

*在 Swift 中，这不要与作为参数的闭包混淆，后者在某种程度上允许函数执行作为参数传入的函数。这里的主要区别在于，当一个函数被返回时，调用者决定何时或者是否执行它。然而，闭包是由函数决定执行的。*

*再用我们的* `refill` *例子。我们将把 refill 定义为返回函数的函数。它返回的函数将是我们定义的另一个函数，叫做* `refillDone` *，它返回一个* `TimeInterval` *表示重新装满咖啡所用的时间:*

```
func refillDone() -> TimeInterval {
  //do stuff: refill the coffee, then return how long it took...
  return 0
}func refill(toFull full: Bool) -> () -> TimeInterval {
  return refillDone
}
```

# 概念不太相似

## 1.扩展运算符

**Spread** 作为一个参数遍历数组中的所有元素(图 a 为参数级的循环)。没有快速的对等物。

```
function sumArguments(x, y, z) {
  //You are appearing within the scope of the function as arguments...
  return x + y + z
}//You are being passed in as an array...
sumArguments([2, 4, 6])
```

## 2.解构

也许更奇怪的 JS 特性之一。它可以提供模式匹配来暗示变量的设置。最适合用在集合结构中，如数组。

Swift 中没有真正的对等物。可能遍历范围的方法是最接近的比较。

根据我的理解，假设你从数组中取出第一个、中间和最后一个元素，你可以像下面这样使用析构:

```
var arrayToUse = [1, 2, 3, 4, 5]
var [head,, middle,, tail] = arrayToUse
console.log(head, middle, tail)
// 1, 3, 5
```

要析构一个对象，可以使用以下语法(为新变量 name 和 city 赋值)，因为如果属性名匹配，则变量名是隐含的:

```
var { name, city } = { name: “dwight”, city: “toronto” };
console.log(name);
// "dwight"
console.log(city);
// "toronto"
```

Swift 开发者特别注意——如果你发现变量声明的方式有任何奇怪之处，你可能会参考析构来寻找答案:[https://hacks . Mozilla . org/2015/05/es6-in-depth-destructing/](https://hacks.mozilla.org/2015/05/es6-in-depth-destructuring/)

## 3.发电机

如果声明时在函数名前面加上了`*`，那么函数就是生成器。

生成器提供了一种使用 next()引用集合中元素的默认方式，非常类似于 LinkedList。所以我想如果你使用类似列表的实现，这可能会删除很多样板代码。

```
function* incrementer() {
  let index = 0
  while(true)
    yield index++
}let gen = incrementer()
const first = gen.next().value
const second = gen.next().value
```

在第 2 部分中，我将采用类似的方法来比较 React Native 和 Swift 之间的任何相似之处。

*特别感谢 Highline BETA 的 Rohan 和 Jeanette 审核了本文的准确性和内容，并感谢整个团队在撰写本文之前对我的教育。*