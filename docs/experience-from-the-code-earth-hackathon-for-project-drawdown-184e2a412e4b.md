# 来自`/earth hackathon for Project Drawdown`的经验

> 原文：<https://medium.com/hackernoon/experience-from-the-code-earth-hackathon-for-project-drawdown-184e2a412e4b>

[项目削减](https://www.drawdown.org/)是有史以来为扭转全球变暖提出的最全面的计划。

2018 年 9 月 5 日、6 日、7 日，亨利·普尔(Henry Poole)创办的一个名为 `/earth 的项目举办了一场小型的项目降额黑客马拉松。我担任过 MC 和敏捷教练。[互联网档案馆](https://archive.org/)好心地在他们位于三藩市的漂亮建筑里为我们提供了会面的空间。目标是启动一个新的自由开源软件(FLOSS)项目。除了五名开发人员和五名科学家以及来自项目缩减的志愿者，我们很荣幸有[理查德·斯托尔曼](https://en.wikipedia.org/wiki/Richard_Stallman)每天出席几个小时。`

Richard Stallman

Ryan Allard

Drawdown 开发了一种建模方法，允许您预测采用各种*解决方案*对温室气体排放的影响。到目前为止，80 个解决方案的例子包括:更多地采用屋顶太阳能，更多地使用 silvopasture，在全球范围内为女孩提供更好的教育，等等。这些模型使用世界各地研究人员收集的可靠数据，并根据非常全面的*模型*计算排放量。这些模型有许多输入和参数，但在数学上并不特别复杂。通过对这些模型和大量数据的大量细致工作，我们有可能预测如何扭转全球变暖——开始减少温室气体。项目削减是关于解决方案，而不是问题，这是一个从根本上充满希望的项目:它表明人类可以控制自己的命运，扭转我们今天乘坐的慢动作全球列车残骸。

Chad Frischmann

但是还有改进的空间，这就是我们在那里的原因。模型在微软 Excel 中。它们不是秘密，但也不是完全自由和公开的。由于在 Excel 中，它们只能被有限的难以控制版本的用户理解、改进和破解。我们相信，以下几项行动将使这些气候模型可供全世界的研究人员和决策者使用:

*   转向 Python，这是一个比 VBA 更友好的建模系统。
*   使用现代软件工程方法，例如 *git* 进行版本控制、持续集成和自动化测试。
*   将数据与计算分开。
*   最终，建立一个现代的浏览器交付平台。
*   使用现代自由软件许可证，允许其他人使用和贡献模型。

Owen Barton

项目缩减在战略上是开放的，但在战术上并不精通自由软件运动的原则。特别是，他们没有“发布”或许可这些电子表格。这次黑客马拉松的最大成果之一是，亨利、理查德·斯托尔曼和一位名叫马克·琼斯的编码律师有机会与 Drawdown 的副总裁兼研究主管查德·弗里希曼(Chad Frischmann)交换意见。他们暂时同意使用 [Gnu Affero 通用公共许可证](https://www.gnu.org/licenses/agpl-3.0.en.html)，有待其董事会批准。

Marc Jones

与此同时，欧文·巴顿、丹顿·金特里和我正在构建代码的初始 Python 实例。Ryan Allard 花了一整天的时间进行讨论和解释，才让开发人员开始说科学家和建模人员的语言。我以紧迫感对待每一个问题，但有些事情不能操之过急。如果你第一次把开发人员和科学家聚在一起，计划花一些时间共同学习彼此的行话。

Stephanie Liu

欧文和我砍了一晚上，直到筋疲力尽。我愿意相信我们不是工作狂，但是我们都已经走了很长的路，希望在这短短的三天里最大限度地发挥我们的影响。

Owen 构建了 Python web 服务。我觉得允许使用[扼杀者模式](https://www.martinfowler.com/bliki/StranglerApplication.html)逐步替换 Excel 系统是非常重要的，所以我破解了 Excel 电子表格来调用 web 服务。这允许现有的电子表格继续使用，但是调用 Python。首先，我们在 Python 中实现了一个减法，以得到一个端到端的“尖峰”。事实上，直到星期五早上，我们还没有完全达到我们需要的桌子的大小。非常感谢 [VBA 网站](https://github.com/VBA-tools/VBA-Web)，一个麻省理工学院许可的开源项目，没有它 Excel 集成是不可能的。

与此同时，Denton 开发了一个“黄金标准”测试，实际上启动 Excel，计算模型，并提取数据值的样本。这是一个“集成测试”,应该会给我们和未来的开发者对我们的编码很大的信心。

Denton Gentry

在最后一天，欧文完成了模型工作中更完整的部分，大概是全部工作的 1/10。与 Drawdown 的科学家合作，我们开发了一个[迷你路线图](https://gitlab.com/RobertLRead/drawdown/blob/master/README.md#road-map)供未来的程序员遵循。已经开发了集成测试和单元测试，以及功能性的(虽然笨拙)Excel 数据比较技术，我们相信我们完成了黑客马拉松的基本任务:现在有了现代的 Python [FLOSS repo](https://gitlab.com/codeearth/drawdown) ，志愿者可以参与进来，帮助项目削减，促进全球变暖的解决方案。

Henry Poole

Robert L. Read