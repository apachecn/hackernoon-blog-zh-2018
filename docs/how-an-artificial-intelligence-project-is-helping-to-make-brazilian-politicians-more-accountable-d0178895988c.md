# 一个人工智能项目是如何帮助巴西政治家变得更负责任的？

> 原文：<https://medium.com/hackernoon/how-an-artificial-intelligence-project-is-helping-to-make-brazilian-politicians-more-accountable-d0178895988c>

众议院是巴西国民议会的下院。它有 513 名国会议员，其中 219 人的月支出最高可报销。一个国会议员每个月要加满 30 个煤气罐。两名国会议员声称当天支付了 13 份餐费。这些是由一个人工智能项目——“T2 行动”(Operation Serenata de Amor)发现的。该平台已分析了超过 300 万份票据，并提出了约 8000 个违规公共支出案例。

![](img/b8ab1cdf10a3308d1d7a9e5773f9d435.png)

该项目于 2016 年 3 月由数据科学家 [Irio Musskopf](https://twitter.com/irio?lang=en) 、社会学家 [Eduardo Cuducos](https://twitter.com/cuducos/) 和企业家 [Felipe Cabral](https://twitter.com/felipebcabral?lang=en) 构思。该项目以 Serenata De Amor 命名，这是一种巴西太妃糖，项目名称的灵感来自于[“Toblerone 事件”](https://en.wikipedia.org/wiki/Mona_Sahlin)一位瑞典政治家用她的官方信用卡购买食品杂货，而 [Toblerone](https://hackernoon.com/tagged/toblerone) 恰好是购买的物品之一。

![](img/05a3beaa94ffd098aa3d48e8b1f86c12.png)

最初的资金是通过 [Catarse](https://www.catarse.me/serenata?ref=ctrse_explore_pgsearch&project_id=40271&project_user_id=56666) 筹集的，这是一个开源的众筹平台，他们达到了目标金额的 131%。他们在 Apoia 上有一个经常性的融资活动，帮助他们和他们的团队成员维持这个项目。

众议院有 4 名行政官员负责报销事宜。他们平均每天收到 1500 份报销申请，需要手动处理。Rosie 是第一个用于分析支出的模块。罗西是以杰特森公司的机器人命名的。

![](img/95390be8a39ce7ff04d836383d1706e9.png)

据[网站](https://serenata.ai/en/)

# “罗西:一个分析巴西国会议员在任期间开支的人工智能。罗西可以发现可疑的支出，并让市民参与到对这些发现的讨论中来。”

Rosie 使用来自 CEAP 的数据(行使议会活动的配额)以及外部数据源来确定似乎不正常的报销，并说明原因。

最初，这些不正常的支出被报告给了众议院，然后被转发给相关的政治家，但根据法律，政治家不必回答这类问题，因此没有人回答。这导致了告密者的产生，告密者是一个推特机器人，在推特上发布这些不规则的支出。可以在 [@ **RosieDaSerenata**](https://twitter.com/RosieDaSerenata) 下关注。

![](img/157608efd272e4010b0a28aac4a0e348.png)

机器人发推文说“你能帮我吗？”。这有助于间接说明可能存在误报，最终需要人工判断。

他们还创造了 [Jarbas](https://jarbas.serenata.ai/dashboard/chamber_of_deputies/reimbursement/) 被描述为

# “为了让人们可视化并理解 Rosie 生成的数据，我们创建了 [Jarbas](https://jarbas.serenata.ai/) 。在这个网站上，用户可以浏览国会议员的开支，并了解每一个疑点的详细情况。这是证实怀疑的起点。”

Operation**Serenata de Amor**在不断发展，他们正致力于将分析扩展到其他形式的公共支出。他们也在改进他们的平台，以便以一种更容易消费的方式呈现信息，并使其更具交互性。

完整的代码是开源的，可以在 [Github](https://github.com/okfn-brasil/serenata-de-amor) 上获得。您可以贡献或使用它作为框架来分析您所在国家的公开数据。还有一个开放的[电报群](https://telegram.me/joinchat/AKDWc0BDOqriD1n-mntRBg)开发者出没的地方。你可以在 twitter 上找到标签为 [#SerenateDeAmor](https://twitter.com/hashtag/SerenataDeAmor?src=hash) 的讨论。有关更多信息:

[网站](https://serenata.ai/)

[维基百科](https://en.wikipedia.org/wiki/Operation_Serenata_de_Amor%20https://changelog.com/podcast/268)

[播客](https://changelog.com/podcast/268)