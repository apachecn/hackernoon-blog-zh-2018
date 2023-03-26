# 使用高阶函数的策略模式

> 原文：<https://medium.com/hackernoon/the-command-pattern-using-higher-order-functions-e482fe322460>

![](img/4c988db7d7d2e48d741caaaba9afd4d6.png)

> 如果您的团队习惯于函数式编程，那么要知道像 Strategy 模式这样的设计模式也可以以函数的方式使用。

策略模式通常使用类来实现。然而，它可以很容易地在函数模式中实现。

通常，具有相同接口的所有策略对象都可以根据需要轻松交换。这是战略模式的主要好处之一。

例如，如果您想要发送一封电子邮件，但是想要实现一种在通过 Sparkpost 还是通过 AWS 发送电子邮件之间进行切换的方法，您可以执行如下操作:

```
class EmailStrategy {
  constructor() {}
  execute(sender, receiver, subject, body) {
    throw new Error('Not implemented');
  }
}class SparkPostEmailStrategy extends EmailStrategy {
  constructor(apiKey, SparkPost) {
    this.sparkpost = new SparkPost(apiKey);
  }
  execute(sender, receiver, subject, body) {
    return this.sparkpost.transmissions.send({
      content: {
        from: sender,
        subject,
        html: body,
      },
      recipients: [
        {address: sender}
      ]
    });
  }
}class AWSEmailCommand extends IEmailCommand {
  constructor(AWS) {
    this.ses = new AWS.SES();
  }
  execute(sender, receiver, subject, body) {
    return new Promise((resolve, reject) => {
      this.ses.sendEmail({
        Destination: {
          ToAddresses: [receiver]
        }, 
        Message: {
          Body: {
            Html: {
              Data: body,
            },
          }, 
          Subject: {
            Data: subject,
          }
        }, 
        Source: sender,
      }, (err, data) => {
        if (err) reject(err);
        resolve(data);
      });
    });
  }
}
```

现在我们可以互换使用`SparkPostEmailCommand`和`AWSEmailCommand`:

```
const command = new SparkPostEmailCommand(myApiKey, SparkPost);
command.execute('us@email.com', 'them@email.com', title, body)
  .then((data) => console.log('success'))
  .catch((err) => console.error('failure', err));
```

但是大多数命令对象只有两个方法:一个构造函数和一个执行方法(有时还有一个撤销方法)。感觉就像当我们调用构造函数时，我们只是延迟了`execute`方法的执行，直到我们希望它执行它的副作用。

# 使用高阶函数

对我来说，这是一个使用高阶函数来简化上面的代码并消除对类的需求以支持简单函数的大好时机。

因为大多数时候，我们关心的只是建立一些依赖关系，然后延迟执行，所以我们可以将命令类写成函数:

```
const sparkPostEmailCommand = (apiKey, SparkPost) => {
  const sparkpost = new SparkPost(apiKey);
  return (sender, receiver, subject, body) => {
    return sparkpost.transmissions.send({
      // Same as above
    });
  };
};const awsEmailCommand = (AWS) => {
  const ses = new AWS.SES();
  return (sender, receiver, subject, body) => {
    return new Promise((resolve, reject) => {
      // Same as above
    });
  };
};
```

现在，我们不用创建新对象，只需将命令用作函数:

```
sparkPostEmailCommand(myApiKey, SparkPost)('us@email.com', 'them@email.com', title, body)
  .then((data) => console.log('success'))
  .catch((err) => console.error('failure', err));
```

或者我们可以推迟执行:

```
const execute = sparkPostEmailCommand(myApiKey);
// do some work
execute('us@email.com', 'them@email.com', title, body);
```

# 为什么要使用高阶函数？

虽然其他语言中的类会给你一些类型提示和安全性，但在 JavaScript 中却不是这样。不幸的是，你不可能真的知道你的`execute`方法的所有签名看起来都一样。

如果没有类型，就没有必要添加所有伴随类实现而来的臃肿。为什么担心`this`的约束力？还是那个`extends BaseCommand`？

使用这些设计模式的意义不在于它们需要面向对象。而是它们帮助你使用通用模式组织你的代码。构建一个具有依赖关系的对象，然后做一些事情是一种常见的模式，其中“做一些事情”可能以不同的方式完成。

如果你的团队习惯了类和对象，那么就使用类和对象。

如果你的团队习惯于功能性的[编程](https://hackernoon.com/tagged/programming)，那么要知道像命令模式这样的设计模式也可以以功能性的方式使用。

> Ivan Montiel 是 Clarity Hub 的创始人兼首席执行官，该公司与 Intercom 集成，在帮助客户时为客户成功团队提供实时建议。
> 
> 你可以在[推特](https://twitter.com/idmontie)上关注他。

.