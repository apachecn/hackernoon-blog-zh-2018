# HIPAA &面向 Web 开发人员的医疗数据安全性

> 原文：<https://medium.com/hackernoon/hipaa-medical-data-security-for-web-developers-c877fcde0716>

![](img/32fd24848c5fd7d6df0999a9fd8c5a3f.png)

Source: [Pixabay](https://pixabay.com/en/computer-business-typing-keyboard-1149148/)

# HIPAA &面向 Web 开发人员的医疗数据安全性

如果你的公司经营医疗保健业务并存储个人健康信息，你很有可能必须遵守 HIPAA。什么是 HIPAA，您如何保持合规性？让我们看看 HIPAA 是如何影响你的网站的。

# HIPAA 是什么？

1996 年的健康保险可携带性和责任法案[保护病人数据](http://www.dhcs.ca.gov/formsandpubs/laws/hipaa/Pages/1.00WhatisHIPAA.aspx)。根据加州医疗保健服务部的说法，它强制规定了有关计费流程的医疗保健信息的行业标准，还“要求对受保护的健康信息进行保护和保密处理”。

根据美国卫生与公众服务部的说法，[HIPAA 隐私规则保护相关实体或其业务关联以任何形式或媒体(无论是电子、纸质还是口头)持有或传输的“个人可识别健康信息”。“简而言之，如果您持有与患者健康相关的任何类型的信息，而这些信息可能会识别出他们的身份，那么这些信息将受到 HIPAA 的保护。](https://www.hhs.gov/hipaa/for-professionals/privacy/laws-regulations/index.html)

例如，一家医疗营销机构要求电话跟踪完全符合 HIPAA，但是[仍然可以将电话跟踪与 Google AdWords 和 Analytics](https://www.callrail.com/blog/call-tracking-hipaa-healthcare-marketing/) 集成。理论上，为 AdWords、分析和客户报告提取数据可能会暴露客户的信息，因此属于 HIPAA 的范畴。

# 谁需要符合 HIPAA 标准？

你的应用或网站需要符合 HIPAA 吗？也许吧。例如，如果它允许患者记录他们的体重，然后制定锻炼计划，保持每日饮食计划并跟踪所述计划，或者查找参考信息，那么它可能不需要顺从。

但是，如果您的企业是医疗保健提供商的合作伙伴，或者提供商已与您的公司签约创建应用程序和相关网站，则您必须遵守 HIPAA 隐私法。HHS [也提供了一些其他例子](https://hipaaqsportal.hhs.gov/community-library/accounts/92/925889/Public/OCR-health-app-developer-scenarios-2-2016.pdf)来说明你的应用或网站是否需要兼容。

# 不合规的后果

不合规的后果可能很严重。正如辛辛那提大学的[所指出的](https://cahsonline.uc.edu/resources/mha/infographics/the-increasing-demand-for-board-certified-behavior-analysts/)，一个第 1 类违规，实际上是无法避免的，并且已采取措施遵守合规性规则，导致高达 50，000 美元的费用，每次违规的最低罚款为 100 美元。第 2 类，违规不可避免，但公司应该知道，情况相同，但每次违规至少罚款 1000 美元。第三类，故意疏忽，但试图改正，每次违规最低罚款 10，000 美元，最高罚款 50，000 美元。最后，第 4 类，故意疏忽而不试图改正，导致每次违规最低罚款 50，000 美元，最高罚款 150 万美元。

还可能有关联监禁，从 1 级违规(如不知道违规)的最高一年，到恶意获取个人信息的最高 10 年监禁的 3 级违规。

# GDPR

你可能还需要遵守[通用数据保护条例](https://hackernoon.com/im-a-developer-and-general-data-protection-regulation-gdpr-is-no-big-deal-or-is-it-2f2b7b3f124)，欧盟的新隐私法。其中一个要点是，你需要提供一份详细的清单，列出你的网站已经收集或存储的关于一个人的所有信息。当然，GDPR 还有很多关于如何存储、备份和访问网站数据的其他规定。请务必检查您的合规性。

# 如何做到安全

您如何保护您的公司并确保遵守 HIPAA？一般来说，大多数建议都是很好的安全建议。 [Duquesne 大学列出了以下违反 HIPAA 最严重的情况](https://onlinenursing.duq.edu/blog/staying-top-hipaa-2017/):丢失或被盗的设备可能被用来存储或访问机密数据；黑客攻击；员工不诚实，例如访问他们无权访问的信息；信息处置不当；第三方披露，但不确定该第三方是否也遵守规定；未经授权发布患者记录；未加密的数据；缺乏培训；不安全的记录；以及口口相传，例如在保密环境之外讨论敏感信息。

同样，其中许多都有简单的修复方法。你的信息数据库应该被加密和保护。有权访问患者记录的员工应使用[复杂密码](http://www.tgdaily.com/security/how-to-create-a-strong-and-secure-password)，只有绝对需要的员工才有权访问数据。

在客户端，利用[两步认证](https://hackernoon.com/dealing-with-national-medical-security-breaches-how-to-prepare-the-front-lines-of-cybersecurity-f31f2ab3d06f)和[电子签名](https://hackernoon.com/navigating-security-concerns-with-e-signatures-health-data-8e21ea15a14f)来防止违规。通过使用一个 [SSL 证书](https://goingclear.com/business-website-needs-ssl-certificate/)将 HTTP 改为 HTTPS 将保护敏感健康数据的传输。确保您使用的是高度加密。

如果出现违规，请务必遵守 [HIPAA 违规通知规则](https://www.hhs.gov/hipaa/for-professionals/breach-notification/index.html)，通知受影响的个人。如果超过 500 个人受到影响，你必须通知媒体和 HHS 秘书。