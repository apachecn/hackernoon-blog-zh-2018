# 在 Django 中创建归档时减少内存占用

> 原文：<https://medium.com/hackernoon/reducing-memory-footprint-while-creating-archive-in-django-3aca58e4b66c>

Python 内置的 zip 库常用来创建归档文件。但是，使用内置库创建 zip 文件时有一个问题。考虑这样一种情况，我们压缩的文件比我们可用的内存大，我们很容易耗尽内存。

我正在建立一个功能，需要压缩文件并上传到我们的 Django 后端存储。在搜索了互联网之后，我总结了我用来支持这个特性的逻辑。

# 使用 NamedTemporaryFile 代替内存