# 技术领导的技能:分布式事务

> 原文：<https://medium.com/hackernoon/skills-for-technical-leadership-on-distributed-transactions-ab0b413e5d2b>

这是正在进行的系列的一部分。查看我的个人资料了解更多信息。

已经过去几个月了，但是我又回来了，准备讨论跨分布式系统的状态协调，特别是关于分布式事务。许多业务需要[事务性](https://hackernoon.com/tagged/transactional)操作，即由许多部分组成的操作，这些部分作为一个原子单元应该成功或失败。当我们进入 SOA 的世界时，事情会变得更加复杂，正如我们将在这篇文章中看到的。我们将从查看非事务性操作开始，然后转到事务性操作，以了解这两种场景引起的问题的差异。

正如在上一篇文章中看到的，解决跨分布式系统操作的部分失败可能是复杂的。上次，我们看了一个分布式博客服务:

```
def publish_blog_post(post_id)
  PostDB.execute(“UPDATE posts WHERE id = ? SET published = ‘t’”, post_id)
  NotificationService.notify_friends(post_id)
  FeedUpdatingService.update_feeds(post_id)
  TwitterService.tweet_about_post(post_id)
end
```

正如上一篇文章中所讨论的，像 notification service . notify _ friends 这样的调用可能会失败或超时，让我们处于一种不确定的状态。之前我们讨论了等幂作为解决这类问题的策略:如果我们重试一次或两次，请求可能最终会成功，我们不需要担心重复执行任何操作的问题。通过使每个操作——包括 publish_blog_post 本身——幂等，我们能够极大地降低复杂性。

在这个例子中，所有那些下游[服务](https://hackernoon.com/tagged/service)调用之间没有真正的相互依赖；每个服务请求的成功或失败不应影响其他请求。更准确地说，publish_blog_post 操作看起来是非事务性的；如果一个服务调用失败，我们可能不希望回滚任何其他操作。有时我们没有那么幸运，发现自己处于需要事务性操作的情况。

让我们以一个买卖门票的电子商务应用程序为例。一个用户可以把票卖给另一个用户。买主想用我们给他的凭单付款。当这种情况发生时，应用程序应该将票“重新分配”给买方，用完买方的凭证，并记入卖方的账户。在这个例子中，我们的公司选择将我们的服务分成三部分:管理用户银行账户支出的账户服务，管理促销和优惠券的促销服务，以及协调门票销售和所有权的票务服务。以下是在票务服务中销售机票的一些示例代码:

```
def sell_ticket(buyer_id, seller_id, ticket_id)
  ticket = TicketDB.execute(“SELECT * FROM tickets WHERE id = ?”, ticket_id)
  TicketDB.execute(“UPDATE tickets SET user_id = ? WHERE id = ?”, buyer_id, ticket_id)
  PromoService.debit(buyer_id, ticket.amount)
  AccountService.credit(seller_id, ticket.amount)
end
```

这种操作显然是交易性的:我们不应该让买方在没有得到他的票的情况下被收费，我们也不应该让卖方在没有得到销售积分的情况下交易掉他的票。请记住，这些服务中的任何一个都可能在任何时间点发生故障，我们必须考虑各种各样的故障场景。如果“信用”电话开始中断会发生什么？我们可以恢复和撤销重新分配机票和借记买方的调用吗？如果借记呼叫超时了怎么办？我们有能力重试并继续前进吗？如果服务重复超时，我们可以将票重新分配给卖家吗？如您所见，这里有很多问题，用于解决这些问题的代码的复杂性可能会大大增加。幂等性在我们的博客应用程序中可能是有用的，但是仅仅像我们在博客平台中所做的那样令人厌烦地重试可能是不够的。如果系统因为足够多的请求而失败，我们可能希望“回滚”其他操作。

在使用传统 RDBMS 的单片应用程序中，这是使用数据库事务的一个极好的例子，如下所示:

```
BEGIN;
UPDATE tickets SET user_id = <buyer_id> WHERE id = <ticket_id>;
UPDATE voucher_balances SET balance = balance — <amount> WHERE user_id = <buyer_id>;
UPDATE accounts SET balance = balance + <amount> WHERE user_id = <seller_id>;
COMMIT;
```

撇开并发性问题不谈，这个例子的事务属性让我们得到了我们想要的行为:除非每个命令都成功，否则什么都不会成功。SQL 的语义允许这种行为:从 BEGIN…COMMIT 消息开始，数据库就知道这些命令将作为一个单元组合在一起。

如果我们可以简化 lift 这个想法，并将其用于我们的分布式票务平台，那就太好了，但事情会变得复杂得多。首先，他们现在*至少有*四个角色(票务应用、其数据库、凭证服务、账户服务)，可能更多(因为其他服务有它们自己的数据存储和可能的下游依赖)。在 monolith 系统中，只有两个(monolith 和数据库)。管理所有这些系统的一致性变得越来越复杂，因为您必须考虑如果这些系统中的任何一个单独(或一个接一个)出现故障会发生什么。我见过一些代码试图在这些场景中捕捉并“撤销”错误，以便在失败发生时回滚。下面是一个简化的代码示例，我们试图在 AccountingService 中防止失败:

```
TicketDB.execute(“UPDATE tickets SET user_id = ? WHERE id = ?”, buyer_id, ticket_id)
PromoService.debit(buyer_id, ticket.amount)
begin
  AccountingService.credit(seller_id, ticket.amount)
rescue Exception => e
  PromoService.credit(buyer_id, ticket.amount)
  TicketDB.execute(“UPDATE tickets SET user_id = ? WHERE id = ?”, seller_id, ticket_id)
end
```

这当然有其缺陷。最重要的是，重新贷记购买者的促销或将票分配给用户的调用可能会失败。代码的复杂性增加了。我们甚至还没有解决超时或重试的问题！沿着这条路走下去，我们可能会以难以管理的代码告终，这些代码容易出现边缘情况*和*难以遵循。

有些人可能试图用分布式提交算法(如两阶段提交、三阶段提交或其他算法)来改善这个问题。请注意，这些算法不提供 RDBMS 风格的回滚，也不保证事务的真实性，而只是协调跨服务提交事务的决策(即“我们是否都同意提交该事务？”).这可能会使避免复杂性变得稍微容易一些，但是如果事务出错，您可能仍然需要编写代码来撤销每个操作。此外，请注意，实现这些算法通常并不简单。

与其痛苦，不如尝试将分布式事务的可能性降到最低。如果您发现有许多原子操作跨服务被破坏，您可能需要重新评估您的服务边界。这样做可能会降低出错的风险，并使您的代码更容易理解。有时，您确实需要支持分布式事务操作，但不幸的是，这里没有灵丹妙药。您必须实现一些代码来管理操作并强制每个依赖系统处理它的部分，并且能够在需要时尽可能优雅地回滚。

这一次就讲到这里——分布式系统还会有更多内容，所以请耐心等待。