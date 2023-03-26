# 班扬简单 JSON 日志库

> 原文：<https://medium.com/hackernoon/bunyan-simple-json-logging-library-1bb1d856c8e5>

想知道如何删除你的 nodeJS 代码中的 console.logs？我们将使用测井仪。bunyan logger 可以简单地以 JSON 格式将消息记录到不同的流中。

# 装置

```
npm install bunyan
```

# 创建日志文件

安装班扬后创建一个文件。在我的例子中，我创建了一个名为 *logger.js* 的文件。首先包括班扬图书馆。

```
**//logger.js
var** bunyan = require('bunyan');
```

然后使用 createLogger 方法创建记录器。

```
var log = bunyan.createLogger({
    name: 'myapp',
    stream: process.stdout,
    level: 'info'
});log.info("This is logging");
```

如果您运行这个文件，您将在控制台中看到类似这样的输出。

```
{“name”:”myapp”,”hostname”:”ChamaraL”,”pid”:28027,”level”:30,”msg”:”This is logging”,”time”:”2018–03–29T15:15:33.715Z”,”v”:0}
```

班扬日志记录是 JSON。一些字段会自动添加:“pid”、“主机名”、“时间”和“v”。

在 bunyan 日志库中定义了几个日志级别。

*   *致命”(60):服务/应用将停止或变得不可用。运营商应该尽快调查此事。*
*   *“错误”(50):对于特定的请求是致命的，但是服务/应用程序继续为其他请求服务。操作员应该很快就会看到这一点。*
*   *“警告”(40):关于操作员最终可能会看到的东西的注释。*
*   *“信息”(30):常规操作的详细信息。*
*   *“debug”(20):其他任何内容，即过于冗长而无法包含在“info”级别中。*
*   *“trace”(10):从你的应用使用的外部库进行日志记录或者非常详细的应用日志记录。*

参赛:https://github.com/trentm/node-bunyan#levels

您可以根据需要指定日志级别。

在 bunyan 中有一个很酷的特性叫做 Streams，它允许我们使用不同的流来记录我们的输出。假设我想将所有错误记录到一个外部日志文件和信息级日志(标准输出的操作细节),那么我们可以如下使用流。

```
**var** bunyan = require('bunyan');
var log = bunyan.createLogger({
    name: "bunyan-log",
    streams: [
        {
            level: 'debug',
            stream: process.stdout // log INFO and above to stdout
        },
        {
            level: 'error',
            path: __dirname + '/logs/appError.log' // log ERROR and above to a file
        }
    ]
});

module.exports = log;
```

如上所述，您可以在列表中定义多个流。当日志级别为 error 时，我将它保存到一个名为 appError.log 的文件中。如果级别为 debug，我将它输出到标准输出中。

我是如何在代码中使用日志记录器的

```
**let** mongoose = require('mongoose');
**let** config = require('./config');
**let** logger = require('./../../utils/logger'); //path to my logger 

**class** Database {

    constructor() {
        **this**._connect();
    }

    _connect() {
        mongoose.connect(`mongodb://${config.server}/${config.database}`)
            .then(() => {
                logger.debug('Database connection successful'); //logs the info
            })
            .catch(err => {
                logger.error('Database connection error' + err); //logs the error
            });
    }
}

module.exports = **new** Database();
```

这种方式比在代码中到处写 console.log()要好，这不是一种好的做法。

通过这样的配置，您将在日志文件中获得所有错误，而其他常规输出以 JSON 格式显示在控制台中。

如果您需要更多细节和更多功能，请参考[https://github.com/trentm/node-bunyan](https://github.com/trentm/node-bunyan)