# 使用 Laravel part 0 创建简单的酒店预订::在 AWS cloud9、Github、AdminLTE、Heroku 上安装 Laravel

> 原文：<https://medium.com/hackernoon/create-simple-hotel-booking-with-laravel-part-0-install-laravel-on-aws-cloud9-github-adminlte-c178cdd6159c>

[![](img/4b9d8cffa2868bd6015341b673e6bea7.png)](https://leanpub.com/create-simple-inventory-management-in-laravel57)

> AWS Cloud9 提供基于云的 Ubuntu 环境。它具有一个基于浏览器的编辑器，支持许多语法高亮和单词补全，一个基于 GUI 的 GDB 调试，对基于云的 Ubuntu 环境的完全控制，以及许多其他功能，包括主题，可定制的布局和键盘快捷键。因为它是基于云的，即使你使用不同的计算机，你也可以继续做你的习题集！
> 您还可以集成 Lambda、Codestar、Light Sail 等其他 AWS 服务。

# [**在 Leanpub**](https://leanpub.com/create-simple-inventory-management-in-laravel57) 上获取这本书的全部内容

在这一章中，我已经演练了如果我们需要跟我来

您需要 AWS 帐户

![](img/e958e9f81c4887e17455885db9d60413.png)

k

![](img/5a459274f00218908446a943d4db704d.png)

接下来，我们创建环境

![](img/c968e62853ecdf5ab816c5c756cf6c6f.png)

填充名称和描述

然后

![](img/f27696dad55e3ad34341670c9c002233.png)

选择环境类型和实例类型，查看您在图像中看到的更多描述

![](img/ab32f6a57ce95f920cd9727a5b3b1ea0.png)

最后一步，我们看到了实例摘要，然后等待

![](img/35dfb436149eec9d495111f86645341c.png)

你有了新操场

试着自己去发现[文档](https://docs.aws.amazon.com/cloud9/latest/user-guide/welcome.html)

这个版本需要 php 7.1.3 或更高版本，现在这台机器上的 php 是 5.6

![](img/42038904b6d2010ec32cb32657314ede.png)

当你试着去得到

![](img/f4095a1c1b9065357c9837b9465376d0.png)

因为基于 Centos 的 Amzon linux

所以首先我们需要用这个命令删除 php 5.6

`sudo yum remove php56*`

![](img/3b39fcb28487a3e381c7d6c8eb1eae9e.png)

我们删除了 php5.6 中所有与*相关内容

我们用这个命令安装所有与 php 7.2 相关东西

`sudo yum install php72*`

![](img/2a6bda87e6667637d8be9cbb7b03cd5f.png)

接下来尝试 php -v

![](img/db56dfd77d1ea7c3ac4fefc23936e2c7.png)

下一次安装作曲家

导航至[作曲家下载页面](http://getcomposer.org/download)

![](img/ce68dc095ea54df7ec8dd94b86d54ffe.png)

我们在终端使用 php

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '93b54496392c062774670ac18b134c3b3a95e5a5e5c8f1a9f115f203b75bf9a129d5daa8ba6a13e2cc8a1da0806388a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

在终端中复制粘贴

![](img/149439e351f74b7bed05ccc38536afb4.png)

当安装成功时，你会在文件管理器中看到 composer.phar

下一次尝试调用编写器

![](img/e0f4c83fef7327974e3bce2fbcb00351.png)

在这种情况下，我们需要调用长命令总是浪费时间

然后，我们需要将 composer 命令与

```
sudo mv composer.phar /usr/local/bin/composer
```

![](img/ec8d1d444228a813dd3407e8cd554133.png)

现在您可以直接调用 composer

使用命令`composer create-project laravel/laravel`

![](img/f94afc9db8c95e45c20a5799c03861f0.png)

当你在文件管理器中运行完成时

![](img/6e01236ffbdbbd1809b815cdfc7f29ee.png)

接下来，我们将 **Sqlite** 用于开发模式。所以打开`.env`

![](img/dcb2e2f649aec639eb808402414117f3.png)

把 mysql 改成 sqlite

![](img/bfccd6ba74a1bf328ebcb79a3066d369.png)

以及删除或插入注释

![](img/838517e8d7e88f826db02ec678980d2a.png)

不要忘记将 Laravel 文件夹中的每个文件移到根目录

![](img/c4b96b42e523ca75ffb7a421c7a47e70.png)

现在试试

![](img/1bbba48f80bf37eece84dd49e8620a17.png)

错误来自于我们忘记了创建 SQlite 文件，这是用文件管理器创建的简单方法

![](img/2ba691b057b2c45778a590ad46df25ae.png)

现在又是`php artisan migrate`

![](img/680536fc77f80589fa5642b9628a9b12.png)

接下来，我们使用 php 内置服务器在端口 8080 上使用 artisan 命令启动服务器，因为 **Cloud9** 允许端口 8080、8081 和 8082

![](img/2e259954380e63ab3887fda33782848a.png)

然后选择预览，并选择预览正在运行的应用程序

![](img/c2454a8e0d40645f193837823286b68f.png)

你已经看到新窗口出现在终端的侧面

我的天啊，我在一个页面中集成了文件管理器、文件编辑器、终端和浏览器

![](img/db4dcb181287e61cffa152176044c0f1.png)

您可以在新标签页中打开

![](img/5c8286c2d36cb16e1b303423ecca114e.png)

接下来，我们为 Laravel 安装[admin LTE](https://github.com/jeroennoten/Laravel-AdminLTE)

![](img/3a76a0201701b8384603804b68f228c4.png)

并遵循指南进行安装

```
composer require jeroennoten/laravel-adminlte
```

![](img/899d45499f2d55de9a3d73b124f37504.png)

下一次打开`app.php`插入这个

```
JeroenNoten\LaravelAdminLte\ServiceProvider::class,
```

![](img/9b133a1ef55920c82a3faa70f6b3a338.png)

发布**管理**资产

```
php artisan vendor:publish --provider="JeroenNoten\LaravelAdminLte\ServiceProvider" --tag=assets
```

![](img/75f19d12696c80595ca04f821701ff48.png)

接下来，我们用`php artisan make:adminlte`创建 Laravel 内置授权

![](img/84de54691f32529a694235cec07e023e.png)

用`php artisan serv --port=8080`运行服务器并运行预览

![](img/4a731ac5dee9913778ddd0458c46fa93.png)

尝试注册

![](img/eba9e64549995687f07aea4e0ab2f5b2.png)

我们重新回到家

![](img/bb7c481466e55da22ecf3fb78130aa0b.png)

## Github 部分

创建[新存储库](https://github.com/new)

![](img/7936768174922ef2b43614c420258946.png)

填写名称和描述，然后单击创建

![](img/c718c6706aa4ebd6e41c2412c900617a.png)

使用终端上的`git init`

![](img/1b47a9ac2bb380fca29e9f27c9a2595a.png)

`git add .` 跟踪所有要提交的文件，然后`git commit -m “first commit”`

![](img/d0fd7e908e1edccbf329089c913b2b27.png)

修复设置提交用户名和电子邮件的 git 警告

```
git config --global user.name "kriss"
git config --global user.email [k](mailto:you@example.com)rissanawat101@gmail.com
```

下一个远程存储库路径

```
git remote add origin git@github.com:krissnawat/larahotel.git
```

下一步尝试推送代码

```
git push -u origin master
```

这个错误来自我们没有写的权限

![](img/af2ba8ca942c0a21be74a9cb41837218.png)

解决这个问题的方法是创建公钥和私钥

配合使用`ssh-keygen`

![](img/9c0d6823b688668ab72b44cbc53f1d42.png)

我们使用默认文件夹，不设置密码

下一次复制公钥

`cat /home/ec2-user/.ssh/id_rsa.pub`

![](img/745b8a52757e9b788f9a55eca28161c4.png)

并粘贴你的 [Github 设置](https://github.com/settings/ssh/new)

![](img/30c9a285dad338ebeec1dc2ce0a6edd8.png)

现在我们成功地将公钥添加到 Github 中

![](img/59584af3927f1abafca36327e896a0bd.png)

回来再推第一次提交

![](img/30dd35849e1db745838bd4281c944ed6.png)

成功

![](img/f96abf32eda5077a7087f1bec621658d.png)

## Heroku 部分

接下来我们让 demo 一直住在 Heroku

转到[注册页面](https://signup.heroku.com/login)

![](img/9cfcc65095c54e2d0f95f794e44042d9.png)

认证过程后，您已重定向到此页面

![](img/9528a012df53aaa2f5f164c5a276608b.png)

尝试创建新应用程序

![](img/12ff157252d78d4544a8c5d2227d77d1.png)

我们重定向到应用仪表板

![](img/39fb3a057cf8145c3a3916cf11c756ca.png)

在部署方法中，我们选择连接到 **Github**

搜索您需要部署的回购

![](img/37cc0b51b0669ae5dbc0298a124e9be7.png)

然后单击自动部署

![](img/67e0536be6e7f9906c4aa9fa468b1d34.png)

现在，在你将触发部署的代码推送到 **Heroku** 之后，我们进入自动部署模式

![](img/038ce6d85f489c5bad11244364ad9723.png)

签出活动选项卡

![](img/331e3f69178e8875a60358eadd90551e.png)

您已经看到了在您的虚拟机中是如何发生的

![](img/71417f3f2452d3e0b9772139d3bed953.png)

尝试将代码推送到 **Github** 并刷新活动选项卡

您已经看到自动部署被触发

![](img/5943df3bae8cad143b0b40b322b47d4b.png)

并部署完整的试用版实时应用程序

![](img/102b9210da20cae78e4e5f1891fb7dd9.png)

这个错误来自我们没有推动。env 到 **Github** ,因为这包含了更多用于替代方式的凭证

**Heroku** 为句柄环境变量提供 **config_var**

![](img/e159157f3baf1e83e2b63c64b00de3d7.png)

第一步尝试显示错误

![](img/907e89b61aa62735a311b2d2be52a7fd.png)

你看到了吗

![](img/6e8cb9b6dd5cc71fdd30e54fbabd530b.png)

因为我们没有设置 **APP_KEY**

![](img/7123f91eb6cc3e9d3e8bdfc53c6c2f39.png)

你的演示会成功的

![](img/82ea149c1c262f3ae30466c1a9bfef64.png)

为什么不使用 Cloud9 来显示直播应用程序，因为如果你需要运行 EC2，而使用 Heroku，你可以让你的演示 24/7 免费直播

我们完成了第一部分，接下来我们将创建 **CRUD** 和 **CRUD** 和 **CRUD**

不要忘记将代码推送到 **Github**

[![](img/cc480749083e914f03554e1acd85f3b8.png)](https://www.patreon.com/krissanawat)

*最初发表于*[*Laraboss*](http://laraboss.com/create-simple-hotel-booking-with-laravel-part-0-install-laravel-on-aws-cloud9-github-adminlte-heroku/)*。*