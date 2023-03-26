# AWS:一些避免“神圣法案”时刻的建议

> 原文：<https://medium.com/hackernoon/aws-some-tips-for-avoiding-those-holy-bill-moments-9e48e0cb1a83>

## 云是令人敬畏的，但是当它咬回你的钱包时，它会非常疼。

云是令人敬畏的:几乎 100%的可用性，几乎零维护，按需购买，最重要的是，无限扩展。

但是最后两个很容易反咬你一口，把这种牛逼变成一场收费噩梦。

偶尔你会看到这样的故事:

[](/@asankha/lambda-programming-errors-that-could-cost-you-thousands-of-dollars-a-day-265dfac354f) [## Lambda 编程错误可能会让你每天损失数千美元！

### 这是一个真实的故事。一个仍在发展的过程..一周之内，由于一个…

medium.com](/@asankha/lambda-programming-errors-that-could-cost-you-thousands-of-dollars-a-day-265dfac354f) ![](img/48d775ce29a075aa43c741f9c8658f34.png)

Holy Bill!

在这里，我公布一些我们从构建世界上第一个无服务器 IDE 的不太顺利的旅程中学到的技巧，可以帮助其他人避免一些“有趣的”陷阱。

# 小心那个配置！

我们学到的一件事是永远不要低估配置的力量。

如果你读了上面的链接文章，你会注意到这是一个简单的错误配置:一个 [CloudTrail 日志配置](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/enable-cloudtrail-events.html)将日志写到它已经监控的一个桶中。

你当然可以想出更复杂、更有创意的例子来创造“服务循环”,产生计费黑洞，但想法很简单:AWS 的智能取决于配置它的人。

![](img/e43a2994d40e53efbfc8cc855fa65e79.png)

Welcome to Infinite Loop

(嗯，上面的案例是我的一个同事配置的，*我是*验证的人；所以你可以停在这里，如果你喜欢它；) )

所以，当你准备提交一个新的配置更新时，试着重新考虑一下后果。你不会后悔的。

# 这是 S3，不是你的阁楼。

