# 在 Django 中使用 Enum 作为模型字段选择

> 原文：<https://medium.com/hackernoon/using-enum-as-model-field-choice-in-django-92d8b97aaa63>

*最初发表于*[*melvinkoh . me*](http://www.melvinkoh.me/#/post/1b66029e-cd20-4e11-9084-9a20e6c90bdb)*。*

在 Django [官方文档](https://docs.djangoproject.com/en/1.11/ref/models/fields/#choices)中，他们提供了 2 个例子，说明如何使用预定义选项作为特定字段的选择。

# Django 官方文件中的例子

第一个示例将选择定义为一个元组，每个选项都是外部元组的值。

每个内部元组中的第一个元素是要在模型中设置的值，第二个元素是它的字符串表示。