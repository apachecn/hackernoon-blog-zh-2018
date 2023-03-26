# 使用 AWS Lambda 启动并运行

> 原文：<https://medium.com/hackernoon/get-up-and-running-with-aws-lambda-e7a5bdb62e7c>

(天使之肩)AWST2Lambda:我喜欢！快速、轻松的部署和更好的模型，以实现更精细的按需定价。

(魔鬼肩膀)AWS Lambda:我又感觉到了 T4 的 JavaScript 疲劳症。尽管我刚刚完善了 AWS / Docker / Heroku / Digital Ocean 的部署，但我必须学习新的部署流程。

进入 [Claudia JS](https://github.com/claudiajs/claudia) ，一个让其他爱好者接管这个美丽新世界细节的机会。您有机会快速启动并运行，了解这一新的技术堆栈是否适合您。

# 福利 101

如[林基·夏尔马](https://medium.com/u/b3a08142d0af?source=post_page-----e7a5bdb62e7c--------------------------------)在[优势&劣势使用具有高级功能的 AWS Lambda](/tech-journal/advantages-disadvantages-of-using-aws-lambda-with-advanced-features-287e73607823)所述:

1.  降低的价格
2.  减少(不)机器维护
3.  降低了扩展成本
4.  更轻松的运营管理

所有的优势对于应用程序开发人员来说都是额外的技术优势。换句话说:它们不是这些开发人员或部署环境中立即获得的优势。Docker 就是一个很好的反例，它有利于基于易用性和便利性从纯开发人员的角度管理部署环境。

AWS Lambda 的优势完全基于定价和关注点分离。虽然许多优势是开发人员长期积累的，但这些优势并不是我们在项目第一天或短期 hackathon 所关注的。

虽然有些人可能会得出不同的结论，但我的结论是，我应该选择最简单的途径进入这项技术。我应该充分利用我的早期投资。换句话说:我想要即插即用解决方案。我要的是克劳迪娅·JS。

# Lambda

当时我正在为一个黑客马拉松做闪电战。在这个过程中，我们决定为我们的服务层测试 AWS Lambda 的优点。

对于无服务器测试案例，我们遇到了完美的 API 风暴:

1.  相当稳健的按需计算
2.  除了审计目的之外，不需要数据库
3.  我们在 S3 托管的简单、包含的 React 应用(另一个有趣的话题[在这里](https://medium.freecodecamp.org/how-to-host-a-website-on-s3-without-getting-lost-in-the-sea-e2b82aa6cd38?gi=1a2177e5797b)来自[凯尔·加尔布雷斯](https://medium.com/u/9c730136e183?source=post_page-----e7a5bdb62e7c--------------------------------)
4.  不需要认证

我们的 API 层本质上是一个“工人”层。它是一个存储专有业务逻辑、算法和进行各种第三方 API 调用的地方。这对 Lambda 来说是一个很好的测试案例。它很简单，而且正好符合无服务器功能的所有优点。

# 缓慢开始，直到…

最初，我们登陆了极其健壮的[无服务器](https://www.npmjs.com/package/serverless)节点模块。我对图书馆没有任何疑虑。只是，我想让一个简单的 API 启动并运行。我想让它运行起来，没有任何混乱。我跟图书馆斗了好半天。最后，我漫无目的地寻找替代解决方案(就像我们所有人在配置极限时所做的那样)。

无意中发现了建议 Claudia JS 的 [AWS 文章，我很震惊图书馆没有更激动人心的宣传。](https://aws.amazon.com/blogs/compute/how-to-turn-node-js-projects-into-aws-lambda-microservices-easily-with-claudiajs/)

# 说服我

尽管 AWS 专业人员和系统管理员。**我**不想整天折腾 AWS 的配置。即插即用是我的中间名，即使在此期间会有一些花费。与我(你)的时间相比，这算不了什么。

克劳迪娅 JS 提供:

1.  定义 API 的干净过程
2.  干净的绿地部署
3.  全新更新(增量)部署

即插即用的感觉很好。即插即用类似于公司团队建设活动一开始就指定合作伙伴。当然，我们都有一个优化的合作伙伴选择。在混乱中这不太可能发生。随机选择的民主化力量让我们都过得更好。

Claudia 解决了如何构建无服务器 API 的深层(通常也是重要的)问题。相反，它提供了一个相当标准的样板文件，努力让你朝着你想做的事情前进。如果您需要一个提醒，那就是:降低成本、减少(无)维护、降低扩展成本，以及更轻松的运营管理。

# API 语法和简单部署

## 关键要点

1.  简单、类似 Express 的 API 语法
2.  非常容易的部署，简单的更新
3.  与节点和现有 *aws-sdk* 标准的兼容性

## 关键要点

1.  简单情况下推断的内容类型
2.  支持返回值的承诺，即装即用
3.  默认超时很短，这是配置中需要注意的一点

## 关键要点

1.  错误处理是一致的，并且易于理解
2.  请求对象，这对于节点开发人员来说并不奇怪
3.  销毁和重新创建 Claudia Lambda 项目很简单，因此提供了一些部署不变性

# 一些警告和好运！

当地的测试不太符合标准。有一个很好但正在发展的项目叫做 [Claudia Local API](https://github.com/suddi/claudia-local-api) 。诚然，它缺少一些我们期待的更成熟的选项，但值得一看。

最终，配置可以也应该成为您关注的一部分。您的团队或产品值得拥有能够处理复杂 Lambda 配置的有能力的 DevOps 和 SRE 工程师。我唯一的建议是，一开始没有必要。使用 [Claudia JS](https://github.com/claudiajs/claudia) 开始并保持简单。

![](img/da91e8a10e35cfb995307219d27fcc81.png)