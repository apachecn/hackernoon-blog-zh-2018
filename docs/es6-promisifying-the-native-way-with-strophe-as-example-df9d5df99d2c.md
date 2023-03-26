# ES6:允诺本土方式(以 Strophe 为例)

> 原文：<https://medium.com/hackernoon/es6-promisifying-the-native-way-with-strophe-as-example-df9d5df99d2c>

*原载于*[*www . melvinkoh . me*](http://www.melvinkoh.me/#/post/81365dcc-b97f-43dd-9804-06d526ebcc8f)*。*

在 ES6 为我们提供 Promise 的原生支持之前，我们依赖第三方库，如 [Bluebird](http://bluebirdjs.com/docs/getting-started.html) 来启用这个有用的机制。在这篇文章中，我们将看到如何以 ES6 原生方式保证基于回调的 API，并避免常见的反模式。

# 什么是承诺？

Promisify 是将传统的基于回调的 API 重构为基于承诺的 API。

# 为什么要承诺？