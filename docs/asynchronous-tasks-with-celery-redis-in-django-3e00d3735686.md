# Django 中 Celery + Redis 的异步任务

> 原文：<https://medium.com/hackernoon/asynchronous-tasks-with-celery-redis-in-django-3e00d3735686>

*最初发表于* [*melvinkoh.me*](http://www.melvinkoh.me/#/post/cdaf221c-8bbb-49ba-941f-a4e0966183d4)

假设你的任务需要 20 秒钟才能完成，然后才能回复你的客户。假设你是你的应用程序的用户。事实上，当用户向您的站点发出请求时，大量的处理可以被节省下来，直到以后。AJAX 是异步执行任务的一种方式，但是我们希望异步运行 Python 代码。因此，我们求助于使用任务队列和消息代理。