[AWS 估计](https://docs.aws.amazon.com/aws-technical-content/latest/cost-optimization-storage-optimization/introduction.html)7%的云计费浪费在“未使用”的存储上——被没有实际用途的内容占用的空间:过时的捆绑包、临时上传、旧主机等等。

## 水桶里的生活

然而，清理东西确实说起来容易做起来难。忘记一个废弃的文件比跟踪它并在适当的时候删除它要容易得多。

可能出于同样的原因，S3 提供了[生命周期配置](https://docs.aws.amazon.com/AmazonS3/latest/dev/intro-lifecycle-rules.html)——基于时间的自动清理计划。你可以简单的说“超过 7 天就删了这个”，7 天就没了。

这是保存临时存储(构建工件、一次性共享等)的理想方式。)在检查中，免提。

![](img/af83b1f384e667d781d521f67c1fe0df.png)

Like that daily garbage truck.

当您想从存储桶中删除大量文件时，生命周期配置也很方便；而不是删除单个文件(这本身会导致 API 开销——虽然删除是免费的，但列表不是！，你可以简单地[设置一个生命周期配置规则](https://docs.aws.amazon.com/AmazonS3/latest/dev/lifecycle-expire-general-considerations.html)，让所有东西在 1 天内过期。坐下来放松一下，S3 会替你做这件事的！

```
{
    "Rules": [
        {
            "Status": "Enabled",
            "Prefix": "",
            "Expiration": {
                "Days": 1
            }
        }
    ]
}
```

或者，你可以把不再需要但还没准备好的东西搬进[冰川，只需存储成本的一小部分](https://aws.amazon.com/glacier/pricing/)；比方说，对于子路径`archived`下的内容:

```
{
    "Rules": [
        {
            "Filter": {
                "Prefix": "archived"
            },
            "Status": "Enabled",
            "Transitions": [
                {
                    "Days": 1,
                    "StorageClass": "GLACIER"
                }
            ]
        }
    ]
}
```

但在此之前…

## 哎哟，有版本了！

*(灵感来源于真实事件。)*

我设置了一个生命周期配置来删除大约 3GB 的桶访问日志(显然是数百万个文件)，并认为一切都很好——直到一个月后，我收到了与上个月相同的 S3 账单:(

结果是 bucket 已经启用了版本控制，所以[删除并没有真正删除对象](https://docs.aws.amazon.com/AmazonS3/latest/dev/DeletingObjectVersions.html)。

因此，启用版本控制后，您需要明确地告诉 S3 生命周期逻辑:

*   [丢弃非当前(已删除)对象版本](https://docs.aws.amazon.com/AmazonS3/latest/dev/lifecycle-configuration-examples.html#lifecycle-config-conceptual-ex6)，以及
*   [过期旧删除标记](https://docs.aws.amazon.com/AmazonS3/latest/dev/lifecycle-configuration-examples.html#lifecycle-config-conceptual-ex7)

为了完全去掉"已删除"的内容和关联的 [*删除标记*](https://docs.aws.amazon.com/AmazonS3/latest/dev/DeleteMarker.html) 。

“简单”的存储服务到此为止；)

## CloudWatch 是你的伙伴

每当您想要[找出您的存储桶所占用的总大小](https://serverfault.com/questions/84815/how-can-i-get-the-size-of-an-amazon-s3-bucket#710080)，只需遍历您的`[AWS/S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/cloudwatch-monitoring.html)` [CloudWatch 度量名称空间](https://docs.aws.amazon.com/AmazonS3/latest/dev/cloudwatch-monitoring.html)。惊讶吧，惊讶吧，没有办法从 S3 本地检查桶的大小；就连 S3 的仪表盘都依赖于 CloudWatch，你为什么不呢？

查看所有内容的快速片段？(使用`bash`上的`[aws-cli](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html)`和`bc`)

```
yesterday=$(date -d @$((($(date +%s)-86400))) +%F)
for bucket in `aws s3api list-buckets --query 'Buckets[*].Name' --output text`; do
        size=$(aws cloudwatch get-metric-statistics --namespace AWS/S3 --start-time ${yesterday}T00:00:00 --end-time $(date +%F)T00:00:00 --period 86400 --metric-name BucketSizeBytes --dimensions Name=StorageType,Value=StandardStorage Name=BucketName,Value=$bucket --statistics Average --output text --query 'Datapoints[0].Average')
        if [ $size = "None" ]; then size=0; fi
        printf "%8.3f  %s\n" $(echo $size/1048576 | bc -l) $bucket
done
```

# EC2:清扫垃圾，堵塞漏洞

EC2 使管理虚拟机(计算、存储和网络)变得轻而易举。然而，它的简单性也意味着它可能会留下一些不为人知的垃圾和账单漏洞。

![](img/056af9d925b7d9d547f5e23f54a4a8d9.png)

EC2: Elastic Compute Cloud

## 选择您的实例类型

当创建一个新的实例时，有太多的设置。除非有特定的性能需求，否则选择一个具有[弹性块存储(EBS)支持的存储](https://aws.amazon.com/ebs/)和 2–4gb RAM 的 [T2 级实例类型](https://aws.amazon.com/ec2/instance-types/t2/)将足以满足大多数需求。

尽管符合免费等级标准，但是如果您的服务器在某个时候能够接收计算或内存密集型负载，`t2.micro`可能是一个 PITA 在这些情况下`t2.micro`倾向于简单地冻结(可能与[耗尽 CPU 信用](https://forums.aws.amazon.com/thread.jspa?messageID=789458#jive-message-holder)有关)？)，造成得不偿失的麻烦。

## 清理 ami 和快照

我们习惯于定期拍摄 EC2 实例的快照作为备份。其中一些被制作成[机器映像(AMIs)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html) 供重用或[与其他 AWS 用户共享](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/sharingamis-explicit.html)。

我们很容易忘记其他的快照。

虽然快照[不会因其完整卷大小](https://aws.amazon.com/ebs/pricing/#Amazon_EBS_Snapshots_to_Amazon_S3)而被计费，但随着时间的推移，它们会累积成大量垃圾。因此，定期访问并清理您的 [EC2 快照选项卡](https://console.aws.amazon.com/ec2/v2/home#Snapshots)非常重要。

此外，创建新的非盟特派团通常意味着旧的非盟特派团将被淘汰；他们也可以从 [AMIs 标签](https://console.aws.amazon.com/ec2/v2/home#Images)中“注销”。

但是…

## 谁是罪魁祸首——AMI 还是 snapshot？

实际费用在*快照*上，而不是在 AMIs 本身上。

这变得很棘手，因为[取消注册 AMI 不会自动删除相应的快照](https://serverfault.com/questions/892384/why-aws-snapshot-is-not-removed-after-de-registering-aws-ami)。

您通常需要复制 AMI ID，转到快照，在描述字段中查找 ID，然后销毁匹配的快照。或者，如果你很勇敢(也很懒)，选择并删除*所有*快照；AWS 将阻止您删除 AMI 正在使用的文件。

## 同样，对于实例和卷

当 EC2 实例运行时，对计算计费；但是它的[存储卷](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumes.html)一直在计费——直到被删除。

当您终止一个实例时，卷通常会被销毁；但是，如果您尝试过卷附件设置，分离的卷可能会留在您的帐户中。虽然没有附加到实例，但它们仍然占用空间；所以 AWS 对它们收费。

同样，只需转到[卷选项卡](https://console.aws.amazon.com/ec2/v2/home#Volumes)，选择处于“可用”状态的卷，并点击 delete 以永久删除它们。

## 标记 EC2 的东西:实例、卷、快照、ami 等等

![](img/a683f03c8ff3010d7a57b4a5f8e21613.png)

Tag ‘em.

创建快照时，很容易忘记实例中的状态。或者运行/停止的实例的目的，似乎没有人对此负责。

命名和标记有助于避免令人不快的意外(“你到底为什么删除上个月的生产快照？!");并且还能帮助你快速决定扔掉什么(“我们已经有了一个 11-05 的主快照，所以只需删除比它更早的)。

## 你停止使用，我们开始计费！

有时，AWS 领主以神秘的方式工作。

例如，[弹性 IP 地址(EIP)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html)只要附加到正在运行的实例，就是免费的。但是实例一停止，他们就开始按小时收费；或者它们是否以某种方式进入了“分离”状态(没有连接到正在运行的实例)。

对你将要注册的服务有一些预先的了解，可以防止这种时尚的一些令人讨厌的惊喜。快速的价格页面查询或谷歌可能是一个交易破坏者。

# 按使用付费与按分配付费

许多 AWS 服务遵循上述一种或两种模式。前者微不足道(你只需为你实际使用的时间/资源付费，其余时间享受零账单)，难以错过；但后者可能有点模糊，很容易被忽视。

考虑 EC2:您主要为[实例运行时](https://aws.amazon.com/ec2/pricing/on-demand/)付费，但是您也为存储(卷、快照、ami)和[网络](https://hackernoon.com/tagged/network)分配(比如**非活动**弹性 IP)付费，即使您的实例已经停止了几个月。

还有很多例子，特别是在[无服务器领域](https://hackernoon.com/why-serverless-why-now-f09ce43c4767)(我们自己也非常熟悉):

*   **Kinesis** 按碎片时间对[收费——即使你所有的碎片都是空闲的](https://aws.amazon.com/kinesis/data-streams/pricing/)
*   **DynamoBB** 根据“容量单位”对[存储和读/写进行收费](https://aws.amazon.com/dynamodb/pricing/) —幸好有一个不会过期的自由层！
*   **RDS** (非常类似于 EC2)对实例运行时收费，不管是忙还是闲([极光无服务器](https://aws.amazon.com/rds/aurora/pricing/)似乎试图在某种程度上改变这一点)
*   **KMS** 对每个客户管理密钥[收取固定费用(CMK)](https://aws.amazon.com/kms/pricing/) 不管你是否使用它

![](img/228b78b4eb6d6b2e853d7a22975db438.png)

Each block adds a bit more to your cost.

与此同时，一些服务秘密建立自己的监控、备份和其他“实用”实体。这些，虽然(大概！)本意是做好事，可偷偷渗入你的账单:

*   DynamoDB 设置 [CloudWatch 告警](https://github.com/awslabs/serverless-application-model/issues/507)；即使在相应的表被删除后，这些表仍会保留下来(至少在通过 CloudFormation 管理时)。
*   RDS 在终止时以及日常维护(esp)期间自动创建[实例卷快照](https://aws.amazon.com/rds/details/backup/)。当通过[“默认”云编队配置](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-rds-database-instance.html)部署时；这些很容易超过您的存储配额

这些都是我们 [AWS](https://hackernoon.com/tagged/aws) 账单中经常出现的罪魁祸首；当然还有更好的例子，但是你明白了。

# CloudWatch(是啊，又来了)

许多服务已经——或者可以配置为——向 CloudWatch 报告[使用指标](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/aws-services-cloudwatch-metrics.html)。因此，了解了哪些指标映射到哪些计费组件后(例如，S3 存储成本由跨越`AWS/S3`名称空间所有条目的`BucketSizeBytes`指标的总和表示)，您可以围绕 [CloudWatch 指标](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)构建一个完整的计费和监控解决方案(或者将工作委托给第三方服务，如 [DataDog](https://www.datadoghq.com/) )。

![](img/88c73ea0f65761082c96dfc0c8188a3e.png)

CloudWatch

CloudWatch 本身基本上是免费的，它的指标有自动的总结机制，所以你不必担心它会被陈旧的垃圾淹没，或者被超出限制的容量账单淹没。

# 计费 API

虽然 AWS 确实有一个专用的[计费仪表板](https://console.aws.amazon.com/billing/home)，但每天登录并查看它并不是你会添加到你的日程中的事情(至少对你我这样的 API/CLI 头脑来说不是)。

幸运的是，AWS 提供了一个[计费 API](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/ce-api.html) ,通过它，您可以在任何首选时间段内获得当前未结账单的详细视图——按服务或实际 API 操作细分。

问题是，这个 API 不是免费的:每次调用要花费你 0.01 美元。当然，这是可以忽略不计的——考虑到不得不支付几十笔费用的风险——甚至在某些情况下支付数百或数千美元——有一个每月 0.30 美元的账单监视器来跟踪任何异常情况是值得的，以免为时过晚。

思考:随着对谷歌云功能的[提供的](https://cloud.google.com/blog/products/gcp/introducing-headless-chrome-support-in-cloud-functions-and-app-engine)[无头浏览器](https://developers.google.com/web/updates/2017/04/headless-chrome)的支持，人们可能能够建立一个无服务器的工作流程，登录 AWS 仪表板并为你检查账单。一些可以在空闲时间尝试的东西(如果一些有独创性的人还没有一起破解的话)。

# 计费提醒

奇怪的是(或许不是；))AWS 没有提供一种为计费设置硬性限制的方法；尽管[大量的用户请求](https://news.ycombinator.com/item?id=13072296)和令人不安的事件报道遍布网络。相反，他们为各种计费“级别”提供[警报](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html)；您可以通过电子邮件或社交网络订阅通知，如“账单金额为限额的 x%”和“超出限额”(方便通过 Lambda 实现[自动化！).](https://slappforge.com/docs/sigma/components/aws/sns.html#sns-as-a-trigger)

**我的建议:**这是每个 AWS 账户必备的**。如果我们有一个，到目前为止，我们已经节省了数千美元。**

**![](img/a6bcbab663e6ccec0ca7648d7277c754.png)**

**Don’t wait till they become worthless pieces of plastic.**

# **组织帐户**

**如果您想将 AWS 访问授权给第三方(测试团队、基于合同的开发人员、演示用户等)。)，通过将您的 root 帐户转换为启用了合并计费的 [AWS 组织来创建子帐户可能是个好主意。](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/consolidated-billing.html)**

**(虽然使用一个 [IAM 用户](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html)可以做几乎相同的事情，但是它不提供资源隔离；所有的东西都将被塞进同一个账户，而且可能需要煞费苦心的复杂 IAM 策略来隔离用户之间的实体。)**

**我们的[首席执行官兼同事 Asankha](https://lk.linkedin.com/in/asankha) 已经[就此写了相当全面的文章](/@asankha/creating-isolated-aws-accounts-for-testing-and-experimentation-9795a8d2e2de)，所以我就讲到这里。**

# **最后:显示器。监视器。监视器。**

**没有必要强调这一点——我没完没了的漫谈已经传达了它的重要性。**

**所以，祝你好运！**

***原载于 2018 年 11 月 30 日*[*randomizd.blogspot.com*](http://randomizd.blogspot.com/2018/11/aws-some-tips-for-avoiding-those-holy.html?m=1)*。***