# 从类型到回路:一个回顾

> 原文：<https://medium.com/hackernoon/from-typeorm-to-loopback-a-retrospective-188ea18527a2>

![](img/c1c2a3e0f514c4eb439ec7553b89d7e7.png)

社区对 [*的支持非常大，2018 年你应该使用哪种 JavaScript ORM？*](https://medium.freecodecamp.org/a-comparison-of-the-top-orms-for-2018-19c4feeaa5f) 该分析是在实施一个即将发布产品的项目之前进行的。受益于经验，项目转向更喜欢 [LoopBack 3.0](http://loopback.io/doc/en/lb3/index.html) 。本文解释了这种变化的动机。

ORM 分析将 LoopBack 确定为首选，但是[类型的 ORM](https://github.com/typeorm/typeorm) 紧随其后。该团队选择使用 TypeORM，因为我们的项目是用 Angular 编写的，我们希望利用同构类型脚本。

使用 TypeORM 时，开发遇到了许多问题。有些在 GitHub 上有记录。有些[甚至被关闭](https://github.com/typeorm/typeorm/issues/900#issuecomment-377059814)。虽然 TypeORM 团队反应非常迅速，非常有帮助，但设置的困难是早期的危险信号，对早期指标的预测将我们的项目时间表置于危险之中。

通过对其中一些问题的试探，我们发现 TypeORM 文档并不像最初看起来的那样强大。TypeORM 团队通过对 GitHub 的响应在一定程度上弥补了这一点，但 [LoopBack 3.0](http://loopback.io/doc/en/lb3/index.html) 的全面文档开始看起来相对更好。

那些小问题虽然不方便，但还可以忍受。两个主要问题是向环回转变的核心:

1.  [TypeORM 与 Angular CLI](https://github.com/typeorm/typeorm/pull/1143#issuecomment-355102915) 不兼容。
2.  Angular Universal 并不容易扩展成一个统一的 API 和渲染服务器。因此，我们分离了 API 服务器。然而，TypeORM 意味着客户端消费，所以我们不能使用 TypeORM 实现核心支持的同构类型。

这两个问题都是决定性的。Angular CLI 是 Angular 开发的最佳实践工具。这对于角度通用应用的快速和经过测试的开发尤为关键。

诚然，LoopBack 3.0 不支持同构类型，但上面的问题 2 首先消除了使用 TypeORM 的关键动机。

LoopBack 保留了许多优点，这些优点使它成为了一种很好的资源:

1.  LoopBack 会自动构建您的 API。这是核心功能，不是扩展。
2.  LoopBack 自动创建一个 Swagger 文件，该文件又可以自动生成[一个 ERD](https://github.com/nrekretep/pikturr) 。
3.  LoopBack 3.0 是一个文档完善、经过生产测试的工具，围绕一个熟悉的、可扩展的 Express 模式构建。

虽然 LoopBack 3.0 没有同构类型，但它利用了标准化的 JSON 模型定义，这些定义可以很容易地反映在 UI 中。我们能够利用数据库中的一致实体，通过服务器，进入 UI，尽管我们必须复制一些定义。

环回并不完美。期望的特征包括:

1.  真正的同构类型，不需要复制代码。
2.  更好地支持数据库关联。
3.  更好地支持数据库代码配置，包括`CREATE DATABASE`
4.  现代 JavaScript 支持包括`async/await`等等。
5.  类型脚本支持

[回环 4](http://loopback.io/doc/en/lb4/index.html) 正在积极开发中，正在寻找贡献者。它已经支持上面提到的一些特性。它还没有准备好生产，但我希望你能和我一起支持这个项目，作为这个领域的一个创新。