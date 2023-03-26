# 我们如何尽可能合乎道德地对待人脸识别和执法

> 原文：<https://medium.com/hackernoon/how-we-approach-face-recognition-and-law-enforcement-as-ethically-as-we-can-3fdb4f87c6bc>

![](img/cc131644803015a8b99becd0bc1f7a38.png)

[https://pixabay.com/en/camera-graffiti-security-cctv-89012/](https://pixabay.com/en/camera-graffiti-security-cctv-89012/)

在 Machine Box，我们一直致力于让您能够使用简单的机器学习工具来构建令人惊叹的东西。这种方法的一个重要方面是所有权。当您使用 Machine Box 时，您可以完全控制您的所有数据，这在监控和人脸识别领域最为重要。

回到 2018 年 5 月，[这来到了我们脑海中的最前沿](https://www.nytimes.com/2018/05/22/technology/amazon-facial-recognition.html)。

> 美国公民自由联盟领导了 20 多个民权组织，要求亚马逊停止向执法部门出售其名为 Rekognition 的图像识别系统。

当时，我记得我试图让自己的脑袋明白这种反弹对人脸识别的普遍影响。一方面，我是个人隐私的强烈拥护者，不仅是政府机构，还有试图跟踪我每一个想法的大公司。另一方面，我是一起犯罪的受害者，如果有监控摄像头的话，这起犯罪本来是可以避免的——或者至少由此产生的视频可以帮助执法部门在后来认出他。所以，就像其他人一样，我必须努力解决安全与自由这个古老的问题。

我提出这个问题的原因是因为收购机器盒子[的公司最近宣布了](https://www.veritone.com/insights/veritone-announces-ai-powered-law-enforcement-application-suite/)两个新的应用程序，可以使用 [Facebox](https://machinebox.io/docs/facebox?utm_source=Medium&utm_medium=Post&utm_campaign=Ethics%20of%20surveillance) 来识别执法视频中的已知罪犯。

> 作为 aiWARE 应用程序套件的自然扩展，各机构现在不仅能够智能地搜索以找到相关证据，还能在分发证据之前识别嫌疑人并编辑其中的敏感材料。

我也强烈主张在执法中使用视频，因为它可以成为真理的卓越仲裁者。但是，如果执法机构必须手动筛选，捕捉所有这些视频不会有太大帮助。此外，如果没有人工智能能力的帮助，以理解所捕获的视频，遵守证据法和其他考虑因素将变得更加困难。视频太多了。

我们想要视频，但我们也必须在帮助执法部门保护我们的安全和支持老大哥之间划清界限。

这就是为什么我更喜欢这些应用程序使用 Facebox。Facebox 是一个 Docker 容器，可以在私有或公共云环境中运行，其中数据的进出完全由应用程序控制。在任一部署选项中，都没有开放式的云端点，面部数据会消失在其中。相反，作为开发人员，您可以 100%控制您的数据——从训练数据、状态文件，到您呈现给 Facebox 的任何后续数据。

你会注意到，在新闻稿中，它提到了这样一个事实，即用于训练人脸识别的唯一人脸是由特定执法机构使用该应用程序提供的现有罪犯或相关人员的人脸。因为 [Facebox](https://machinebox.io/docs/facebox?utm_source=Medium&utm_medium=Post&utm_campaign=Ethics%20of%20surveillance) 以这样一种受控的方式运行，所以所有的训练数据都不会离开执法机构的监管链。它被视为在犯罪现场发现的指纹。

![](img/741db8f3de3b2af689f9fc8d684c9081.png)

这与我们的使命是一致的，我们的使命是让组织将他们的数据保持私有并在他们的控制之下。

此外，Facebox 允许删除或忘记面部数据和其中的所有生物特征属性。其他人脸识别服务可能不允许这样做，要么是因为他们的业务目标需要永久保留这些数据，要么是因为他们在技术上不具备这些能力。例如，在 [Google Vision 的 API 服务条款](https://developers.google.com/terms/#b_submission_of_content)中规定:

> b.内容提交
> 我们的一些 API 允许提交内容。除非条款中明确规定，否则 Google 不会获得您通过 API 客户端提交给我们的 API 的内容的任何知识产权的所有权。出于使 Google 能够提供、保护和改进 API(及相关服务)的唯一目的，并且仅在符合适用的 Google 隐私政策的情况下，**您授予 Google 永久、不可撤销、全球、可再许可、免版税和非排他性的许可，以使用通过您的 API 客户端向 API 提交、发布或显示的内容。“使用”是指使用、托管、存储、修改、交流和发布。**在您通过您的 API 客户端向我们的 API 提交内容之前，您将确保您拥有授予我们许可的必要权利(包括您的最终用户的必要权利)。

(强调我的)

我们加入 Veritone 是因为,它不仅让我们有能力扩展机器盒子的范围，还让这些工具的力量民主化。这一步的一部分是正面解决这些用例，并尽最大努力维护我们的价值观。