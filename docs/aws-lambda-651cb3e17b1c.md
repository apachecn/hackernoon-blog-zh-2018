# AWS Lambda 带 AWS 圣杯

> 原文：<https://medium.com/hackernoon/aws-lambda-651cb3e17b1c>

使用 Python3 处理 AWS Lambda 时的一些注意事项

**Limit:** [https://docs . AWS . Amazon . com/lambda/latest/DG/limits . html # limits-list](https://docs.aws.amazon.com/lambda/latest/dg/limits.html#limits-list)

**由 AWS 提供的无服务器框架:** [https://github.com/aws/chalic](https://github.com/aws/chalice)e
[https://chalice.readthedocs.io/en/latest/](https://chalice.readthedocs.io/en/latest/)

**构建扩展模块**
[https://docs.python.org/2/extending/extending.html](https://docs.python.org/2/extending/extending.html)

有些模块会与亚马逊 Linux 不兼容，需要为亚马逊 Linux 重新构建:
-使用构建实例:AWS EC2
-使用 docker:亚马逊 Linux 容器镜像:[https://docs . AWS . Amazon . com/AmazonECR/latest/user guide/Amazon _ Linux _ Container _ Image . html](https://docs.aws.amazon.com/AmazonECR/latest/userguide/amazon_linux_container_image.html)-使用 AWS Cloud9

那些扩展模块需要在 Chalice 项目的 **/vendor** 中:
[http://Chalice . readthe docs . io/en/latest/topics/packaging . html # rd-party-packages](http://chalice.readthedocs.io/en/latest/topics/packaging.html#rd-party-packages)