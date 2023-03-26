# Swift 和 JavaScript 比较片段(1) -基础知识

> 原文：<https://medium.com/hackernoon/swift-and-javascript-comparison-snippets-1-the-basics-ac530d8eeacf>

![](img/b45d414098034d4a99f4b1fbf33b25f3.png)

# 常量和变量

迅速发生的

```
// declare a constant 
let maximumNumberOfLoginAttempts = 10// declare a variable
var currentLoginAttempt = 0// declare multiple constants or multiple variables on a single line, separated by commas
var x = 0.0, y = 0.0, z = 0.0
```

Java Script 语言

```
// declare a constant 
const maximumNumberOfLoginAttempts = 10// declare a variable
var currentLoginAttempt = 0 
// or 
let currentLoginAttempt = 0// declare multiple constants or multiple variables on a single line, separated by commas
var x = 0.0, y = 0.0, z = 0.0
```

# 评论

迅速发生的

```
// This is a comment./* This is also a comment
but is written over multiple lines. */
```

Java Script 语言

```
// This is a comment./* This is also a comment
but is written over multiple lines. */
```

# 数字类型转换

迅速发生的

```
let pi = 3.14159
// Integer and Floating-Point Conversion
let integerPi = Int(pi)
```

Java Script 语言

```
const pi = 3.14159
// Integer and Floating-Point Conversion
const integerPi = parseInt(pi)
```

# 布尔运算

迅速发生的

```
let orangesAreOrange = true
let turnipsAreDelicious = falseif turnipsAreDelicious {
    print("Mmm, tasty turnips!")
} else {
    print("Eww, turnips are horrible.")
}
```

Java Script 语言

```
const orangesAreOrange = true
const turnipsAreDelicious = falseif (turnipsAreDelicious) {
    console.log("Mmm, tasty turnips!")
} else {
    console.log("Eww, turnips are horrible.")
}
```

# 错误处理

迅速发生的

```
func canThrowAnError() throws {
    // this function may or may not throw an error
}
do {
    try canThrowAnError()
    // no error was thrown
} catch {
    // an error was thrown
}
```

Java Script 语言

```
function canThrowAnError() {
    // this function may or may not throw an error
}
try {
    canThrowAnError()
    // no error was thrown
} catch (e) {
    // an error was thrown
}
```

# 更多 [Swift 和 JavaScript 比较片段](/@unbug/swift-and-javascript-comparison-snippets-ab39171b85fd)