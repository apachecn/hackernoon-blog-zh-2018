# 开发者如何为 GDPR 立法准备他们的应用

> 原文：<https://medium.com/hackernoon/how-developers-can-prepare-their-apps-for-gdpr-legislation-20968e426f89>

![](img/28adf28f187d37158246dfca3391db17.png)

*当*[](https://hackernoon.com/tagged/gdpr)**被关注时，开发者不能忽视 app 用户隐私、同意和选择加入偏好。这里有五个小贴士可以让你变得顺从。**

*对于应用发行商来说，这是一个巨大的问题。你如何[遵守令人生畏的隐私立法](https://www.tamoco.com/blog/gdpr-app-devlopers-privacy)并最大限度地增加选择你的应用服务的用户数量？*

*据估计，目前超过 50%的应用程序不符合 GDPR 的新法规。*

*这是因为应用程序集成了多个第三方和 SDK。其中很多都是要求用户的数据。*

*出版商很难跟踪这一点。但是现在法律规定要控制这些数据。*

*遵守隐私法规不应该如此困难。你的用户选择加入或退出个人偏好应该不难。*

*幸运的是，我们认为我们已经为开发人员找到了一个在他们的移动应用套件中管理、同步和审核同意的解决方案。*

# *征求同意并让你的用户选择加入*

*![](img/017fc5a3221244a9571e417a51b942a8.png)*

*遵守隐私立法并不是最简单的过程。*

*你如何确保你不会吓到你的用户选择退出所有的服务？获得用户选择加入非常重要，因为它可以成为推动参与和保留的重要工具，更不用说货币化了。*

*你需要让用户在合适的时候选择加入。你需要清楚他们在控制。我们试图通过设计我们的[同意工具包](https://www.tamoco.com/app-consent-toolkit)来解决这个问题，以帮助开发者获得和管理用户同意。*

*许多应用程序选择加入的时间是错误的。用户第一次打开应用程序时，不要询问所有权限。解释用户选择某个权限的回报是什么，意味着用户对他们的数据用途有了更好的了解。*

*确保你的选择加入过程清晰明了，并对你的用户坦诚。*

# *尊重地管理用户退出请求*

*![](img/4930d1c1ca560573afcea336a9d19128.png)*

*在新的立法下，确保用户可以选择退出与首先适当地获得同意同样重要。要做到这一点，发布者必须有一个适当的系统，允许他们的用户选择退出他们以前选择的一些或所有权限。*

*这是塑造我们的[同意模块](https://www.tamoco.com/app-consent-toolkit)工作方式的基础之一。我们希望我们的工具包能让用户选择退出和选择加入一样容易。这样做的方式不仅要让用户控制他们的数据，还要允许他们选择发布者使用的数据类型。*

# *确保您可以跨设备管理同意*

*![](img/4d17f3ea152239b4c0ec6769f381508b.png)*

*同意和用户选择加入[管理](https://hackernoon.com/tagged/management)是很难做到的。但是，当你考虑到应用程序用户不断删除应用程序和更换设备的事实时，这几乎是不可能的。*

*同步用户设置很重要，因为如果用户已经撤销了一台设备的权限，那么继续使用该设备可能会违反隐私法规。此外，如果用户请求删除其所有数据，则很难做到这一点，除非您可以在任何地方识别该用户有权访问的数据。*

*这也是[同意工具包](https://www.tamoco.com/app-consent-toolkit)所要解决的问题之一。通过使用一系列唯一的标识符，开发人员可以使用该工具包同步同意首选项。通过这种方式，每当用户与应用或服务交互时，同意工具包管理用户的同意和选择加入/选择退出偏好。*

*当用户要求删除他们的数据时(或者用 GDPR 的话来说——被遗忘的权利),这尤其有用。拥有一个跨设备同步的工具包允许发布者删除这些数据，并在将来用户出现的任何地方停止收集。*

*有时，它是一个混乱的基础架构。如果用户在一个应用程序中更新了同意首选项，但使用了您的其他应用程序，会发生什么？确保你能在你的房产中同步这个偏好。*

# *将用户同意与第三方集成*

*![](img/5c9c9db21f8bf2332070c546538f5dc3.png)*

*应用程序很少单独运行。您可能有第三方服务或其他 SDK 可以访问我们用户的数据。*

*这些需要与用户的选择加入偏好保持同步。例如，如果您的用户拒绝交流，这需要通过运行[行为目标](https://www.tamoco.com/blog/what-is-behavioral-targeting/)活动的第三方广告商来更新。*

*在塔莫科，我们的[同意模块](https://www.tamoco.com/app-consent-toolkit)允许应用程序用新的用户偏好立即更新第三方。如果用户要求删除其所有历史数据，则需要将该信息转发给第三方。*

*当用户的首选项更新时，同意软件开发工具包会自动将此信息传达给第三方。*

*这方面的信息随后被保存在安全的审计跟踪中。同意模块将自动要求第三方确认他们已经收到这些改变用户偏好的请求。当收到(或未收到)此信息时，它将与时间戳和相关信息一起保存在审计跟踪中。*

*这意味着开发者可以确保他们的用户的选择加入偏好在第三方集成中得到尊重。能够跟踪审计线索以证明该信息被传递给第三方合作伙伴和集成(如 SDK)是非常重要的。*

# *确保你有一个安全的审计线索*

*![](img/07fc79287964ba7f361223d55e4119e7.png)*

*有了正确的程序，开发人员就不需要担心手动管理同意。但是，如果你需要证明你的应用程序保护了用户数据，会发生什么呢？*

*应用开发者需要一种存储用户同意历史的方式。开发商应该很容易证明已经获得历史同意。*

*在我们的[同意工具包](https://www.tamoco.com/app-consent-toolkit)中，我们为开发人员提供了一个审计跟踪来做到这一点。每当用户改变他们的同意偏好时，SDK 会自动用时间戳记录下来。*

*这确保了应用发行商总是被覆盖。这些信息易于查看并提供参考。第三方同意也存储在审计跟踪中。所有选择退出的请求都发送给第三方，然后将相关记录存储在审计中。*

*同意工具包在有限的时间内免费提供给早期用户。负责人[在这里](https://www.tamoco.com/app-consent-toolkit)获得权限。*