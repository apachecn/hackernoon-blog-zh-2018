# 自我治理设计模式

> 原文：<https://medium.com/hackernoon/self-governance-design-pattern-3a5937767b11>

![](img/db392d6c1ac3a76450d3a285b235edb9.png)

“white mansion” by [Zac Nielson](https://unsplash.com/@zacherynielson?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

可以说，自我治理是控制混乱和掌握可持续性和可伸缩性的最简单的方法之一。无论是一个国家的政府，一个社区，甚至是一个你我都是其中一员的 10 人团队。

一天，我读到了一个名为 Auroville 的实验小镇，这里居住着来自 40 个国家的 2000 个人。奥罗维尔有一个名为居民大会的政府部门，由所有合法居民组成。这个实验性的城镇是这个饱受腐败和仇恨折磨的国家中最和平的城镇之一。

这让我思考，并最终成为我创建自我治理设计模式的动机。

这种设计模式可以在任何框架中使用，但是本系列中提供的所有示例都基于 React。如果您在任何其他框架中使用过这种设计模式，请评论。我很想听听你的意见。

如果你是一个视频教程类型的家伙，看看这个(我会建议阅读这篇文章，但这取决于你):

我已经把这个分成了一系列中等文章，可以随意跳转。

1.  导言和意识形态
2.  [React 示例—设置用户界面](/@revanth0212/self-governance-design-pattern-part-1-setting-up-ui-a22ba90a1c43)
3.  [React 示例—设置数据流](/@revanth0212/self-governance-design-pattern-part-2-setting-up-data-flow-e54a96ea691)
4.  [React 示例—设置现场互动规则](/@revanth0212/self-governance-design-pattern-part-3-setting-up-field-interaction-rules-721d315f9d02)

在这篇文章中，我们将讨论自治理设计模式的思想。下面的帖子将集中在例子上。

道德学很简单。软件系统中的每个实体应该只做一件事，而且必须做到最精确和优雅。UI 元素应该只在交互时呈现 UI 并更新自身，而不用担心处理字段交互规则或状态管理。与此同时，SGDP 并不要求开发者在选择一项技术时就固定在某项技术上。它不要求开发人员使用 Redux 作为 UI 中的状态管理工具，也不要求您在后台使用 Rails 来构建您的服务。它说的是，使事情功能化，并确保它们在项目的整个生命周期中保持功能性。

让我们从 UI 的角度来看看这个系统是如何扩展的。不要告诉字段如何渲染和渲染什么。当有人与现场互动时，让他们决定做什么。让他们决定如何获取数据进行渲染，只告诉他们从哪里获取数据。当需要的信息被给出时，装备他们去获取和更新他们自己。

例如，如果有一个包含 10 个文本字段的页面，只需给出这些字段的呈现样式、从状态中获取值的路径以及交互时要调用的回调函数。因此，将来如果项目管理团队决定改变组件本身的外观，你所要做的就是改变呈现你的 UI 的共享组件。

此外，由于每个字段维护自己，在 Redux 或 React 上下文作为状态管理工具的情况下，只有已更改的字段才会重新呈现。这将提高 UI 的响应性，因为 JS 引擎需要更新的元素较少。这本身就是 SGDP 的一大优势。

这种模式还有另一个好处。你有没有想过新的 UI 和旧的 UI 结构是如何工作的(比如 Flickr，Meetup)。我们遇到这样的用户界面，他们希望用户使用实验性的或新的用户界面，但是如果用户不满意，他们可以切换回旧的用户界面。有了 SGDP，UI、动作、规则管理和数据流都被分离出来，UI 之间的切换非常简单。

不相信我？查看[这个代码沙箱](https://codesandbox.io/s/github/revanth0212/sgdp-bonus/tree/master/)来看看我的声明。

在以后的文章中，您将深入了解如何构建这样的 UI，以及如何自己构建一个 UI。

概括一下:

SGDP 的意识形态只要求两条规则:

1.  软件系统中的每个实体应该只做一个功能，但是它必须以最精确和优雅的方式执行。
2.  开发者可以选择他所选择的任何技术，但是当他/她使用它们时，必须应用规则 1。

就是这样。这就是自我治理设计模式的全部内容。在下一篇 [**的**](/@revanth0212/self-governance-design-pattern-part-1-setting-up-ui-a22ba90a1c43) 文章中，我们将会看到一个使用 React 和 Redux 的例子。

您可以通过以下方式联系到我:

电子邮件:revanth0212@gmail.com

推特:[https://twitter.com/imre1th](https://twitter.com/imre1th)

领英:[www.linkedin.com/in/revanth0212](http://www.linkedin.com/in/revanth0212)

github:[https://github.com/revanth0212](https://github.com/revanth0212)

和平\m/