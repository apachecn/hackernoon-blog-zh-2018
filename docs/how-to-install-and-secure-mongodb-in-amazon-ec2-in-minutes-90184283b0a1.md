# 如何分分钟在亚马逊 EC2 中安装并保护 MongoDB？

> 原文：<https://medium.com/hackernoon/how-to-install-and-secure-mongodb-in-amazon-ec2-in-minutes-90184283b0a1>

## 在本帖中，我们将在 Amazon EC2 实例中安装 MongoDB，并使用身份验证机制保护它。

![](img/4bb5c5229fedd53d888abb0d6420cdfa.png)

只有当一个人知道安全工作的重要性时，他才知道安全的重要性。

> 据说超过 30%的 MongoDB 数据库及其连接是不安全的。

你不想保护它吗？当然，每个人都喜欢保护他们托管的任何东西。让我们开始实施吧。

MongoDB 站点中的步骤不足以让一个 noob 成功完成这些步骤。这发生在我个人身上。在浏览了近数百篇帖子后，我最终决定完美地记录下来，因为我知道**分享是关爱**。如果你在这篇文章中发现任何困难，请随时评论。我会尽快回复你。

我假设您已经知道 Amazon Web services，并且对 EC2 知之甚少。如果你不是，不要担心。我在下面的帖子中创建了一个关于 AWS(Amazon Web Services)和 EC2 实例设置的教程。请求你经历同样的事情。

[](/@balasubramanim/make-your-amazon-ec2-instance-up-and-running-5d645c2a1f03) [## 启动并运行您的 Amazon EC2 实例。

### 在这一部分，我们将创建一个 Amazon 帐户 EC2 实例，并通过 SSH 连接到该实例。

medium.com](/@balasubramanim/make-your-amazon-ec2-instance-up-and-running-5d645c2a1f03) 

## 让我们安装 MongoDB。

一旦您准备好了终端，让我们开始在其中安装 MongoDB。由于我们将在几分钟内完成安装，如果您需要，我会要求您浏览参考资料部分的以下链接，了解下面提到的命令的详细用法。

**步骤 1** :导入包管理系统使用的公钥。

```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5
```

**第二步**:为 MongoDB 创建一个列表文件。(适用于 Ubuntu 16.04)

```
echo “deb [ arch=amd64,arm64 ] [https://repo.mongodb.org/apt/ubuntu](https://repo.mongodb.org/apt/ubuntu) xenial/mongodb-org/3.6 multiverse” | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list
```

**第三步**:重新加载本地包数据库。

```
sudo apt-get update
```

**第四步**:安装 MongoDB 包。

```
sudo apt-get install -y mongodb-org
```

这将安装 MongoDB 的最新稳定版本。

就这样，我们已经在我们的 Amazon EC2 实例中安装了 MongoDB 最新的稳定版本。

> 在下面的过程中，我们将首先创建一个`*admin*`，他通常对您的数据库有根访问权限，然后`*admin*`将创建一个`*user*`，他可以被创建为具有特定角色和对特定数据库的访问权限。最后，我们将使用`*user*`凭证连接到我们的数据库并开始处理它。

## 让我们开始 MongoDB

**第一步**:启动 MongoDB。发出以下命令启动`[mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)`。

```
sudo service mongod start
```

**步骤 2** :通过键入以下命令，验证 MongoDB 是否已经成功启动。

```
cat /var/log/mongodb/mongod.log
```

确保你在最后找到下面的线。

```
[initandlisten] waiting for connections on port 27017
```

就是这样。您现在正在远程实例中成功运行 MongoDB 实例。

**注意:**发出以下命令停止`[mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)`(不需要时不要执行)

```
sudo service mongod stop
```

**注意:**发出以下命令重启`[mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)`(不需要时不要执行)

```
sudo service mongod restart
```

## 是时候保护你的 MongoDB 了。

在 MongoDB 部署上启用访问控制会强制执行身份验证，要求用户标识自己的身份。当访问启用了访问控制的 MongoDB 部署时，用户只能执行由其角色决定的操作。

下面的过程首先将用户管理员添加到不带访问控制的 MongoDB 实例中，然后启用访问控制。

门禁？—是的，当您使用 AccessControl 运行 MongoDB 时，您会说，启动安全的 MongoDB 实例。

`admin` —该用户可以管理用户和角色，如:创建用户、授予或撤销用户角色、创建或修改自定义角色。

## 程序

**第一步**:创建一个存放数据的目录，并设置权限。

```
//Creating a path to store DB data.sudo mkdir -p /data/db//Giving yourself permission to write in that folder.

sudo chown $USER /data/db
```

