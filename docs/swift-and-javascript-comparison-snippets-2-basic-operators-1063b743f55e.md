# Swift 和 JavaScript 比较片段(2) —基本运算符

> 原文：<https://medium.com/hackernoon/swift-and-javascript-comparison-snippets-2-basic-operators-1063b743f55e>

![](img/b45d414098034d4a99f4b1fbf33b25f3.png)

# 赋值运算符

迅速发生的

```
let b = 10
var a = 5
a = b// decomposed tuple with multiple values into multiple variables
let (x, y) = (1, 2)
print("x = \(x), y = \(y)") // x = 1, y = 2
```

Java Script 语言

```
let b = 10
var a = 5
a = b// object matching with destructuring assignment
const {x, y} = {x:1, y:2}
console.log(`x = ${x}, y = ${y}`) // x = 1, y = 2
// or array matching with destructuring assignment
const [x, y] = [1, 2]
console.log(`x = ${x}, y = ${y}`) // x = 1, y = 2
```

# 算术运算符

*   加法(+)
*   减法(-)
*   乘法(*)
*   除法(/)

迅速发生的

```
1 + 2       // equals 3
5 - 3       // equals 2
2 * 3       // equals 6
10.0 / 2.5  // equals 4.0
"hello, " + "world"  // equals "hello, world"
```

Java Script 语言

```
1 + 2       // equals 3
5 - 3       // equals 2
2 * 3       // equals 6
10.0 / 2.5  // equals 4
"hello, " + "world"  // equals "hello, world"
```

# 余数运算符

迅速发生的

```
9 % 4    // equals 1
-9 % 4   // equals -1
```

Java Script 语言

```
9 % 4    // equals 1
-9 % 4   // equals -1
```

# 一元减/加运算符

迅速发生的

```
let three = 3
let minusThree = -three       // minusThree equals -3
let plusThree = -minusThree   // plusThree equals 3, or "minus minus three"let minusSix = -6
let alsoMinusSix = +minusSix  // alsoMinusSix equals -6
```

Java Script 语言

```
const three = 3
const minusThree = -three       // minusThree equals -3
const plusThree = -minusThree   // plusThree equals 3, or "minus minus three"const minusSix = -6
const alsoMinusSix = +minusSix  // alsoMinusSix equals -6
```

# 复合赋值运算符

迅速发生的

```
var a = 1
a += 2 // a is now equal to 3
```

Java Script 语言

```
let a = 1
a += 2 // a is now equal to 3
```

# 比较运算符

*   等于(a == b)
*   不等于(a！= b)
*   大于(a > b)
*   小于(a < b)
*   Greater than or equal to (a > = b)
*   小于或等于(一个<= b)
*   Identity operators, refer to the same object instance (a === b)
*   Identity operators, not refer to the same object instance (a !== b)

Swift

```
1 == 1   // true because 1 is equal to 1
2 != 1   // true because 2 is not equal to 1
2 > 1    // true because 2 is greater than 1
1 < 2    // true because 1 is less than 2
1 >= 1   // true because 1 is greater than or equal to 1
2 <= 1   // false because 2 is not less than or equal to 1let name = "world"
if name == "world" {
    print("hello, world")
} else {
    print("I'm sorry \(name), but I don't recognize you")
}
// Prints "hello, world", because name is indeed equal to "world".let p1 = Person();
let p2 = Person();
p1 === p2 // false
p1 !== p2 // true
```

JavaScript

```
1 == 1   // true because 1 is equal to 1
2 != 1   // true because 2 is not equal to 1
2 > 1    // true because 2 is greater than 1
1 < 2    // true because 1 is less than 2
1 >= 1   // true because 1 is greater than or equal to 1
2 <= 1   // false because 2 is not less than or equal to 1const name = "world"
if (name == "world") {
    console.log("hello, world")
} else {
    console.log(`I'm sorry ${name}, but I don't recognize you`)
}
// Prints "hello, world", because name is indeed equal to "world".const p1 = new Person();
const p2 = new Person();
p1 === p2 // false
p1 !== p2 // true
```

# Ternary Conditional Operator

Swift

```
let contentHeight = 40
let hasHeader = true
let rowHeight = contentHeight + (hasHeader ? 50 : 20)
// rowHeight is equal to 90
```

JavaScript

```
const contentHeight = 40
const hasHeader = true
const rowHeight = contentHeight + (hasHeader ? 50 : 20)
// rowHeight is equal to 90
```

# Logical Operators

*   Logical NOT (!a)
*   Logical AND (a && b)
*   Logical OR (a || b)

Swift

```
let allowedEntry = false
if !allowedEntry {
    print("ACCESS DENIED")
}
// Prints "ACCESS DENIED" let hasDoorKey = false
let knowsOverridePassword = true
if enteredDoorCode && passedRetinaScan || hasDoorKey || knowsOverridePassword {
    print("Welcome!")
} else {
    print("ACCESS DENIED")
}
// Prints "Welcome!"
```

JavaScript

```
const allowedEntry = false
if (!allowedEntry) {
    console.log("ACCESS DENIED")
}
// Prints "ACCESS DENIED" const hasDoorKey = false
const knowsOverridePassword = true
if (enteredDoorCode && passedRetinaScan || hasDoorKey || knowsOverridePassword) {
    console.log("Welcome!")
} else {
    console.log("ACCESS DENIED")
}
// Prints "Welcome!"
```

# More of [Swift 和 JavaScript 的比较片段](/@unbug/swift-and-javascript-comparison-snippets-ab39171b85fd)