# 引入响应式 React 组件🔥

> 原文：<https://medium.com/hackernoon/introducing-responsive-react-components-f6cd14976570>

![](img/e74d1a88cd376c4199e06e25b46aa9ec.png)

**TL；DR** — *你可以在 React 中用一行程序渲染特定于视口的组件*

React 很牛逼，全世界一致同意。当我们把一切都看作组件时，开发会快得多，也容易得多。由于一切都是 JavaScript 驱动的， **React 只根据应用程序的状态**呈现必要的代码。即使你有超过一千个组件和数万行代码也没关系。**如果你** [**懒加载**](https://medium.freecodecamp.org/how-to-use-react-lazy-and-suspense-for-components-lazy-loading-8d420ecac58) **你的组件，你只加载用户需要的东西，我认为这是使用 React** 的最大赢家。

也就是说，您有没有想过在 React 代码库中编写媒体查询时会发生什么？

你渲染那些不应该出现在特定视窗中的元素，并使用 css 隐藏它们。

在这个例子中

```
import React from 'react';
import './Example.style.scss';

const Example = () => {
  return (
    <div className="example">
      <div className="mobile-only">I'm a fancy mobile div</div>
      <div className="desktop-only">I'm a heavy desktop div</div>
    </div>
  );
};
```

`Example.style.scss`文件

```
.example {
  .mobile-only {
    @media (min-width: 768px){
      display: none;
    }
  }
  .desktop-only {
    @media (max-width: 767px){
      display: none;
    }
  }
}
```

当`Example`组件渲染时，**`.mobile-only`和`.desktop-only`元素**都将在 DOM 中渲染**，而`.mobile-only` div 将在较大的视口中隐藏，`.desktop-only` div 将在较小的视口中用 css `display: none`隐藏。**

**如果这是小的，这是没问题的。但是在我工作的一个项目中，我们有一个沉重的桌面菜单和一个沉重的移动菜单，两者都呈现在所有的视口中。**仅仅是** `**Menu**` **一个大小就应该在** `**20Kb**` **左右，我们很容易为每个用户在浏览器中加载一个不需要的`20Kb`。如果您有更多的视口特定元素，这个大小将继续增加。****

**[React Socks](https://github.com/flexdinesh/react-socks) 是**最小 React 库**到**基于视口**渲染组件。**

**向媒体提问说再见。下面是你如何用一个超级酷的语法来渲染特定于视口的组件。**

```
const Example = () => {
  return(
    <Breakpoint small down>
      <MyAwesomeMobileMenu>
        This component will render only in mobile devices
      </MyAwesomeMobileMenu>
    </Breakpoint>
  );
};const Example = () => {
  return(
    <div>
      <Breakpoint small down>
        <div>I will render only in mobile devices</div>
      </Breakpoint>

      <Breakpoint medium only>
        <div>I will render only in tablets (iPad, etc...)</div>
      </Breakpoint>

      <Breakpoint large up>
        <div>I will render in laptops, desktops and everything bigger</div>
      </Breakpoint>
    </div>
  );
};
```

**不仅如此。**你可以指定自己的断点**(想要多少有多少**哇！**)并根据您的项目需求使用它们**。此外，在你的项目中，你只需要`setDefaultBreakpoints`一次😎****

```
**import { setDefaultBreakpoints } from 'react-socks';

setDefaultBreakpoints([
  { xs: 0 },
  { s: 376 },
  { m: 426 },
  { l: 769 },
  { xl: 1025 }
]);**
```

****这些是我最喜欢的断点****

```
**setDefaultBreakpoints([
  { cats: 0 },
  { dinosaurs: 900 }
]);

<Breakpoint cats only>
    Only cats can render me 🐈
</Breakpoint>**
```

## ****你应该使用[反应袜](https://github.com/flexdinesh/react-socks)的理由****

*   ******轻松渲染视口特定的**组件****
*   ****你可以定义你自己的**断点**(例如 xs，ipad，bigmonitors)并使用它们****
*   ****如果您延迟加载视口特定组件，您可以**提高您的应用程序性能******
*   ****更简单、更甜蜜的**语法**便于使用****

****该库已经发布到 [npm](https://www.npmjs.com/package/react-socks) 并且是 alpha 版本。我很乐意在发布第一个稳定版本之前得到您的反馈并改进它。****

*****编辑:首个稳定版本已于 2018 年 12 月 9 日发布，性能提升，支持 SSR。*🎉****

****如果你想知道**为什么叫 React Socks** 🤷‍♂️****

****[**React Socks**](https://github.com/flexdinesh/react-socks) 舒适地包裹你的组件以防止在各种视口中不必要的渲染，就像你如何用袜子包裹你的脚以防止临阵退缩一样🎉****

****让我们穿上一些花哨的反应袜，把所有的部件包起来🔥****

****你太棒了！祝您愉快！⚡️****

*****最初发布于*[*dev . to*](https://dev.to/flexdinesh/introducing-responsive-react-components--1a6a)*。*****