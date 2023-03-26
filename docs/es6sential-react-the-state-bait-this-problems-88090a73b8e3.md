# 反应——国家诱饵和这个点的问题

> 原文：<https://medium.com/hackernoon/es6sential-react-the-state-bait-this-problems-88090a73b8e3>

对于新用户来说，让 [React](https://hackernoon.com/tagged/react) 颇具威胁性的一点是，在开始之前需要掌握一些概念。

不管怎样，学习 ES6 是很重要的，但是对于所使用的模式和结构，它对我们最喜欢的组件化框架特别有用。也就是说，你仍然可以使用 ES5 来让它工作，但是为了一致性，ES6 是最好的选择！

![](img/04e55182c43893ca4da7dee719631188.png)

Create websites with React.js!

别活在过去了，跟上时代吧，兄弟！！:P

# 别改变国家，伙计！

大多数情况下，这无论如何都不是一个好主意，但对于构建前端项目来说尤其如此。当事情出错时，改变最初的初始状态会导致很多问题，它会使跟踪错误来自哪里变得困难。

这里我们有一个商店，让我们添加一个项目…

```
class App extends Component { 
    constructor(props) {  
        super(props);  
            this.state = { shop:[] }; 
    }...//onAdd = (item) => {  
    this.state.shop.push(item);
};
```

对于没有受过训练的人来说，这可能是你认为应该做的。但这是一个典型的常见情况，我们正在改变原始状态，这在函数式编程中是一个大忌。

```
onAdd = (item) => { 
    let newList = this.state.shop;
    newList = newList.concat(item);
    this.setState({ shop: newList })};
```

Concat 是一种将两个数组合并成一个新数组的方法。这里，我们获取商店数组(目前为空),并将其与商品组合成一个新数组。然后我们用 React 的 setState 来修改当前状态，而不是直接自己动手。

```
onAdd = (item) => {
    let newList = this.state.shop;
    newList = [...newList, item];
    this.setState({ shop: newList })
```

大多数情况下，我们使用 ES6“版本”,用 spread 运算符组合数组。“…”允许引用现有数组，但不允许修改，这意味着我们不会改变原始数组。

确保我们永远不会直接改变状态是一个很好的实践，我们希望一直保持这样的心态。

# 用箭头函数解决你的“这个”问题

您可能已经注意到了，也可能没有注意到，上面写的函数看起来格式很奇怪。请注意，没有函数关键字，对于更高级的编码器，因此也没有绑定方法。

当我们在组件中传递数据时，我们使用`this.[function]`来引用其他地方的函数，但是由于词法范围的原因，`this`并没有引用我们想要它引用的地方。

箭头函数是一种不同的编写函数的方式，但是它的主要优点是它阻止了`this`关键字被限制在函数中。

```
class ParentComponent extends Component {
    render() {
        return (
            <div>
                <ChildComponent 
                parentFunction="this.parentFunction" 
                />
            </div>
        )
    };function parentFunction() {
        console.log("Button is clicked!")
    }
}class ChildComponent extends Component {
    render() {
        return (
            <div>
                <button onClick={this.handleClick}>
                    Click Me!
                </button>
            </div>
        )
    }; handleClick = () => {
        this.props.parentFunction();
    }
}
```

在这个子组件中，我们有一个按钮，单击它时，会使用从父组件传递过来的 parentFunction 属性。

使用`function`关键字传递 parentFunction 的事实将使`this`关键字无法引用我们想要的内容，如果它没有使用箭头函数的话。

ES5 解决这个问题的方法是使用`bind`方法，但是这意味着传递的每个函数都必须被绑定，并且有很多函数是不可伸缩的。

所以就用`[name] = () => {}`来拯救自己`this`的范围界定问题吧！

另一个常见的场景是当 DOM 事件需要一个参数时。

```
**Using ES6 Arrow Functions**<button onClick={(event) => this.deleteRow(id, event)}>Delete Row</button>*// Events as parameters are explicit so can be even shorter!*<button onClick={() => this.deleteRow(id)}>Delete Row</button>**Using ES5 Bind**<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

绝对看起来更干净，需要更少的维护。我们不必跟踪所有的绑定，因此不容易出错。它的可读性更强，整体看起来更整洁！

# 获得 2 个带 ES6 的把手！

如果你不知道这些概念，就一头扎进去，理解起来肯定会很困难。学习 ES6 更新对学习 React 是非常必要的，这对初学者来说可能很不友好。

不管怎样，随着 [Javascript](https://hackernoon.com/tagged/javascript) 的发展，挑战所有的可能性，继续生存下去，它变得越来越明显，及时更新所有新添加的特性是很重要的。

这些天来，我试图通过尽可能多地使用 ES6 语法和 Air Bnb 林挺来保持事情的一致性。事实上，对于一个有抱负的新手来说，你可以用不同的方式做很多事情，这可能会令人望而生畏。

无论如何，希望这是有帮助的，有一个伟大的一周！