# 命名变量的艺术

> 原文：<https://medium.com/hackernoon/the-art-of-naming-variables-52f44de00aad>

> 计算机科学只有两个硬东西:缓存失效和事物命名。—菲尔·卡尔顿

给事物命名确实很困难，但是为事物正确命名的回报是值得的。你见过类似这样的代码吗？

```
const convertObj = (x, y, z) => {
    const k = Object.keys(x);
    return k.map((key) => {
        return {
            [y]: key,
            [z]: x[key],
        }
    });
}
```

这到底有什么用？您可以通过一行一行地阅读它来找出答案，但是如果变量命名正确的话，事情会变得更简单。

好的变量名是至关重要的，尤其是在动态类型语言中，你没有定义类型来帮助你理解变量。然而，当使用好的命名约定时，即使静态类型的语言也会有更好的可读性。

我将分享一些我多年来建立的关于命名的一般规则。我将按类型分解一些例子。让我们从数组开始。

# 数组

数组是项目的可迭代列表，通常是同一类型的。因为它们将保存多个值，**复数化**变量名是有意义的。

```
// bad
const fruit = ['apple', 'banana', 'cucumber'];// okay
const fruitArr = ['apple', 'banana', 'cucumber'];// good
const fruits = ['apple', 'banana', 'cucumber'];// great - "names" implies strings
const fruitNames = ['apple', 'banana', 'cucumber'];// great
const fruits = [{
    name: 'apple',
    genus: 'malus'
}, {
    name: 'banana',
    genus: 'musa'
}, {
    name: 'cucumber',
    genus: 'cucumis'
}];
```

# 布尔运算

布尔值只能保存两个值，`true`或`false`。鉴于此，使用类似于“**是**”、“**有**”、“**可以**”这样的前缀会帮助读者推断变量的类型。

```
// bad
const open = true;
const write = true;
const fruit = true;// good
const isOpen = true;
const canWrite = true;
const hasFruit = true;
```

谓词函数(返回布尔值的函数)呢？在命名函数之后，命名值会变得很棘手。

```
const user = {
    fruits: ['apple']
}const hasFruit = (user, fruitName) => (
    user.fruits.includes(fruitName)
);// what do we name this boolean?
const x = hasFruit(user, 'apple');
```

我们不能命名布尔型`hasProjectPermission`，因为我们已经给函数起了这个名字。在这种情况下，我喜欢用`check`或`get`作为谓词的前缀。

```
const checkHasFruit = (user, fruitName) => (
    user.fruits.includes(fruitName)
);const hasFruit = checkHasFruit(user, 'apple');
```

# 数字

对于数字，想想描述数字的词。像`maximum`、`minimum`、`total`这样的词会。

```
// bad
const pugs = 3;// good
const minPugs = 1;
const maxPugs = 5;
const totalPugs = 3;
```

# 功能

函数应该用动词和名词来命名。当函数在资源上执行某种类型的动作时，它的名字应该反映这一点。一个好的格式是`actionResource`。比如`getUser`。

```
// bad
userData(userId)
userDataFunc(userId)
totalOfItems(items)// good
getUser(userId);
calculateTotal(items);
```

我见过的用于转换值的一个常见约定是在函数名前面加上`to`。

```
// I like it!
toDollars('euros', 20);
toUppercase('a string');
```

我喜欢的另一种常见命名模式是在迭代项目时。当在函数中接收参数时，使用数组名的单数形式。

```
// bad
const newFruits = fruits.map(x => {
    return doSomething(x);
});// good
const newFruits = fruits.map(fruit => {
    return doSomething(fruit);
});
```

# 总结

对这些命名约定有所保留。如何命名变量没有一致的命名重要。如果您保持一致的命名模式，您的代码库将更容易推理，下一个开发人员将能够考虑得更少。

如果你有自己的命名习惯，我很乐意在评论中听到。如果你喜欢这篇文章，请随意给它一两下掌声。