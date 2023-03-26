# CSS 中的动画:当事物开始移动

> 原文：<https://medium.com/hackernoon/an-animation-in-css-when-things-start-to-move-a90ec789b407>

在 CSS 中使用动画的第一步是[过渡属性](https://kolosek.com/css-transition/)。当预定义的过渡不够或者不合适时，就有 CSS 动画。

**注意:本帖没有使用 JavaScript！**

动画属性有两部分:*关键帧*和*动画属性*。关键帧定义动画的阶段和样式，animation 属性将关键帧分配给元素，并指定动画如何在定义的元素上播放。

# 关键帧

通过定义关键帧，您实际上为动画设置了步骤。这部分动画的语法是单词`@keframes`，后跟动画名称，再后跟关键帧选择器及其样式。

```
@keyframes animation-name{
    0%{CSS}
    50%{CSS}
    100%{CSS}
}
```

动画是通过由关键帧选择器设置的从一种风格逐渐改变到另一种风格来创建的。这些选择器的值可以是 0%-100%(或者单词“from”和“to”分别代表 0%和 100%)。我相信非常清楚，0%表示动画的开始，100%表示结束。

你可以制作动画的属性不是无限的，所以这里有一个列表[。](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animated_properties)

现在，关于@keyframes 选项最令人震惊的是:CSS 中的
`**!important**` **关键帧不仅让你的代码变得重要，还忽略了整个代码行...！多么令人震惊！**

# 动画属性

一旦定义了关键帧，你最好确保工作有回报并使用它们。在你想要制作动画的选择器里面添加`animation`属性。该属性是八个动画属性的简写。

```
animation
=
animation-name
animation-duration
animation-timing-function
animation-delay
animation-iteration-count
animation-direction
animation-fill-mode
animation-play-state
```

**主要添加的属性有**`**animation-name**`**`**animation-duration**`**。没有** `**animation-name**` **您无法将动画链接到关键帧，没有** `**animation-duration**` **持续时间将被设置为 0，同样没有动画。****

**其他属性用于更好地定义动画。`Animation-timing-function`指定动画的速度曲线。它可以用预定义的值(线性、缓动、缓入、缓出、缓出)定义，或者由[步骤](https://designmodo.com/steps-css-animations/)或[三次贝塞尔](http://cubic-bezier.com/#.17,.67,.83,.67)值自定义定义。**

**`Animation-delay` property works pretty much the same as [transition](https://kolosek.com/css-transition/)一 it defines the start of the animation.**

**默认情况下，动画将播放一次。用`animation-iteration-coun`你可以把它改成任何数字或者定义为`infinite`。**

**`Animation-direction` property specifies animation cycle 一 whether it will be played forwards, backward or in alternate cycles. Options that define those states are `normal`, `reverse`, `alternate` (forward then backward) and `alternate-reverse` (backward then forward).**

**`Animation-fill-mode`属性定义了动画结束时或开始前元素的样式。默认状态是`none`，在这种状态下，动画不会对动画元素应用任何样式。选项`both`将为该元素设置第一个关键帧值，直到它开始，并在它结束后设置最后一个关键帧值。选项`forwards`和`backwards`定义了与`both`分离的选项。**

**最后，用`animation-play-state`你可以暂停动画并再次播放。因此，它有两个选项，`running`(默认)和`paused`。**

**即使`animation`属性是所有列出的选项的简写，但通常仅用于`animation-name`和`animation-duration`以及其他单独列出的属性。**

```
animation: icon-animation 2s;
animation-timing-function:ease;
animation-delay: 5s;
animation-iteration-count:infinite;
animation-direction:normal;
animation-fill-mode:both;
animation-play-state:running;
```

**为了便于记录，可以向元素中添加多个动画。**

**还有一个关于[前缀](https://kolosek.com/understanding-css-vendor-prefixes/)的简短说明:对于所有动画属性和关键帧，你应该使用`-webkit-`。对于一些特定的已知问题，检查[可以使用](https://caniuse.com/#feat=css-animation)。**

# **例子**

**动画本身在设计中并不是一个有特定目的的东西。另一方面，它可以在任何地方使用，它总是会增值。我会说特定的动画(用这个属性定义的)用在特效上最多，用过渡定义的动画用在更常见的元素上，比如悬停和其他状态。**

**此外，悬停效果的一些影响，甚至只是一个不寻常的行为线是用动画属性定义的。**

**所以，如果你需要在你已经完成的设计中添加“那个东西”，你可以在背景或者一些特定的元素上添加一个特殊的效果(加载器，滚动，悬停……)。**

**如果你正在寻找一个不错的博客，里面有更好的帖子和最好的 CSS 动画示例，一定要去看看 Creative Bloq 上的那个。**

**此外，一定要检查 [Animista](http://www.animista.net/) 中许多你可以轻松使用的预定义动画。**

# **收场白**

**在我看来，动画属性是 CSS 中最高的属性之一，所以如果不是因为 wow 效果，你需要它来实现你的“主控 CSS”目标。如果你对 UX 感兴趣(如果不是，也许你应该感兴趣)，看看[，这是一篇来自视觉应用团队](https://www.invisionapp.com/blog/importance-good-animation-ux/)的关于动画的非常好的文章。**

***敬请关注更多 CSS 相关帖子！***

***原载于 2018 年 6 月 12 日*[*kolosek.com*](https://kolosek.com/css-animation/?utm_source=me)*。***