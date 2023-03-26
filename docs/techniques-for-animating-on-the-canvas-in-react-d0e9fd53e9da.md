# React 中的画布动画技术

> 原文：<https://medium.com/hackernoon/techniques-for-animating-on-the-canvas-in-react-d0e9fd53e9da>

![](img/75ab6cd75c6b63c45885d2b1ca17bd1c.png)

我最近在 Twilio 博客上尝试了 React 中的[音频可视化。当我打算自学更多关于 web audio API 的知识时，我发现我学到了一些在 React 项目中制作 canvas 动画的技巧。如果你正在 React 中创建一个画布动画，那么这可能也会对你有所帮助。](https://www.twilio.com/blog/audio-visualisation-web-audio-api--react)

# 好的推荐信

首先，如果你以前用过 React，你会知道你应该避免接触 DOM，让 React 来处理它。如果你以前使用过 HTML5 `<canvas>`的话，你也会知道要获得在画布上绘制的上下文，你需要直接调用画布元素本身。值得庆幸的是，这是一个边缘案例，[通过引用](https://reactjs.org/docs/refs-and-the-dom.html)来支持 React。

要在 React 组件中获取对 canvas 元素的引用，首先需要使用`React.createRef`在构造函数中创建引用。当您开始渲染画布元素时，添加一个名为`ref`的道具，它指向您创建的 ref。

```
class Animation extends React.Component {
  constructor(props) {
    super(props);
    this.canvasRef = React.createRef();
  }

  render() {
    return (
      <div>
        <canvas ref={this.canvasRef} />
      </div>
    );
  }
}
```

这样设置好之后，就可以通过 ref 的`current`属性引用 canvas 元素，例如在`componentDidMount`中:

```
 componentDidMount() {
    const canvas = this.canvasRef.current;
    const context = canvas.getContext('2d');
    context.fillRect(0, 0, canvas.width, canvas.height);
  }
```

现在你有了可以随意绘制和制作动画的背景。

# 分离动画和绘图

许多使用 React 的构建都是为了维护视图的状态。我第一次在 React 的画布上制作动画时，我保存了状态和代码，以便在同一个组件中绘制它。在网上浏览示例后，我在 CodePen 上偶然发现了[这个旋转的正方形。这个例子中我真正喜欢的是使用两个组件将状态从绘图中分离出来的方式。然后，绘图的状态通过 props 从动画组件传递到绘图组件。](https://codepen.io/vasilly/pen/NRKyWL)

我重新创作了原作来展示这种分离。

首先定义一个`Canvas`组件，它使用道具作为参数来绘制图像。

```
class Canvas extends React.Component {
  constructor(props) {
    super(props);
    this.canvasRef = React.createRef();
  }

  componentDidUpdate() {
    // Draws a square in the middle of the canvas rotated
    // around the centre by this.props.angle
    const { angle } = this.props;
    const canvas = this.canvasRef.current;
    const ctx = canvas.getContext('2d');
    const width = canvas.width;
    const height = canvas.height;
    ctx.save();
    ctx.beginPath();
    ctx.clearRect(0, 0, width, height);
    ctx.translate(width / 2, height / 2);
    ctx.rotate((angle * Math.PI) / 180);
    ctx.fillStyle = '#4397AC';
    ctx.fillRect(-width / 4, -height / 4, width / 2, height / 2);
    ctx.restore();
  }

  render() {
    return <canvas width="300" height="300" ref={this.canvasRef} />;
  }
}
```

然后创建一个使用`[requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)`运行动画循环的`Animation`组件。每次动画循环运行时，更新状态中的动画参数，并让 React 使用更新的道具渲染`Canvas`。

不要忘记实现`componentWillUnmount`来停止`requestAnimationFrame`循环。

```
class Animation extends React.Component {
  constructor(props) {
    super(props);
    this.state = { angle: 0 };
    this.updateAnimationState = this.updateAnimationState.bind(this);
  }

  componentDidMount() {
    this.rAF = requestAnimationFrame(this.updateAnimationState);
  }

  updateAnimationState() {
    this.setState(prevState => ({ angle: prevState.angle + 1 }));
    this.rAF = requestAnimationFrame(this.updateAnimationState);
  }

  componentWillUnmount() {
    cancelAnimationFrame(this.rAF);
  }

  render() {
    return <Canvas angle={this.state.angle} />;
  }
}
```

你可以在这支笔中看到[的动作。](https://codepen.io/philnash/pen/QVeOrd)

# 重新渲染

在 React 中制作动画或进行其他密集的视觉更新时，一个问题是过于频繁地重新渲染子元素，导致 [jank](http://jankfree.org/) 。当我们在画布上绘图时，我们从不希望画布元素本身被重新渲染。那么，暗示我们不希望这种情况发生的最好方式是什么呢？

你可能会想到`[shouldComponentUpdate](https://reactjs.org/docs/react-component.html#shouldcomponentupdate)`生命周期法。从`shouldComponentUpdate`返回`false`会让 React 知道这个组件不需要改变。然而，如果我们使用上面的模式，从`shouldComponentUpdate`返回`false`将跳过运行`componentDidUpdate`，这是我们绘图的原因。

我最终在 StackOverflow 上看到了丹·阿布拉莫夫对一个问题的回答。我们可以创建一个实现`shouldComponentUpdate`并返回`false`的`PureCanvas`组件，并使用一个[回调引用](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs)来获取对父`Canvas`组件中 canvas 元素的引用。

*注意:在* [*Dan 的回答中*](https://stackoverflow.com/a/49803151/28376) *他说使用上面的模式应该没问题，下面的技术可能只有在你已经对你的应用程序进行了概要分析并发现它有所不同时才是必要的。*

更新上面的例子，我们将`Canvas`组件分成一个`Canvas`和一个`PureCanvas`。首先，`PureCanvas`使用回调 ref 和通过 props 提供的回调将画布上下文返回给父组件。它还呈现画布元素本身。

```
class PureCanvas extends React.Component {
  shouldComponentUpdate() {
    return false;
  }

  render() {
    return (
      <canvas
        width="300"
        height="300"
        ref={node =>
          node ? this.props.contextRef(node.getContext('2d')) : null
        }
      />
    );
  }
}
```

然后`Canvas`组件传递一个回调函数`saveContext`，作为渲染`PureCanvas`时的`contextRef`道具。当函数被调用时，我们保存上下文(并缓存画布元素的宽度和高度)。其余与之前的不同之处是将对`ctx`的引用改为`this.ctx`。

```
class Canvas extends React.Component {
  constructor(props) {
    super(props);
    this.saveContext = this.saveContext.bind(this);
  }

  saveContext(ctx) {
    this.ctx = ctx;
    this.width = this.ctx.canvas.width;
    this.height = this.ctx.canvas.height;
  }

  componentDidUpdate() {
    const { angle } = this.props;
    this.ctx.save();
    this.ctx.beginPath();
    this.ctx.clearRect(0, 0, this.width, this.height);
    this.ctx.translate(this.width / 2, this.height / 2);
    this.ctx.rotate((angle * Math.PI) / 180);
    this.ctx.fillStyle = '#4397AC';
    this.ctx.fillRect(
      -this.width / 4,
      -this.height / 4,
      this.width / 2,
      this.height / 2
    );
    this.ctx.restore();
  }

  render() {
    return <PureCanvas contextRef={this.saveContext} />;
  }
}
```

尽管这不是必须的，但我发现动画、绘图和呈现画布元素本身之间的这种分离非常令人愉快。你也可以在 CodePen 上看到这个例子。

# 画布 vs 反应

在 React 中使用画布元素是一段有趣的旅程。他们的工作方式彼此感觉非常不同，所以让他们同步并不一定简单。希望如果你有这个问题，那么这些技术可以帮助你。

如果你对 React 中的其他动画感兴趣，请查看我关于 React 中[音频可视化的文章。](https://www.twilio.com/blog/audio-visualisation-web-audio-api--react)

如果你有另外一种在 React 中使用 canvas 的方法，我很乐意听听。在 Twitter 上给我留言，地址是 [@philnash](https://twitter.com/philnash) 。

*原载于 2018 年 9 月 27 日*[*philna . sh*](https://philna.sh/blog/2018/09/27/techniques-for-animating-on-the-canvas-in-react/)*。*