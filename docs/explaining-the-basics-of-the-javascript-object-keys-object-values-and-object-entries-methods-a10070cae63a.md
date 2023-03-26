# 解释 JavaScript Object.keys、Object.values 和 Object.entries 方法的基础知识

> 原文：<https://medium.com/hackernoon/explaining-the-basics-of-the-javascript-object-keys-object-values-and-object-entries-methods-a10070cae63a>

![](img/8407388e7c60d4ee39b828c0a9561deb.png)

当处理数据时，我们可能需要将一个对象转换成一个数组。这些方法让我们很容易做到这一点。

首先，我们得到一个包含美国各州及其首都列表的对象。

```
const states = {
  "AZ": "Phoenix",
  "NY": "Albany",
  "VA": "Richmond",
  "Wisconsin": "Madison"
}
```

**Object.keys()**

如果要求我们返回所有的键(Javascript 对象被写成“key: value”对)，我们将使用 Object.keys()方法。

```
function displayKeys(){

  const states = {
    "AZ": "Phoenix",
    "NY": "Albany",
    "VA": "Richmond",
    "Wisconsin": "Madison"
  }return Object.keys(states); // We pass the object as the argument.// This statement will return an array of [ 'AZ', 'NY', 'VA',      // 'Wisconsin' ].
}displayKeys(); // Function Call
```

够简单吗？

**Object.values()**

Object.values 是一个与 Object.keys 非常相似的方法，但是它返回一个数组，其中包含提交对象的*值*，而不是键。

(关键:->价值)

这是它实际运行的样子。

```
function displayValues(){

const states = {
    "AZ": "Phoenix",
    "NY": "Albany",
    "VA": "Richmond",
    "Wisconsin": "Madison"
  }

return Object.values(states); // We pass the object as the argument.// This statement will return an array of [ 'Phoenix', 'Albany',    // 'Richmond', 'Madison' ].}displayValues(); // Function Call
```

**Object.entries()**

最后，我们有 Object.entries 方法，它与前两个方法略有不同。Object.entries 不选择键-值对的任何特定部分，而是转换键和值。这里的区别是 Object.entries 返回数组的数组，而 Object.keys 和 Object.values 方法都返回单个数组。

在这里，使用 Object.entries 方法，有一个数组包装了整个翻译后的对象，对象中的每个键-值对都位于它自己单独的子数组中。

这是一个将被返回的数组中的数组的例子。

```
[ [ 'AZ', 'Phoenix' ],
  [ 'NY', 'Albany' ],
  [ 'VA', 'Richmond' ],
  [ 'Wisconsin', 'Madison' ] ]
```

下面是 Object.entries 方法的完整示例。

```
function displayEntries(){

const states = {
    "AZ": "Phoenix",
    "NY": "Albany",
    "VA": "Richmond",
    "Wisconsin": "Madison"
  }

return Object.entries(states);// We pass the object as the argument.
// This statement will return an array of:
// [ [ 'AZ', 'Phoenix' ], [ 'NY', 'Albany' ], 
// [ 'VA', 'Richmond'], [ 'Wisconsin', 'Madison' ] ]}displayEntries(); // Function Call
```

**返回元素的排序**

数组中各项的顺序取决于键-值对中键的类型。如果使用数字键，那么数组中的值将根据其键的数值进行排序

例如:

```
const states = {
    4: "Phoenix",
    1: "Albany",
    2: "Richmond",
    3: "Madison"
  }// The resulting array will be ordered like so:[ [ '1', 'Albany' ],
  [ '2', 'Richmond' ],
  [ '3', 'Madison' ],
  [ '4', 'Phoenix' ] ]
```

如果键不是数字的，那么返回的数组中的元素将按照它们原来的顺序排列。

**包装**

请注意，关键字“keys”、“values”或“entries”必须小写。

此外，如果将非对象作为参数传递给方法，该方法将尝试将该项强制转换为对象，然后该对象将被转换为数组。这可能是一些异常错误的原因。

示例:

```
function displayEntries(){

const notAnObject = "Medium.com/@MendelBak"

return Object.values(notAnObject); 
// The result of this would be:
// [ 'M', 'e', 'd', 'i', 'u', 'm', '.', 'c', 'o', 'm', '/', '@',
// 'M', 'e', 'n', 'd', 'e', 'l', 'B', 'a', 'k' ]}displayEntries(); // Function Call
```

如果你喜欢这篇文章，请分享或鼓掌，以帮助他人找到它。

如果您有任何问题或意见，请在下面留下您的评论。