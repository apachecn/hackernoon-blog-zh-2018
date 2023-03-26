# 在 Laravel 5.7 第 0 部分创建健身房管理::在 AWS cloud9 上安装 Laravel 并设置 Github、AdminLTE，然后在 Heroku 上部署

> 原文：<https://medium.com/hackernoon/create-gym-management-in-laravel-5-7-part-0-install-laravel-on-aws-cloud9-f942f37be2c8>

![](img/3fbb0ca6ee493194f31659ca9708277c.png)

## 旅程赞助商

[**使用 Laravel 和 Vue.js 进行 Fullstack Web 开发**](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1608944&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Flaravel-vuejs-fullstack-web-development%2F)了解如何使用 Laravel 5、Laravel Mix、Vue js、Bootstrap 4 & Sass 构建 fullstack web 应用

[**带推送器的实时单页论坛 App&vuejs**](https://click.linksynergy.com/link?id=qt/jYwyHv8A&offerid=507388.1587178&type=2&murl=https%3A%2F%2Fwww.udemy.com%2Freal-time-single-page-forum-app-with-pusher-laravel-vuejs%2F)在单页 App 中用推送器实时制作东西

AWS Cloud9 提供基于云的 Ubuntu 环境。它具有一个基于浏览器的编辑器，支持许多语法高亮和单词补全，一个基于 GUI 的 GDB 调试，对基于云的 Ubuntu 环境的完全控制，以及许多其他功能，包括主题，可定制的布局和键盘快捷键。因为它是基于云的，即使你使用不同的计算机，你也可以继续做你的习题集！
您还可以与 Lambda、Codestar、Light Sail 等其他 AWS 服务集成

在这一章中我已经演练了如果我们需要跟我来

您需要 AWS 帐户

![](img/d4cd9f09006a3561cdd9a9e130d6bd4f.png)

尝试搜索 Cloud9

![](img/fe27dd903f854acbee87fab51f94062f.png)

接下来，我们创建环境

![](img/6724c38f907c91849d759ada6f01351b.png)

填充名称和描述

然后

![](img/3480e0063dfc0774f0b1c31873979f7f.png)

选择环境类型和实例类型，查看您在图像中看到的更多描述

![](img/3afa1cb8c5ed3605f068cd0fd8e312c3.png)

上一步我们看到了实例摘要

![](img/008bfb197adc6222cf9c3c37e8507959.png)

然后等待…

![](img/645cfbbd57d6dcece208bb296ef3593c.png)

你有了新操场

试着自己去发现文档

## 安装 Laravel 5.7

这个版本需要 php 7.1.3 或更高版本，现在这台机器上的 php 是 5.6

![](img/89ffd27f91646342ad4ea4fd37a0f4be.png)

当你试着去得到

![](img/4fb7dd21d50f1846af5403b40f2982bf.png)

因为基于 Centos 的 Amzon linux

所以首先我们需要用这个命令删除 php 5.6

![](img/ef4a6b93e4c8ca3dcc772c314d906011.png)

`sudo yum remove php56*`

我们删除了 php5.6 中所有与*相关内容

我们用这个命令安装所有与 php 7.2 相关东西

`sudo yum install php72*`

![](img/7d4fc4636697e59f70dfa4e438ca2ee3.png)

接下来尝试 php -v

![](img/c94b8bc809b183ddcc3dba31f92ef10e.png)

下一次安装作曲家

导航至[作曲家下载页面](http://getcomposer.org/download)

![](img/6f01a5852aa37a099897c18bda899b30.png)

我们在终端使用 php

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '93b54496392c062774670ac18b134c3b3a95e5a5e5c8f1a9f115f203b75bf9a129d5daa8ba6a13e2cc8a1da0806388a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

在终端中复制粘贴

![](img/561b3a1957eb7d8af2df97f6b3a97aa5.png)

当安装成功时，你会在文件管理器中看到 composer.phar

下一次尝试调用编写器

![](img/b32d8e6bb2eb8d2ae984ac75adea1c25.png)

在这种情况下，我们需要调用长命令总是浪费时间

然后，我们需要将 composer 命令与

```
sudo mv composer.phar /usr/local/bin/composer
```

![](img/31fc0462413a67b808b634255dc8b27b.png)

现在您可以直接调用 composer

## 安装 Laravel 5.7

使用
命令`composer create-project laravel/laravel`

![](img/62d8c084d7feb3149d3d133bf2606abc.png)

当你在文件管理器中运行完成时

![](img/ca0ebf2f102a853f1570ca7d687d0095.png)

接下来，我们将 Sqlite 用于开发模式。所以打开`.env`

![](img/eb6934d09c0dc5c700462fbc6c5132d8.png)

把 mysql 改成 sqlite

![](img/a64182d7a6e7680807df8cdb3c4134c5.png)

以及删除或插入注释

![](img/f628d434e18d19e23e3a1c5051f1e389.png)

不要忘记将 laravel 文件夹中的每个文件移到根目录

![](img/2fee66abfd5b7bdaffa0e2ee17c2f2d2.png)

现在试试`php artisan migrate`

![](img/d13a5f5104bf153c73624d12170318cc.png)

错误来自于我们忘记了创建 sqlite 文件，这是用文件管理器创建的简单方法

![](img/b9c266f35afccfa53bea182369fb28c4.png)

现在又是`php artisan migrate`

![](img/0a725734aaaa21a32e1e13fecdb09968.png)

接下来，我们使用 php 内置服务器在端口 8080 上使用 artisan 命令启动服务器，因为 cloud9 允许端口 8080、8081 和 8082

![](img/304ffc6b7e680511584c0a367a3f21a6.png)

然后选择预览，并选择预览正在运行的应用程序

![](img/e44d7fedb8933419cce0c6891bdc7991.png)

你已经看到新窗口出现在终端的侧面

我的天啊，我在一个页面中集成了文件管理器、文件编辑器、终端和浏览器

![](img/1f55d302d4c763530e504be3578a43c5.png)

您可以在新标签页中打开

![](img/a20b37e9fd5af6fae0830dd96d0350ce.png)

明白了，我们已经在 **AWS cloud9** 上完成了 **Laravel** 的设置

# Github 部分

接下来我们创建 [Github 仓库](https://github.com/new)

![](img/21327bb91f9f38a988242cbbe3b811f1.png)

接下来遵循这个指南

![](img/d1da32d5a8424ef84fc6a69543566cff.png)

git 初始化

![](img/8fba8c3820080c50de831fd01fcf8e7e.png)

`git add .` 用于跟踪所有提交的文件

git commit -m“第一次提交”

![](img/a4a207639c4223fa24fdea8063b9c973.png)

添加远程存储库路径

git 远程添加原点[https://github.com/krissnawat/laragym.git](https://github.com/krissnawat/laragym.git)

下一步尝试推送代码

```
git push — set-upstream origin master
```

在这种情况下，我们使用 HTTP 身份验证，它始终需要用户名和密码，如果您使用 git url，它需要公钥和私钥，我使用这种方法是因为它很容易，下一次提交时，我们将更改为使用私钥和公钥模式

![](img/b2dbfbf3c2ad3cc93a47f57ef4beb33d.png)

推送第一次提交成功

![](img/8f441412b932e05f6008cb08a514f26c.png)

# Heroku 部分

创建 Heroku 帐户

![](img/e7be8a6e06fe0af331d41b052c85b79f.png)

认证过程后，您已重定向到此页面

![](img/61d4bfe24f557394cb17a3e73d9f3f54.png)

尝试创建新应用程序

![](img/0cb4217630f7236cf1d9ddf930fc0759.png)

我们重定向到应用仪表板

![](img/e693a9a79e5fd820b6c082f692f90002.png)

在部署方法中，我们选择连接到 **Github**

![](img/36fadd681778b664de88dbbbe0572b63.png)

搜索您需要部署的回购

![](img/4f1223961cb1c0b918f41a26b0984426.png)

然后单击自动部署

![](img/f95190bc7078359768fd400043aad62e.png)

现在，在您将触发部署的代码推送到 **Heroku** 之后，我们将进入自动部署模式

签出活动选项卡

![](img/b470ba56bad8af073b91ca7b2747c1c0.png)

您已经看到了在您的虚拟机中是如何发生的

![](img/65ddacb43b369824de01f90f969d1c45.png)

尝试将代码推送到 **Github** 并刷新活动选项卡

![](img/4d6f444b5679e72c4508b72063b37f5a.png)

您已经看到自动部署被触发

![](img/9b6f3155e82fb2f78d7f55dfa499d1c8.png)

部署完成

尝试查看实时应用程序

![](img/a179e29eb7dcc7d21732c16018790e84.png)

这个错误来自我们没有推动。env to **Github** 因为这包含了更多替代方式的凭证

**Heroku** 提供**配置变量**

![](img/47b9437b5ea2874e2b55272bf78372fc.png)

第一步尝试显示错误

![](img/e39ac5a63a2a28b433edaca5b1b7dfd1.png)

你看到了吗

![](img/3cf543d50f0de5dd211f32a11a6c82fa.png)

因为我们没有设置 **APP_KEY**

![](img/c02da48e1d3ee6efeef8e3fd60cbb685.png)

这是工作

![](img/4b9e8ecb73ffc61c71b8c60c0021d820.png)

为什么不使用 Cloud9 来显示直播应用程序，因为如果你需要运行 EC2，而使用 Heroku，你可以让你的演示 24/7 免费直播

接下来的快速方式，我们需要一些基本的 kickstart 开发工作流程的管理支架

我选择 [**杨洛诺滕/拉腊维尔-AdminLTE**](https://github.com/jeroennoten/Laravel-AdminLTE)

![](img/6c79f8b70ab9d5afcd5a03e60e797aa9.png)

并遵循指南进行安装

```
composer require jeroennoten/laravel-adminlte
```

![](img/73f265fd656ff2eda93b1c6308e6d4f5.png)

下一个打开 app.php 插入这个

```
JeroenNoten\LaravelAdminLte\ServiceProvider::class,
```

![](img/c59f5ec0f2ae57db79e1706f399d5993.png)

发布**管理**资产

```
php artisan vendor:publish --provider="JeroenNoten\LaravelAdminLte\ServiceProvider" --tag=assets
```

![](img/5a95f804376746876c47cfcc6619602b.png)

接下来，我们用`php artisan make:adminlte`创建 Laravel 内置授权

![](img/85d1d382ba3ddb49ecb120e1b2980cea.png)

用**PHP artisan server-port = 8080**运行服务器并运行预览

![](img/66a7efcfeeb6e00b3707503b485c17f4.png)

转到注册页面

![](img/701338845554fbd1b6e48f19d4678b2c.png)

尝试注册

![](img/b81d764c74d4b24157c599634f4a1104.png)

将重定向到主页

我们完成了第一部分，接下来我们将创建 **CRUD** 和 **CRUD** 和 **CRUD**

不要忘记将代码推送到 **Github**

## 结束语:

[![](img/43b2daddadddc09193be2c843a023d8d.png)](https://www.patreon.com/krissanawat)