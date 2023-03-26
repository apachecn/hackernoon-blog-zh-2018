# 使用 TypeScript 建模聊天 API

> 原文：<https://medium.com/hackernoon/modelling-a-chat-api-using-typescript-56d36be86b10>

![](img/21144e54071c46fcd4684c1d559152cb.png)

“Hands create art on a pottery wheel” by [SwapnIl Dwivedi](https://unsplash.com/@momentance?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

清晰、干净和简洁，是我会将 API 与愉快和富有成效的[开发](https://hackernoon.com/tagged/development)体验联系在一起的一些形容词。

TypeScript 提供了一个很好的特性——区分联合——可以用来构建这样的 API。

> 区别联合，也称为标记联合，代表一个有限的、定义明确的选择集。

## [*来源*](https://en.wikibooks.org/wiki/F_Sharp_Programming/Discriminated_Unions)

## 在 TypesScript 中实现有区别的联合

> 如果您有一个带有 [*文字成员*](https://github.com/basarat/typescript-book/blob/master/docs/types/literal-types.md) 的类，那么您可以使用该属性来区分联合成员。
> 
> 作为一个例子，考虑一个`Square`和`Rectangle`的联合，这里我们有一个成员`kind`，它存在于两个联合成员上，并且是一个特殊的*文字类型*:

[*来源*](https://github.com/basarat/typescript-book/blob/master/docs/types/discriminated-unions.md)

## 设计 API

聊天 API 将接收来自聊天用户的请求，并返回适当的响应。

聊天用户将发出以下请求:

*   注册
*   注销
*   发送消息
*   请求通知(聊天室事件的通知请求)

服务器将依次响应以下通知之一:

*   LoggedOnUsers(可以聊天的用户列表，在登录时提供)
*   用户已加入(通知新用户已加入聊天)
*   UserLeft(通知用户已离开聊天)
*   消息来自
*   什么都没有(当用户注销时，没有什么可说的)

TypeScript 中请求模型的定义如下

…对于回应也是如此

因此，在几行代码中，我描述了聊天 API 需要处理的所有情况。

然后，我可以根据需要使用 switch 语句来处理每种情况。

开关中的 default 子句确保我们已经处理了所有可能的请求类型。

如果我在将来扩展 API，编译器会确保我已经处理了新的情况。这是一个很棒的未来防臭虫的方法。

感谢您的阅读，如果您觉得这篇文章有帮助，请点击👏按钮并共享文章，以便其他人可以轻松找到它。干杯。