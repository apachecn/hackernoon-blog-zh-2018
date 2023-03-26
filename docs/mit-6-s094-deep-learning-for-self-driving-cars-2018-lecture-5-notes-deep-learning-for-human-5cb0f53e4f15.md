# 麻省理工 6。S094:自动驾驶汽车的深度学习 2018 讲座 5 笔记:人类感知的深度学习

> 原文：<https://medium.com/hackernoon/mit-6-s094-deep-learning-for-self-driving-cars-2018-lecture-5-notes-deep-learning-for-human-5cb0f53e4f15>

> 你可以在 Twitter @bhutanisanyam1 找到我，在 [Linkedin 这里](https://www.linkedin.com/in/sanyambhutani/)
> 这里[这里](https://becominghuman.ai/a-self-driving-new-year-33284e592f35)和[这里](https://hackernoon.com/a-self-driving-new-year-2-d1bbc5a83570)是我的两篇文章
> 
> [ing](https://hackernoon.com/tagged/learning) 通向自动驾驶汽车的道路
> 
> [你可以在这里找到降价文件](https://github.com/init27/MIT-6.S094-Deep-Learning-for-Self-Driving-Cars)
> 
> [你可以在这里找到第一讲的笔记](/init27-labs/mit-6-s094-deep-learning-for-self-driving-cars-2018-lecture-1-notes-807be1a50893)
> [第二讲的笔记可以在这里找到](https://hackernoon.com/mit-6-s094-deep-learning-for-self-driving-cars-2018-lecture-2-notes-e283b9ec10a0)
> [第三讲的笔记可以在这里找到](https://hackernoon.com/mit-6-s094-deep-learning-for-self-driving-cars-2018-lecture-3-notes-deep-reinforcement-learning-fe9a8592e14a)
> [第四讲的笔记可以在这里找到](https://hackernoon.com/mit-6-s094-deep-learning-for-self-driving-cars-2018-lecture-4-notes-computer-vision-f591f14b3b99)
> 
> 这些是麻省理工学院六年级第四讲的笔记。S094:自动驾驶汽车深度学习课程(2018)，由[莱克斯·弗里德曼](https://twitter.com/lexfridman)教授。

所有图片均来自讲座幻灯片。

![](img/4fec38b4828adbddc60c6b35efea2412.png)

应用数字逻辑理解人类的感觉

聚焦[计算机视觉](https://hackernoon.com/tagged/computer-vision)。

我们如何使用 CV 从视频中提取有用的信息(以汽车为例)

![](img/11935ded44648e816209d133a3e93aae.png)

**针对人类感知的深度学习:**
利用 CV、DL 创建在现实世界中运行的系统。

![](img/6aeea6b5c40590137569c0d75bc523b9.png)

要求(根据重要性排序):

*   数据:需要大量的真实数据。数据收集是最难也是最重要的部分。
*   半监督:
    原始数据需要简化为有意义的代表性案例，原始数据需要注释。
    我们需要收集数据，并使用半监督技术来寻找可用于训练我们网络的数据片段
*   高效的注释:
    好的注释带来好的性能。
    不同场景的标注技术完全不同。例如:用于扫视分类的注释工具与用于身体姿势估计的注释以及用于 SegFuse 的图像像素级标记
*   硬件:
    大量数据需要大规模分布式计算和存储。
*   算法:
    我们希望算法能够自我校准，允许一般化。
*   当前的算法主要是基于图像，而不是基于时间/序列。

外卖:数据收集，清洗比算法更重要。

# 人类的不完美

*   分心驾驶:【2014 年，3179 人因分心驾驶而丧生，43.1 万多人受伤。
*   眼睛离开路面:
    5 秒是平均时间，发短信时你的眼睛离开路面。
*   酒后驾车:2014 年 31%的交通死亡事故是由酒后驾车造成的。
*   吸毒驾驶:
    23%的夜间司机是吸毒司机(2014)。
*   昏昏欲睡的驾驶:所有交通死亡事故中有 3%涉及一名昏昏欲睡的司机。

![](img/06d177c0c845457062b741696a0772dc.png)![](img/29cc8bd6d0732b6eb48e9d9f38a91a72.png)

鉴于这些缺陷，以及第一讲中讨论的通往自主未来的两条道路(以人为中心与完全自主):
**以人为中心的想法是个坏主意吗？**

![](img/4d3cd03cf713cef3395be792f5c95684.png)![](img/f0109004f6eaddd2a35a51e4055be57a.png)

*   人类可能倾向于“过度信任”;系统。

![](img/0cc08a7ae517a80479763838a003be2a.png)

**麻省理工学院-AVT 自然驾驶数据集**

![](img/1b853cb00d4333a79ccc3d4fe0477425.png)

数据收集:

*   两个+一个摄像头。
*   **摄像机 1** :捕捉脸部高清视频，进行目光识别，估计认知负荷。
*   **摄像机 2** :(鱼眼)估计身体姿势-手放在方向盘上，活动识别。
*   **摄像机 3** :记录室外场景，进行全场景分割。

收集的数据提供了对以下方面的洞察

![](img/61645c76125dec9d060ca384f65e43b7.png)

*   人类行为。
*   展开自治。
*   用于训练感知任务的深度神经网络的算法设计。

**安全 Vs 偏好自动驾驶？**

![](img/e6ef9fe865a36c913109961d4467edf8.png)![](img/669f5c372bddf6d89cc0502d716a7d08.png)

*   数据集显示，无论自动驾驶仪是否打开，身体接触都保持不变。

![](img/2d07fe249ed443e6af950f6a1c554e11.png)

*   所以，自动驾驶允许身体接触。但是司机并没有过分信任这些系统

![](img/bab22ecea885bb5b6ed6b2166041265a.png)

# 行人检测

挑战:

*   不同的外观:类间变异。
*   不同的发音。
*   咬合配件。
*   互相遮挡的行人。

解决方案:
需要的是从原始像素中提取特征。

滑动图像:

*   哈尔瀑布。
*   猪。
*   CNN。

更多智能网络:

![](img/6b239baaffd1d575487c4dd5fb2f13ea.png)

*   快速 R-CNN。
*   屏蔽 RCNN。
*   体素网。

这些网络生成要考虑的候选，而不是滑动窗口方法，提供要考虑的子集。

*   使用 CNN 分类器来检测是否存在感兴趣的对象。
*   使用非最大抑制移除重叠的边界框。

![](img/5f807e191cb1b4ee5d7d763924038f81.png)![](img/2fd4df55b7d649d45bda3c4c5d5e2c5d.png)

数据(来自不同的交叉点):

*   每天录 10 个小时。
*   Aprrox 12，000 行人过马路。
*   21M+特征向量样本。
*   RCNN 对行人进行包围盒检测。

# 身体姿态估计

包括:

![](img/91ad71a16c155d9369cbb9c0fe6d600a.png)

*   在图像中寻找关节。
*   图像中的标志点。

为什么重要？

*   以确定驱动器的对准。
*   注意:一般安全气囊在假设驾驶员面向前方的情况下展开。
*   随着自动化程度的提高，这种假设可能会失败。

**顺序检测方法**

![](img/b08f0381bbc6134de59f2c085214d720.png)

*   检测手，然后检测脚步、肩膀等等。
*   传统方法

**深层整体观:**

![](img/556c6e69de84a79134a5961bca5c8f48.png)

*   强大的，成功的多人，多姿态检测。
*   对来自完整图像的检测部分单独执行回归，而不是顺序检测。
*   之后，它将检测到的关节缝合在一起。
*   允许检测各种姿势和不可见的关节。

**姿态回归器级联:**

![](img/7528c3edd5f980803b43e7a0120bacf5.png)

*   接受原始图像并产生每个关节的 X-Y 估计位置的 CNN。
*   每个估计放大并产生关节的重复的更精细的检测和估计。

**零件检测:**

![](img/0329da90ce6548b3caee425732b6c528.png)

*   我们可以使用这种方法来检测多个人的照片中的部分。

![](img/f2cdbca61545ffc76da9d33f98ef8eed.png)

*   首先，检测身体部位，而不首先进行个人检测。
*   接下来将它们连接在一起。

![](img/067aac7b88f14bd10bcc7b65de018946.png)

*   通过两部分匹配，将不同的人缝合在一起。

![](img/1ba0d8050fe6a953675d896b6a508cba.png)

*   这是麻省理工学院用来检测上身部分的方法。
*   驾驶员位置与标准前向位置。

![](img/93c0550130a2a6968a63db30f8de27b2.png)

*   时间与颈部位置的关系图。

![](img/5b8730ea4cc5a383e554709bf3de9332.png)

*   行人的身体姿态估计。
*   这允许检测行人过马路并看着车辆时发生的“非语言”交流的动态。
*   有趣的发现:大多数人在过马路前都会把目光从驶来的车辆上移开。

# 扫视分类:

![](img/5c4135fddf6d2454e64d953b80526ef6.png)

*   确定司机在看哪里。
*   注意:这与凝视检测不同，在凝视检测中，我们试图找到(x，y，z)姿势。
    我们分为两个区域:公路/越野。
    或六个区域:
    -上路
    -越野
    -左
    -右
    -仪表板
    -后视镜
    分类允许作为 ML 问题处理。
*   同样可以扩展到行人，以确定他们是否在看着/远离正在靠近的车辆。
*   注:地面数据由人工标注提供。

![](img/1eac3c1e3e606925c60f9324e657023e.png)

**面部对齐:**

![](img/b97b80d502be298bfc7587d5246e2540.png)

*   设计能够检测面部的单个标志和估计头部姿态的算法。

![](img/c1432b0fde271cedaad3b0ea30ac9ffb.png)

凝视分类管道:

*   源视频
*   校准:
    确定传感器的位置，因为它基于区域。
*   视频稳定。
*   人脸检测。
*   面部对齐。
*   眼睛/瞳孔检测。
*   头部(和眼睛)姿态估计。
*   分类。
*   决策修剪。

![](img/cbe699b2a264b683edcb8d2ac35cd9ab.png)

标注工具:
半自动:网络不确定的数据被手工标注。

基本权衡:
我们愿意忍受的精确度是多少？

为了提高准确性，人工迭代和注释数据。

误报:
可以通过更多的训练数据来处理。
某种程度的人工注释修复了一些问题。

# 驾驶员状态检测:

![](img/9f66b7dc1006cc422442fd5bdee0c5f3.png)![](img/bed997ea90c9f7f42277da242ba54ee9.png)

驾驶员的情绪检测。

*   许多分类情感的方法。

![](img/37ad7071520863accc45596c00ff3ada.png)

*   检测情绪的一般情况。

![](img/e39126047d88134ddb169d6742a74e8b.png)

*   例如:Affectiva SDK。

![](img/4bdf57186af765a1663fcc6782e82ef3.png)

*   这些算法将我们的表情映射到情感。

![](img/1e3592add06313eba1b5e90e70c25015.png)

*   应用特定的情感识别:
    -例如:使用基于语音的 GPS 交互
    -自我注释。
    -通用情绪检测器在这里失效，因为在驾驶时，“微笑=沮丧”。
    ——因此注释很重要。数据必须被标记以反映这些情况。

# 认知负荷:

一个人精神上忙碌的程度。

![](img/84c62f34bfaa5001e0b192ad4c4cb2e4.png)

*   眼睛的扩张和收缩基于认知负荷，动作也反映了深刻的思想。

![](img/ea2be876313804ac2ca538a4f21bdbca.png)

*   认知负荷可以通过眨眼动力学、眼球运动和瞳孔扩张来检测。

![](img/5f3808ad368a7fa03e47b20a691db8f9.png)

*   然而，在现实世界中，照明忽略了瞳孔放大。
*   利用了眨眼动力和眼球运动。

![](img/f86288be50b68f40797400766c7ea650.png)

*   3D 卷积神经网络:
    -输入一系列图像，我们使用 3D 卷积。
    -对多个图像/通道进行卷积。
    -允许[通过时间学习](https://hackernoon.com/tagged/learning)动态。
*   真实世界数据:
    N-back 任务估计认知负荷。

![](img/3073645b4795528a048b0412dbd2ed65.png)

*   我们检测人脸，提取眼睛，然后输入 CNN。

![](img/79fa08d08fe976b85de9914af8ee039a.png)

*   眼动图与认知负荷。

![](img/4b6470d752028c872ca099256488083a.png)![](img/2fa83645eca0c3f349c3dd0d40004d1f.png)

*   标准 3D CNN 架构。

![](img/357100b8d67ff80b545799c4e146b4b8.png)

*   真实世界数据的准确性。

![](img/452ebf7f55b3f2bd8354209f5f285497.png)

# 以人为中心的自动驾驶汽车愿景:

![](img/3a910faefe8f9d71860c7917ed20f8c3.png)

*   尽管我们正在研究感知，利用传感器进行定位和路径规划。我们离解决这个问题还很遥远(论点:20+年)。因此，人类必须参与其中。

![](img/bce020bf3edcb246d078a18f6f9700cc.png)

*   因此,“机器人”需要理解“人的活动”,人与机器人的互动也需要改进。

![](img/f2171974ad1f8085426ae4163ca4c21b.png)

*   大规模自动化之路。
    (不再有方向盘)
*   以人为中心的自主:
    -SDC 是一个个人机器人，而不是一个感知控制系统。
    -“控制权转移”涉及与机器的“个人”联系。
    - SDCs 的覆盖面将会很广。

![](img/a25350ad817e7c0965f85ffcecadc367.png)

*   预告:麻省理工学院 SDC 分校将于 2018 年 3 月在公共街道上首次亮相(公开测试)。
*   接下来呢？
    -深度交通
    -深度碰撞
    - SegFuse

> [你可以在 Twitter @bhutanisanyam1](http://twitter.com/bhutanisanyam1) 找到我，在 [Linkedin 这里](https://www.linkedin.com/in/sanyambhutani/)
> 这里[这里](https://becominghuman.ai/a-self-driving-new-year-33284e592f35)和[这里](https://hackernoon.com/a-self-driving-new-year-2-d1bbc5a83570)是我学习[自动驾驶汽车](https://hackernoon.com/tagged/self-driving-cars)道路上的两篇文章
> 
> [订阅我的时事通讯，获取深度学习、计算机视觉文章的每周精选列表](https://tinyletter.com/sanyambhutani)