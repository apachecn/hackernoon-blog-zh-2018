# 直觉 RL:优势介绍-演员-评论家(A2C)

> 原文：<https://medium.com/hackernoon/intuitive-rl-intro-to-advantage-actor-critic-a2c-4ff545978752>

强化学习(RL)实践者已经产生了许多优秀的教程。然而，大多数用数学方程和抽象图表来描述 RL。我们喜欢从不同的角度思考这个领域。RL 本身是受动物如何学习的启发，那么为什么不将底层的 RL 机制转换回它们被设计来模仿的自然现象呢？人类通过故事学习得最好。

这是一个关于演员优势评论家(A2C)模型的故事。行动者-批评家模型是政策梯度模型的一种流行形式，它本身是一种普通的 RL 算法。如果你了解 A2C，你就了解深层的 RL。

【2020 年 1 月 31 日更新】对应代码[此处](https://github.com/rgilman33/simple-A2C)。关于 RL 的更多细节，请查看 OpenAI 的[旋转起来](https://spinningup.openai.com/en/latest/)。

【2021 年 4 月 19 日更新】下载高分辨率文件，在 rudygilman.com[的](http://rudygilman.com)查看更多 ML 内容

*插图由*[*@ ember marke*](https://twitter.com/embermarke)

![](img/33584f0c4595c73f49867c6cf4202685.png)