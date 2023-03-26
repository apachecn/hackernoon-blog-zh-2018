# 使用 aws 参数存储存储机密的一些技巧

> 原文：<https://medium.com/hackernoon/a-few-tips-for-storing-secrets-using-aws-parameter-store-f03557c5cf1b>

![](img/4bda77285ef3fc575d24a401ab38c0f7.png)

在我目前的工作中，我们有 50 多种服务，大约 6 个以上的环境，以及服务和人类在我们的环境中使用的大约 3.5k 个参数。我们曾经使用 Vault，但是在使用了一年之后，我们最终决定使用 Parameter Store，因为我们是在 AWS 上托管的。参数存储对我们来说是一个明显的赢家，因为它通过利用 IAM 策略和 KMS 密钥为我们提供了我们想要的所有安全性。而且…我不需要自己跑跳马。

如果您正在考虑迁移东西，或者已经开始使用 Param Store，这里有一些我使用 Param Store 管理秘密的经验。

# 使用 CLI 搜索参数

一个非常恼人的问题是，我不能轻松地在整个参数库中搜索参数。如果你尝试过使用 UI 来做这件事，那么在使用正则表达式和/或路径进行搜索之间会有些痛苦。例如，我不能搜索所有以“db_”开头的参数，即`*db_*`

一个有用的解决方法—对参数存储进行快速搜索*。这需要获取所有的参数——但是允许您在可用的路径上应用普通的正则表达式，这在 UI 中是做不到的。*它有点慢，但可以让你快速预览你可用的秘密

```
> aws ssm describe-parameters \
 --output text \
 | egrep '^PARAMETERS' \ 
 | awk '{print $5}' \
 | egrep $REGEX /dev/myApp/foo 
/dev/myApp/bar 
...
```

可能有更好的方法。

# 将您的数据库连接参数放在一起

尽量避免将数据库凭证分散在参数存储中的不同键中，并将它们放在一起。审计哪些用户/角色访问了凭证更容易，因为只有一个地方可以获取凭证。

```
/$env/databases/$appDb/host      = mydb.somewhereinaws.com
                      /user      = read-only
                      /password  = 'a good password'
                      /port      = 5432
                      /dbname    = 'orders'
                      /scheme    = 'postgres'connection_string = $scheme://$user:$password@$host:$port/$dbname
```

这将允许您通过 IAM 策略控制谁有权访问哪些数据库集。您还可以编写脚本，让它自动让您登录数据库，而无需查看密码。这里有一个在 bash 中如何实现的例子

```
dbInfo=$(aws ssm get-parameters \
 --names "/dev/dbs/$dbName/database" \
 "/dev/dbs/$dbName/host" \
 "/dev/dbs/$dbName/password" \
 "/dev/dbs/$dbName/port" \
 "/dev/dbs/$dbName/user" \
 "/dev/dbs/$dbName/scheme" \
 --region $REGION \
 --with-decryption \ 
 --query Parameters[*].Value \
 --output text | tr "\t" " ") database="${dbInfo[0]}"
database_host="${dbInfo[1]}"
database_pass="${dbInfo[2]}"
database_port="${dbInfo[3]}"
database_scheme="${dbInfo[4]}"
database_user="${dbInfo[5]}"if [ "$database_scheme" = "mysql" ]; then 
  MYSQL_PWD=$database_pass mysql -h $database_host -P $listen_port -u $database_user $database_database 
else 
  PGPASSWORD=$database_pass psql -h $database_host -p $listen_port -U $database_user -d $database_database 
fi
```

# 坚持命名惯例

这是我们用于我们的参数的约定，已经调整好了。

```
/$environment_name/databases/$database_name/{host,port,pass,user} 
                  /databags/$service_name/{all,my,server,creds}
                  /other_sensitive_info/{foo,bar,baz}
```

我们用厨师烹饪书&加密的数据标签从我们的日子里借来了`databags`。这基本上意味着在属于一个服务的键下有一堆参数。

这让我们可以通过 IAM 策略在几个不同的维度上确定访问范围。我们可以说，在 dev 环境中，开发人员应该可以访问数据库机密，但在 prod 中只能访问服务机密。

```
{ 
  "Effect": "Allow", 
  "Action": [ "ssm:GetParameters" ], 
  "Resource": [ 
    "arn:aws:ssm:us-east-2:xxxx:parameter/dev/databases/*",
    "arn:aws:ssm:us-east-2:xxxx:parameter/prod/databags/myService/*"
  ] 
} 
```

# 你不能把大项目塞进参数里

我们曾经使用证书指南在我们的主机上安装证书，这要求我们以这种格式保存我们的证书

```
{ 
  "id": "mail", 
  "cert": "-----BEGIN CERTIFICATE-----\nMail Certificate Here...", 
  "key": "-----BEGIN PRIVATE KEY\nMail Private Key Here...", 
  "chain": "-----BEGIN CERTIFICATE-----\nCA Root Chain Here..." 
}
```

但是由于 4096 个字符的[大小限制](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_Parameter.html)，您不能将它作为 json 填充到参数中。当您迁移机密时，这更麻烦，但是很容易解决。不幸的是，它将要求你更新一些脚本，这些脚本需要以前的秘密格式。

# 使用标签/指针帮助您轮换密码

密码轮换是一个固有的难题，但是一个策略是使用指向秘密的指针(或者他们现在称之为标签)。例如，假设您想要轮换一个加密密钥，但是有多个服务依赖于它。

您可以这样定义您的结构:

```
/dev/databags/my-key/current = 2 (index of actual secret) 
                    /1       = 'secret 2018' 
                    /2       = 'secret 2019'
```

您的应用程序需要知道如何跟随来自`current` 的指针并读取想要的秘密。从那里，您必须重新生成一个配置文件并退回您的服务。

幸运的是，[你现在可以在 AWS](https://aws.amazon.com/blogs/mt/use-parameter-labels-for-easy-configuration-update-across-environments/) 上通过参数标签来实现。
使用 Lambda 和 Secrets Manager 也可以完成旋转密码[——点击这里查看这个演练](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets-lambda-function-overview.html)

# param store 保留参数的版本，但如果您删除它们，则不会

文档表明参数是版本化的，并且当它们存在时它们是版本化的。但是如果你不小心删除了一个参数，历史记录也随之消失了。

共识似乎是你应该把你的参数存储数据库导出到 S3 或者 Dynamodb，但是我还没有碰到过这个领域的工具。

# 一些支持参数存储的相关工具

秘密地将秘密输出到你的环境中，用 python 写的—[https://github.com/energyhub/secretly](https://github.com/energyhub/secretly)

parameter-store-exec —类似 secretly，用 go 写的。—[https://github.com/cultureamp/parameter-store-exec](https://github.com/cultureamp/parameter-store-exec)

放入配置文件的 confd—[https://github.com/kelseyhightower/confd](https://github.com/kelseyhightower/confd)

作为咨询模板的一部分—[https://github.com/hellofresh/consul-template-plugin-ssm](https://github.com/hellofresh/consul-template-plugin-ssm)

包括参数库在内的密室管理—[https://github.com/segmentio/chamber](https://github.com/segmentio/chamber)

*原载于 2018 年 10 月 20 日*[*www . intra cide cloud . io*](https://www.intricatecloud.io/2018/10/a-few-lessons-learned-using-aws-parameter-store/)*。*