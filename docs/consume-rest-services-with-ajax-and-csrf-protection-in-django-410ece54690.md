# 在 Django 使用 AJAX 和 CSRF 保护来消费 REST 服务

> 原文：<https://medium.com/hackernoon/consume-rest-services-with-ajax-and-csrf-protection-in-django-410ece54690>

*最初发表于*[*melvinkoh . me*](http://www.melvinkoh.me/#/post/b6f545a3-5ad5-4c5e-96aa-751cff328559)*。*

# 我现在有什么？

1.  提供了架构的 API 端点
2.  它们需要令牌认证

# 我打算做什么？

创建一个表单来与这些端点交互，并在调用 REST 服务之前执行验证和预处理。

# 动机:

1.  为 REST 服务提供直观的交互 UI。