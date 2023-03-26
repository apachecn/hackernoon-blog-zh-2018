# 使用谷歌视角查找评论的“毒性/亵渎性”

> 原文：<https://medium.com/hackernoon/find-toxicity-profanity-of-a-comment-using-google-perspective-f0eea2b5249e>

![](img/5217ead90fcf5b9a151d21b87335dc24.png)

社交媒体已经成为每个人生活的重要组成部分。截至 2017 年，全球互联网用户的日常社交媒体使用量达到每天 135 分钟。当涉及到极端的平均水平时，青少年拿了奖。他们每天花在社交媒体上的平均时间是 9 个小时。这比大多数人睡觉或上学的时间都长。

随着我们花在网上的时间越来越多，新技术让我们在虚拟世界中联系得更远，我们联系得越远，我们就越容易产生网上问题。其中一个主要问题是网上骚扰。

在一项对 3，000 多名美国人的调查中，47%的人报告经历过某种形式的在线骚扰或虐待。为了遏制这一问题，谷歌采取的措施之一是提出一项有助于改善在线对话的技术。介绍**谷歌视角。**

# 谷歌视角:

Perspective 是一个 API，它使主持更好的对话变得更加容易。API 使用机器学习模型来对评论可能对对话产生的感知影响进行评分。开发者和出版商可以使用这个分数向评论者提供实时反馈，或者帮助版主完成他们的工作，或者让读者更容易地找到相关信息。观点确定评论是否会被认为对讨论“有害”。

# 如何使用谷歌视角？

首先，你需要访问谷歌的透视 API。这可以通过被透视图团队列入白名单来利用。[申请这里](https://www.perspectiveapi.com/)

一旦你被列入白名单，继续创建一个[谷歌云项目](https://console.developers.google.com/)。

从 Google API 列表中搜索“Perspective API ”,然后转到 [Perspective API 的概述页面](https://console.developers.google.com/apis/api/commentanalyzer.googleapis.com/overview)并点击 *Enable* 。

要验证您的请求，您需要为您的项目生成凭据。使用 API 键是最简单的选择。进入 [API 凭证页面](https://console.developers.google.com/apis/credentials)，点击*创建凭证*，选择“API Key”。

*现在您拥有了一切，除了 nodejs 客户端使用 Google Perspective 查找评论的“毒性/亵渎性”。*

## 多田…&这是您的 Google 透视图的开源 nodejs 客户端:

我开源了一个用于 Google perspective 的 nodejs 客户端，带有清晰的文档，可以快速入门。点击下面的链接，使用它&如果你发现任何问题，请随时提出。

[](https://github.com/sandeshbsuvarna/Comment-Toxicity-Profanity-Analyzer) [## sandeshbsuvarna/Comment-毒性-亵渎-分析仪

### 评论毒性/亵渎分析器，将有助于增加同理心，参与和在线交谈的质量…

github.com](https://github.com/sandeshbsuvarna/Comment-Toxicity-Profanity-Analyzer) 

> 互联网应该是一个好地方