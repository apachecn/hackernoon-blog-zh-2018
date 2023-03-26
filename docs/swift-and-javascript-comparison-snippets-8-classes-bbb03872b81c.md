# Swift 和 JavaScript 比较片段(8)-类别

> 原文：<https://medium.com/hackernoon/swift-and-javascript-comparison-snippets-8-classes-bbb03872b81c>

![](img/8806efae2eed68d0053cfda221fe950f.png)

迅速发生的

```
// class definition
class Counter {
    var count = 0
    func increment() {
        count += 1
    }
    func increment(by amount: Int) {
        count += amount
    }
    func reset() {
        count = 0
    }
}// class instance
let counter = Counter()
// the initial count value is 0
counter.increment()
// the count's value is now 1
counter.increment(by: 5)
// the count's value is now 6
counter.reset()
// the count's value is now 0print("The count property value is \(counter.count)")
```

Java Script 语言

```
// class definition
class Counter {
    contructor() {
        this.count = 0
    }
    function increment() {
        this.count += 1
    }
    function increment(amount) {
        this.count += amount
    }
    function reset() {
        this.count = 0
    }
}// class instance
let counter = Counter()
// the initial count value is 0
counter.increment()
// the count's value is now 1
counter.increment(5)
// the count's value is now 6
counter.reset()
// the count's value is now 0console.log(`The count property value is ${counter.count}`)
```

# 更多 [Swift 和 JavaScript 比较片段](/@unbug/swift-and-javascript-comparison-snippets-ab39171b85fd)