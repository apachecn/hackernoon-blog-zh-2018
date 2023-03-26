# Flog:一个通用日志格式的假日志生成器

> 原文：<https://medium.com/hackernoon/flog-a-fake-log-generator-for-common-log-formats-f7bd41617bf6>

我做了另一个命令行工具，flog。flog 是一个伪日志生成器，用于常见的日志格式，如 apache-common、apache error 和 RFC3164 syslog: [flog](https://github.com/mingrammer/flog)

它对于测试一些需要日志数据的任务很有用，比如 amazon kinesis 日志流测试。

Flog 目前支持以下特性:

*   完全随机日志生成器
*   支持`apache common`、`apache combined`、`apache error`和`rfc3164`日志格式
*   支持标准输出，文件和 gzip 类型的日志记录

我有一个支持下一版本附加功能的计划。

*   上下文随机日志生成器
*   统计随机日志生成器
*   支持其他一些系统日志格式
*   支持自定义日志格式(最重要的特性，它将被添加到 flog 版本 1.0.0 中)

我希望这可以帮助那些想测试需要日志数据东西的人。

谢了。