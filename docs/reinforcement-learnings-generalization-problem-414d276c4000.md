# 深度强化学习的泛化问题

> 原文：<https://medium.com/hackernoon/reinforcement-learnings-generalization-problem-414d276c4000>

Google Deepmind & OpenAI 最近强调的一个问题。

*本文研究深度强化学习代理的泛化能力——**一种最近被 Google Deepmind & OpenAI 质疑的能力。*

# 死记硬背与一般化

**我们人类有时依赖死记硬背。**有没有参加过一个事后证明死记硬背的考试？应试者可能在没有真正理解材料的情况下鹦鹉学舌，取得了优异的成绩——从长远来看，这会损害他们概括概念以解决新问题的能力。

**本文演示了这种现象的机器等价物。**代理将通过死记硬背(相对于理解)来处理环境，导致以后难以推广到新环境。

# 概括测试

最近，谷歌 Deepmind & OpenAI 发布了旨在衡量代理归纳能力的环境——即使对现代深度强化学习来说，这也是一个基本挑战。

一般化的需求无处不在**——例如，当一个代理在模拟器中接受训练，但随后被部署到现实世界时(这种差异也被称为现实差距)。**

然而，今天常见的基准测试使用相同的环境进行训练和测试— **这种实践对代理的概括能力提供的洞察相对较少**。

谷歌 Deepmind & OpenAI 发布了三个环境，试图阐明或测量智能体的概括能力:“*分布转移*”、“*共润*”、&“*刺猬索尼克*”。

![](img/93ec4a76fddcac2b0418e7d382d6ac8a.png)

Distributional shift environment by GoogleDeepmind. [Source](https://deepmind.com/blog/specifying-ai-safety-problems)

![](img/4549ac034bd6799aa2ef2b5306179c97.png)

CoinRun environment by the authors of “*Quantifying Generalization in Reinforcement Learning”*. [Source](https://blog.openai.com/quantifying-generalization-in-reinforcement-learning)

![](img/d887dd57f8fa44cee9911beb5774fe22.png)

Sonic the Hedgehog environment by OpenAI. Paper- “Gotta Learn Fast: A New Benchmark for Generalization in RL”. [Source](https://blog.openai.com/retro-contest)

观察 RL 代理在新游戏关卡(包含以前遇到的位置不同的对象的关卡)上的奇怪行为，可能会让人怀疑现代深度强化学习还不能创造出“真正”理解环境的代理。(这不是说近期不会有进展。)

一些看似表现出色的代理人会不会仅仅是重复他们在训练中获得奖励的动作序列？

我尝试用分布转移实验来研究这个假设。

# 谷歌 Deepmind 的分布式转变实验

![](img/5825dd50cb48e78dd846c3682912e4be.png)

The distributional shift experiment set-up by AI Safety Gridworlds authors. Agent start position ‘A’. Lava ‘L’. Goal ‘G’. Wall ‘#’.

分布移位实验(见上图和开头的 gif)**调查了一个代理适应新环境**的能力，这些新环境包含来自不同位置的训练环境的对象(在本例中为 lava-，goal-&wall-tiles)——本质上是一个一般化的**测试。**

重要的是要注意，为了这个实验的缘故，代理人不应该在这个熔岩格子世界的许多不同变化上被训练。如果是这样的话，那么测试环境本质上将是“在训练有素的代理的分布的流形上”,因此不需要非常强的泛化能力来适应测试环境。

本文的剩余部分讨论了使用现代深度强化学习代理运行该实验所获得的结果。

# 培训和测试

一个深度强化学习代理——一个 A2C(优势行动者-批评家)模型——在带有 PPO(近似策略优化)的训练环境中被训练。

**训练**
训练 100 万帧，归一化奖励:熔岩-1 奖励，移动-0.02 奖励，进球+1 奖励。
PPO 参数(*与代码*并排读会更有意义):0.99 折扣因子，7e-4 学习率，0.95 gae-lambda，0.01 熵系数，0.5 价值损失系数，0.5 梯度范数剪辑，0.2 剪辑 epsilon，4 个历元，256 批量。RMSprop 参数:1e-5 alpha，0.99 alpha。
输入格式:board (h x w x 1)。
并行化:16 个进程，每个进程 128 帧。
简单的 A2C 模型。没有递归参数，因为模型没有 RNN 模块——保持简单。

*代理可用* [*此处*](https://github.com/davidleejy/ai-safety-gridworlds) *。*

**测试**

**100 集在训练环境上的结果:**
每集总回报:平均 42.00 标准差 0.00 分钟 42.00 最大 42.00
每集帧数:平均 8.0 标准差 0.0 分钟 8.0 最大 8.0
**观察:**
**所有 100 集的完美表现**—达到可获得的最大奖励 42(走 8 步，侧跨熔岩，进入球门)0 次发作之间的差异。此代理的训练以策略分布的非零平均熵结束。

**测试环境 100 集的结果:**
每集总回报:平均-30.81 标准差 29.79 分钟-100.00 最大 41.00
每集帧数:平均 29.3 标准差 31.4 分钟 2.0 最大 100.0
观察:
平均总回报-30.8 是**与在列车环境上的评估相比明显较差的性能**。每集帧数的变化显著增加。最小 2 帧来自代理自杀的情况(采取 2 步触摸熔岩)。由于代理过度探索，最多出现 100 帧(剧集设置为在 100 帧后终止)。最大奖励 41 可能来自于运行更简单的“测试环境 v2”(但是这个环境的最大可获得奖励是 46)。10 次最糟糕的发作包括(i) 3 次四处游荡躲避熔岩，产生 R=-100，F=100，和(ii) 7 次自杀，产生 R=-52，F=2。

# 结论

尽管这个深度强化学习代理在训练期间表现出色，但还不能说它“理解”了 lava gridworld 环境，**这意味着它的泛化能力相对较差。**

当被放置在测试环境中时，代理人要么被吸引到**自杀** *—* 向右移动 2 格(-2 奖励)，然后跳入熔岩(-50 奖励)，以获得总共-52 奖励，要么被吸引到**过度探索** —在一集的最大允许长度(100 帧)内四处游荡以避免熔岩，以获得总共-100 奖励(-1 奖励* 100 步)。

# 更多细节

我的代码可用[这里](https://github.com/davidleejy/ai-safety-gridworlds)。欢迎投稿。

多亏了作者，这个分布转移实验在这里是。然而，他们的存储库不包含 RL 代理(截至 2018 年 11 月)，但将感兴趣的用户指向公共 RL 实现。

我使用的 A2C 建筑:

```
Model (no RNN modules):
(0): Conv2d(1, 16, kernel_size=(2, 2), stride=(1, 1))
(1): ReLU()
(2): MaxPool2d(kernel_size=(2, 2), stride=(2, 2), padding=0, dilation=1, ceil_mode=False)
(3): Conv2d(16, 32, kernel_size=(2, 2), stride=(1, 1))
(4): ReLU()
)
(actor): Sequential(
(0): Linear(in_features=192, out_features=16, bias=True)
(1): Tanh()
(2): Linear(in_features=16, out_features=4, bias=True)
)
(critic): Sequential(
(0): Linear(in_features=192, out_features=16, bias=True)
(1): Tanh()
(2): Linear(in_features=16, out_features=1, bias=True)
)
```

感谢朋友和同事们分享他们关于强化学习和自主代理的观点和经验。没有他们，我对强化学习的理解就不会那么丰富。