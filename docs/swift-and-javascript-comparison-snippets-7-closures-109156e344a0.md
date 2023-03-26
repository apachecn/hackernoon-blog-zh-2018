# Swift 和 JavaScript 比较片段(7)——闭包

> 原文：<https://medium.com/hackernoon/swift-and-javascript-comparison-snippets-7-closures-109156e344a0>

![](img/9ed154c3c33e7e9404f239263f4dd415.png)

迅速发生的

```
// Closure Expressions
// The Sorted Method
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
func backward(_ s1: String, _ s2: String) -> Bool {
    return s1 > s2
}
var reversedNames = names.sorted(by: backward)
// reversedNames is equal to ["Ewa", "Daniella", "Chris", "Barry", "Alex"]
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
    return s1 > s2
})
// or
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in return s1 > s2 } )
```

Java Script 语言

```
// Closure Expressions
// The Sort Method
const names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
function backward(s1, s2) {
    return s1 < s2
}
let reversedNames = names.sort(backward)
// reversedNames is equal to ["Ewa", "Daniella", "Chris", "Barry", "Alex"]
reversedNames = names.sort((s1, s2) => {
    return s1 < s2
})
// or
reversedNames = names.sort((s1, s2) => return s1 < s2)
```

# 更多 [Swift 和 JavaScript 比较片段](/@unbug/swift-and-javascript-comparison-snippets-ab39171b85fd)