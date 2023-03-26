# 再平衡与 HODL:技术分析

> 原文：<https://medium.com/hackernoon/rebalance-vs-hodl-a-technical-analysis-6f341b0db9cd>

![](img/c266c8a7481f180cb461b0210b57cd1d.png)

这项研究的目的是描绘一幅公平的画面，说明再平衡作为一种策略如何符合霍德林。为了做到这一点，我们仔细考虑了如何设计回溯测试、数据和变量。

## 贸易和数据

从交易所收集了一整年的市场数据。这些数据被用来评估每笔交易在执行再平衡时的成本。此外，所有交易都包括 0.25%的费用，这是大多数交易所的标准。所以，从 LTC 到 SNT 的交易会从 LTC 到 BTC，然后从 BTC 到 SNT。在这种情况下，两笔交易都产生了 0.25%的费用。这使我们能够创建最准确的模型来重新平衡性能。

## 再平衡期

这项研究需要的第一个变量是再平衡期。重新平衡期是指每次重新平衡之间的具体时间。因此，1 天的时间会导致每天在完全相同的时间进行重新平衡。改变该值的目的是确定再平衡的频率是否会影响投资组合的表现。在本研究中，我们选择了 1 小时、1 天、1 周和 1 个月的重新平衡周期。 [**了解有关加密货币再平衡的更多信息。**](https://blog.shrimpy.io/blog/portfolio-rebalancing-for-cryptocurrency)

## 投资组合规模

我们决定在这项研究中调查的第二个变量是投资组合中的资产数量。假设是投资组合中的资产数量对业绩有很大的影响。这一假设用 5 组资产规模进行了检验。由于在比较再平衡和 HODLing 时，2 是产生任何差异的最小资产数量，因此我们从 2 资产投资组合开始。然后，我们增加 2 以获得 2、4、6、8 和 10 作为每个投资组合组中的资产数量。 [**了解投资组合中的资产数量如何影响业绩。**](https://blog.shrimpy.io/blog/crypto-users-who-diversify-perform-better)

## 资产选择

为了确定在构建投资组合的过程中需要考虑哪些资产，我们使用了 Bittrex 和 Poloniex 的横截面。这意味着我们从 Poloniex 中提取了所有拥有 1 年数据的资产，并将它们与拥有 1 年数据的 Bittrex 资产列表进行了比较。两个列表中的任何资产都包含在我们的选择过程中。当构建投资组合时，从池中随机选择资产来创建投资组合。

虽然我们的研究随机选择资产，但我们强烈反对将此作为创建投资组合的策略。 [**了解如何成功构建强大的投资组合。**](https://blog.shrimpy.io/blog/10-tips-for-creating-a-killer-cryptocurrency-portfolio)

## 回溯测试

回溯测试是使用来自交易所的交易数据来模拟一个策略在给定时间内的表现的过程。这通常用于通过在这些大型数据集中运行来测试策略的可行性。在这项研究中，我们使用回溯测试来比较再平衡和 HODL 的结果。我们为每个投资组合规模和再平衡期对运行的回溯测试的数量被设置为 1000。这被确定为足够大以产生明显的趋势。 [**阅读更多关于回溯测试的内容或自己运行。**](https://blog.shrimpy.io/blog/the-crypto-portfolio-rebalancing-backtest-tool)

现在我们知道了研究是如何设置的，让我们来看一下整个过程。首先，重新平衡周期设置为 1 小时，资产数量设置为 2。这意味着投资组合将包含 2 项资产，每小时重新平衡 1 次。接下来，从资产池中随机选择 2 项资产。如果没有重复项，就运行回溯测试。一旦完成，软件随机选择 2 个新的资产，并运行另一个回溯测试。这一过程一直持续到成功运行 1000 次回溯测试。完成后，资产的数量从 2 个增加到 4 个，并运行了 1000 多个回溯测试。这个过程一直持续到资产数量和再平衡周期的每个组合都经过回溯测试。

# 表演

## 2 资产组合

![](img/a84a745c41f7323414eb367ed0ff3b68.png)

This group compares the performance of portfolios which contain two assets, but differ by rebalance period. This performance varies from 1 hour (top left chart) to 1 month (bottom right chart). Each histogram incorporates 1,000 backtests, where the x-axis is the percent gain better than HODL. The y-axis is the number of backtests which fell into the performance buckets that are defined on the x-axis. (Example: A backtest was run with a rebalance period of 1 hour and 2 assets in the portfolio. The results of a backtest was a 50% increase over buy and hold. This would mean you add a 1 to the top left chart in the x-axis bucket which has the range of 44 and 67\. This process is then repeated until 1,000 backtests have been run.)

![](img/d47eac3553603a37a5186a43b69dbcf7.png)

This demonstrates the median percent for which rebalancing at varying intervals outperformed HODL for a portfolio which contains two assets.

双资产投资组合是投资组合中最简单的选择。在这种情况下，加密货币只是在每个再平衡期间相互之间来回交易。从这些直方图中我们可以看出，较短的重新平衡周期会导致较大的性能差异。在较短的再平衡周期中，异常值明显较少，结果始终较高。随着再平衡期的延长，息差实际上会减小。这导致结果中的差异较小，但对异常值的观察较高。这表明，周期越长，回报越低，但也会产生更多零星的异常值。**使用 1 小时再平衡期的投资组合表现优于买入并持有，最大差异为 93%。**

## 4 资产组合

![](img/5ac748b2de36effad36e780f211854de.png)

This group compares the performance of portfolios which contain four assets, but differ by rebalance period. This performance varies from 1 hour (top left chart) to 1 month (bottom right chart). Each histogram incorporates 1,000 backtests, where the x-axis is the percent gain over HODL. The y-axis is the number of backtests which fell into the performance buckets that are defined on the x-axis. (Example: A backtest was run with a rebalance period of 1 hour and 4 assets in the portfolio. The results of a backtest was a 50% increase over buy and hold. This would mean you add a 1 to the top left chart in the x-axis bucket which has the range of 32 and 66\. This process is then repeated until 1,000 backtests have been run.)

![](img/e61d038c07c946a6c72d85109014bfd3.png)

This demonstrates the median percent for which rebalancing at varying intervals outperformed HODL for a portfolio which contains four assets.

继续 2 种资产组合研究的趋势，我们看到较短的再平衡期在 4 种资产组合中也有较大的表现差异。与较长的再平衡周期相比，这导致了更少的异常值和显著更高的中值性能。还可以观察到，表现最好的投资组合都使用了 1 小时的再平衡期。包括所有异常值时也是如此。**一个小时的时间表现最好，比买入并持有**高出 177%。

## 6 资产组合

![](img/3e90275d012a26226544e1bfa850171d.png)

This group compares the performance of portfolios which contain six assets, but differ by rebalance period. This performance varies from 1 hour (top left chart) to 1 month (bottom right chart). Each histogram incorporates 1,000 backtests, where the x-axis is the percent gain over HODL. The y-axis is the number of backtests which fell into the performance buckets that are defined on the x-axis. (Example: A backtest was run with a rebalance period of 1 hour and 6 assets in the portfolio. The results of a backtest was a 50% increase over buy and hold. This would mean you add a 1 to the top left chart in the x-axis bucket which has the range of 22 and 55\. This process is then repeated until 1,000 backtests have been run.)

![](img/00b365e9de9dcde4f7203c138e26b88d.png)

This demonstrates the median percent for which rebalancing at varying intervals outperformed HODL for a portfolio which contains six assets.

我们从 6 个资产组合的结果中观察到，2 和 4 个资产组合中讨论的趋势仍在继续。这包括较短再平衡期的较大价差和较短再平衡期的较高平均绩效。此时，我们也可以开始得出结论，随着我们增加投资组合中的资产数量，1 小时再平衡期和 1 个月再平衡期之间的差距会越来越大。我们可以在继续研究时记住这一点。**一个包含 6 种资产、再平衡周期为 1 小时的投资组合，其表现超过 HODL 203%。**

## 8 资产组合

![](img/65cf5d4a462fd536ae5a356598e08daa.png)

This group compares the performance of portfolios which contain eight assets, but differ by rebalance period. This performance varies from 1 hour (top left chart) to 1 month (bottom right chart). Each histogram incorporates 1,000 backtests, where the x-axis is the percent gain over HODL. The y-axis is the number of backtests which fell into the performance buckets that are defined on the x-axis. (Example: A backtest was run with a rebalance period of 1 hour and 8 assets in the portfolio. The results of a backtest was a 50% increase over buy and hold. This would mean you add a 1 to the top left chart in the x-axis bucket which has the range of 50 and 80\. This process is then repeated until 1,000 backtests have been run.)

![](img/7024f0e144e8bc52776d3b3141d344dd.png)

This demonstrates the median percent for which rebalancing at varying intervals outperformed HODL for a portfolio which contains eight assets.

我们从 8 个资产组合的结果中观察到，2、4 和 6 个资产组合中讨论的趋势仍在继续。这包括较短再平衡期的较大价差和较短再平衡期的较高平均绩效。我们还可以看到，在这项对 8 个资产组合的研究中，只有一个直方图包含了比 HODL 表现更差的结果。这可以在右下角的图表中看到，该图表代表了使用 1 个月再平衡期的投资组合。**每 1 小时重新平衡一次的中值 8 资产组合比 HODL 高出 224%。**

## 10 资产组合

![](img/7dcbae77eedf9ac02e9b0f433b6a9dbc.png)

This group compares the performance of portfolios which contain ten assets, but differ by rebalance period. This performance varies from 1 hour (top left chart) to 1 month (bottom right chart). Each histogram incorporates 1,000 backtests, where the x-axis is the percent gain over HODL. The y-axis is the number of backtests which fell into the performance buckets that are defined on the x-axis. (Example: A backtest was run with a rebalance period of 1 hour and 10 assets in the portfolio. The results of a backtest was a 50% increase over buy and hold. This would mean you add a 1 to the top left chart in the x-axis bucket which has the range of 44 and 72\. This process is then repeated until 1,000 backtests have been run.)

![](img/11d93018c90a3816de750f34e4f9e175.png)

This demonstrates the median percent for which rebalancing at varying intervals outperformed HODL for a portfolio which contains ten assets.

我们从 10 个资产组合的结果中观察到，2、4、6 和 8 个资产组合中讨论的趋势仍在继续。这包括较短再平衡期的较大价差和较短再平衡期的较高平均绩效。我们还可以从这些结果中看到，如果每个月进行一次再平衡，4000 个投资组合中只有 10 个表现比 HODL 差。这意味着，如果你随机选择 10 种资产，每月至少重新平衡一次，那么在过去一年里，你有 99.75%的机会跑赢买入并持有。这真是难以置信。拥有 10 项资产、再平衡周期为 1 小时的投资组合的中值表现比 HODL 好 234%*。*

## *完全比较*

*现在我们有了所有的数据，我们可以将结果简化为一个 4 x 5 的网格，来说明每个投资组合和再平衡期的表现。由于大多数图上的上界远高于下界，我们将计算中值。这也意味着 50%的投资组合高于该值，50%的投资组合低于该值。因此，如果在创建你的投资组合时，你没有进行任何研究就随机选择了资产，你将有 50%的机会比列出的价值表现更好。*

*此外，列出的价值是买入和持有的百分比收益。因此，10%的值将意味着再平衡执行得比 HODL 好 10%*。**

**![](img/4e6ad712d6b5e43465c3aaaec24e279b.png)**

**The median performance demonstrates that the higher the rebalance period with the higher number of assets presents the highest gains for rebalancing. Each value represents a percent increase OVER buy and hold. That means a value of 18 means the median of that group performed 18 percent BETTER than buy and hold. This demonstrates, even the absolute worst case performs better than by and hold, even after considering taxes.**

**我们可以从这个网格中得出两个主要结论。首先，再平衡期和业绩之间有明显的相关性。随着再平衡期变短，投资组合的表现会提高。我们可以看到的第二个相关性是资产数量和绩效之间的关系。随着投资组合中资产数量的增加，业绩也会提高。因此，表现最好的投资组合是那些既有短期再平衡期又有大量资产的投资组合。**

**为了完成完整的比较，我们将结合每个回溯测试来创建一个总体比较。**

**![](img/8d334204aa815a302d9c14c0f8288c86.png)**

**Combining all of the backtests over all portfolios and rebalancing periods produces a complete picture comparing rebalancing and HODL. We observe a median complete performance of 64%. This means, if you were to randomly select a portfolio size between 2 and 10, randomly select a rebalance period between 1 hour and 1 month, and randomly select the assets in your portfolio, you would have a 50% chance of performing 64% better than buy and hold if the only difference was rebalancing.**

****结果显示，在所有投资组合规模、再平衡期和硬币选择上，中值业绩增长了 64%。****

# **税收影响(特定于美国)**

**根据最新消息，如果资产持有时间不到一年，加密交易将按照您当前收入等级的税率作为短期资本利得征税。当资产持有时间超过一年时，长期资本利得将被折价征税。由于围绕税收有很多误解，我将在这里尝试打破一些影响。所有计算都将基于 120，000 美元的个人收入。**

**在美国，年收入 12 万美元的个人属于收入最高的 10%人群。他们也在 24%的联邦所得税范围内。这意味着任何短期资本利得都将被征收 24%的税，这相当于个人所得税。同一个人挣 120，000 美元，将支付 15%的长期资本利得。**

**我们很快就能看出，长期和短期资本利得的税收相差 9%。我们可以将这一差异与通过再平衡观察到的 64%的回报率进行比较。我们看到的是，即使考虑到频繁交易的税收影响，再平衡的表现也明显优于 HODL。事实上，在去年重新平衡的所有投资组合中，92%在税后都超过了 HODL。**

**然而，这并不是故事的全部。再平衡在任何给定时间只交易投资组合的一部分。这意味着使用再平衡的投资组合的一部分在一年结束时不会被交易。这些未触及的部分可以作为长期资本利得征税，从而减少因再平衡而产生的总体税收。可以通过检查过去几年所有加密货币之间的波动性差异来量化该数量。这将让我们了解投资组合中有多少比例通常被视为长期资本收益。由于正确的模拟需要仔细的设计，我们将把这个分析留到另一篇文章中。**

# **结论**

**从这项研究中我们可以得出两个主要的关系。第一个关系是，增加资产数量会提高投资组合的表现。第二个关系是，缩短再平衡周期(增加再平衡频率)会提高投资组合的绩效。因此，理想的投资组合是频繁再平衡的，并且包含许多资产。**

**应该记住，所有这些投资组合都是完全随机选择的。在确定哪些资产应该纳入投资组合时，没有研究或排除过程。对于积极研究和选择有前途的资产的个人来说，有很大的改进潜力。**

> **再平衡以 64%的中间值击败了 HODL。税后，这代表了所有可能的加密货币投资组合的 92%。**

# **用 Shrimpy 重新平衡**

**既然我们已经确定再平衡客观上比霍德林更好，我们需要一种方法来利用这一知识。这就是 [Shrimpy 应用程序](https://www.shrimpy.io/)可以提供帮助的地方。Shrimpy 是一项完全免费的服务，可以自动重新平衡您的投资组合。然而，这并不是它的全部功能。这是管理投资组合最简单的方法。快速选择资产，即时分配投资组合，并随时监控您的投资。这是投资组合管理的少压力，多收益的解决方案。**

**点击[这里](https://www.shrimpy.io/)，立即报名。**

**如果您仍然不确定，请尝试演示，看看我们提供的一切！**

**[](https://www.shrimpy.io/demo) [## Shrimpy 演示

### Shrimpy 是管理您的加密组合的最简单的方法。试试我们的演示，看看我们能提供什么！

www.shrimpy.io/demo](https://www.shrimpy.io/demo) 

# 附加阅读

[***多样化的加密用户表现更好【新研究】***](https://blog.shrimpy.io/blog/crypto-users-who-diversify-perform-better)

[***投资组合再平衡为加密货币***](https://blog.shrimpy.io/blog/portfolio-rebalancing-for-cryptocurrency)

[***加密组合再平衡回溯测试工具***](https://blog.shrimpy.io/blog/the-crypto-portfolio-rebalancing-backtest-tool)

[***加密中的投资组合再平衡白皮书***](https://hackernoon.com/the-whitepaper-for-portfolio-rebalancing-in-crypto-ea6af634d35f)

[***加密中常见的再平衡场景***](https://blog.shrimpy.io/blog/common-rebalance-scenarios-in-crypto)

[***创建黑仔加密货币投资组合的 10 个技巧***](https://blog.shrimpy.io/blog/10-tips-for-creating-a-killer-cryptocurrency-portfolio)

[***如何用这 24 种加密货币红旗避免骗局***](https://blog.shrimpy.io/blog/how-to-avoid-scams-with-these-24-cryptocurrency-red-flags)

别忘了查看一下 [Shrimpy 网站](https://www.shrimpy.io/)，在 [Twitter](https://twitter.com/ShrimpyApp) 和[脸书](https://www.facebook.com/ShrimpyApp)上关注我们的更新，并在[Telegram](https://t.me/ShrimpyGroup)&[Discord](https://discord.gg/gXyy95y)上向我们令人惊叹的、活跃的社区提问。

留下你的评论，让我们知道你的平衡经验！

*捕虾队***