如果你在设置这个文件夹和权限时有任何困难，你可以参考这个 [StackOverflow](https://stackoverflow.com/questions/7948789/mongodb-mongod-complains-that-there-is-no-data-db-folder) 链接。

**第二步**:无访问控制启动 MongoDB。

```
mongod --port 27017 --dbpath /data/db
```

**第三步**:让 MongoDB 实例在这个终端实例上运行。现在让我们在另一个远程终端实例(比如终端 2)上工作，这样我们将在创建一个用户后来回切换，并重启 Mongo 实例。

打开一个新的终端并连接到您的 EC2 实例。现在我们将在 Mongo Shell 上工作，它用于在您创建的数据库中运行命令或查询。

现在我们将通过发出下面的命令来创建 Mongo Shell。

```
mongo --port 27017
```

现在，您的终端将看起来像下面的图像，这是准备运行命令。

![](img/09ee273f02563cbb8e0a780cab74363c.png)

Mongo Shell

**第四步**:创建用户管理员(`admin`

在`admin`数据库中，添加一个角色为`[userAdminAnyDatabase](https://docs.mongodb.com/manual/reference/built-in-roles/#userAdminAnyDatabase)`的用户。该数据库充当 admin DB，我们创建它只是为了进行身份验证。

发出以下命令切换到 admin DB，即使您还没有创建它。当您发出该命令时，它将自动创建。

```
use admin
```

切换到 admin DB 后，让我们通过发出以下命令来创建一个`admin`。

```
db.createUser({user: "admin", pwd: "adminUser123", roles: [{role: "userAdminAnyDatabase", db: "admin"}]})
```

它被美化为(在美化的命令之上，不要运行两次)

```
db.createUser(
 {
 user: “admin”,
 pwd: “adminUser123”,
 roles: [ { role: “userAdminAnyDatabase”, db: “admin” } ]
 }
)
```

现在您已经创建了一个名为`admin`的用户，其角色为`userAdminAnyDatabase`。

按下`Ctrl+C`断开 mongo 外壳。

**步骤 5** :切换回运行它的旧 mongod 实例(终端 1)。让我们使用访问控制重新启动 MongoDB 实例，以获得对数据库的管理员访问权。请记住，您现在开始使用访问控制来启动 Mongo 实例。

使用`--auth`命令行选项重新启动`[mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod)`实例。

```
mongod --auth --port 27017 --dbpath /data/db
```

**步骤 6** :切换回终端 2 (mongo shell 实例)。

通过发出以下命令，以用户管理员的身份进行连接和身份验证。

```
mongo --port 27017 -u "admin" -p "adminUser123" --authenticationDatabase "admin"
```

**步骤 7** :根据部署需要创建额外的用户(`user`)。

一旦被认证为用户管理员，使用`[db.createUser()](https://docs.mongodb.com/manual/reference/method/db.createUser/#db.createUser)`创建额外的用户。您可以将任何[内置角色](https://docs.mongodb.com/manual/core/security-built-in-roles/)或[自定义角色](https://docs.mongodb.com/manual/core/security-user-defined-roles/)分配给用户。以下操作将用户`myTester`添加到`test`数据库中，该用户在`test`数据库中拥有`[readWrite](https://docs.mongodb.com/manual/reference/built-in-roles/#readWrite)`角色

发出以下命令创建一个名为`test`的数据库并使用它。

```
use test
```

现在让我们在这个数据库中创建一个用户，为她/他分配特定的角色。

```
db.createUser({user: "user", pwd: "user123", roles: [{role: "readWrite", db: "test"}]})
```

它被美化为(在美化的命令之上，不要运行两次)

```
db.createUser(
  {
    user: "user",
    pwd: "user123",
    roles: [ { role: "readWrite", db: "test" } ]
  }
)
```

现在，您已经成功地创建了一个具有特定角色和数据库访问权限的用户。

**步骤 8** :现在连接并认证为`user`。

现在，通过在同一个 Mongo Shell 中发出以下命令，连接到具有用户角色的实例。

```
mongo --port 27017 -u "user" -p "user123" --authenticationDatabase "test"
```

现在，您已经安全地通过了名为`test`的数据库用户身份验证。如果您想执行一些查询操作，您可以发出下面的命令并立即测试。

```
db.foo.insert( { x: 1, y: 1 } )
```

它创建了一个名为`foo`的集合，并在其中插入了前面提到的 JSON。

## 通过蒙戈 URI 连接字符串连接。

下面给出了使用 mongoose 模块从 Node.js 服务器安全连接到该实例所需的连接字符串。

```
mongodb://user:user123@localhost:27017/test
```

是的。您已经在远程服务器上安全地安装和配置了 MongoDB。让我们休息一会儿。

在下一篇教程中，让我们使用 [Docker](https://www.docker.com/) 并创建一个映像，并自动完成这一过程，这样，在任何情况下，只需一个 Docker 映像就可以在一条命令中安装并保护 MongoDB。

谢谢你。

参考资料:

[](https://docs.mongodb.com/manual/tutorial/enable-authentication/) [## 启用 Auth - MongoDB 手动

### 您可以在启用访问控制之前或之后创建用户。如果您在创建任何之前启用了访问控制…

docs.mongodb.com](https://docs.mongodb.com/manual/tutorial/enable-authentication/) [](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/) [## 在 Ubuntu 上安装 MongoDB 社区版- MongoDB 手册

### 由于旧版本的 Ubuntu 16.04 POWER 软件包中存在锁省略错误，您必须升级…

docs.mongodb.com](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)