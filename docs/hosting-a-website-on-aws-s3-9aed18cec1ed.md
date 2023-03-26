# 在 S3 AWS 上托管一个网站

> 原文：<https://medium.com/hackernoon/hosting-a-website-on-aws-s3-9aed18cec1ed>

![](img/3973c124da97d513c7caa42574eb2e6f.png)

亚马逊网络服务(AWS)简单存储服务(S3)可以以极低的成本托管静态网站。大多数网站的费用是每月几美分至几美元(对于一个非常大，高流量的网站)。

在这篇文章中，我将讨论两个用 Java 编写的软件工具，它们允许将大型网站迁移到 S3，并在网站位于 S3 后进行维护。

我的个人网站[bearcave.com](http://bearcave.com)于 1995 年上线，当时网络还处于早期。

[](http://bearcave.com) [## 熊产品国际主页

### 编辑描述

bearcave.com](http://bearcave.com) 

多年来，bearcave.com 已经发展到超过 2300 个网页。这些页面包括我在[小波信号处理](http://bearcave.com/misl/misl_tech/wavelets/index.html)和[计算金融](http://bearcave.com/finance/index.html)上所做的工作。

直到最近，我还在一个商业 Linux 网络主机上主持 bearcave.com(为了保护罪犯，名字被省略了)。与在 AWS 上托管相比，每月 35 美元(对于“白金支持”)是非常昂贵的。

在亚马逊 S3 上建立了我的咨询网站[topstonesoftware.com](http://topstonesoftware.com)后，我打算把 bearcave.com 搬到 S3。我一直推迟这个项目，因为它是一个大任务。最后，我使用的网络主机提供了足够的动力。他们把我的 Linux 主目录和网站移到了新的服务器上，丢失了我已故母亲的艺术作品的照片。几周前，我收藏了几年旅行照片的照片库停止了工作(尽管幸运的是，这些照片没有像我母亲的照片库那样丢失)。

当我向网络主机投诉时，他们毫无歉意。虽然我是他们多年的客户，但他们并不在乎他们丢失了照片，破坏了我的网站。这种傲慢的待遇最终促使 bearcave.com 搬到了 S3。

bearcave.com 的大部分由静态网页组成。除了一些死链接，bearcave.com 唯一被破坏的部分是照片画廊。

我最初使用基于 PHP 的图库管理我的图库。

 [## 图片库|您网站上的照片

### 那么下一步是什么？不久之后，我们将把网站设为只读。所有的论坛帖子和主题仍然会在那里，但是…

galleryproject.org](http://galleryproject.org) 

为了支持我在 AWS S3 上的照片库，我需要从基于 PHP 的动态照片库转移到基于 JavaScript 的静态照片库。

当我为社交网络[under ground](http://www.nderground.net)建立图片库时，我使用了基于 Galleria JavaScript 的图片库。

 [## Galleria |响应 JavaScript 图像库

### 一个免费的 JavaScript 图片库框架，简化了为网络创建漂亮图片库的过程…

galleria.io](http://galleria.io) 

有了这次经历，我决定把 bearcave.com 的照片画廊搬到 Galleria。

我使用的基于 PHP 的图库版本将所有照片和照片元数据(如标题)存储在文件中。照片元数据，包括标题信息，存储在一组名为 photo.dat，photos.dat.0，photos.dat.1，…

我想从元数据中恢复标题，并构建 Galleria 将使用的 HTML。这是一个几乎不可能手工完成的任务，尤其是因为我有数百张照片。

我编写了 GalleryBuilder Java 应用程序来处理画廊元数据并构建 Galleria HTML。这个应用程序可以在 GitHub 上找到:

 [## IanLKaplan/GalleryBuilder

### Gallery builder——一个 Java 应用程序，它将从图库目录中构建静态图片图库 HTML

github.com](https://github.com/IanLKaplan/GalleryBuilder) 

GalleryBuilder 是一款专门用于转换基于图库的照片图库的工具。它只能在基于文件的 Gallery 版本上工作(后来的版本使用 mySQL 来存储照片和照片元数据)。

一旦我为基于 Galleria 的新图片库生成了 HTML，我就使用 JetBrains IntelliJ 集成开发环境来构建和测试我的基于 Galleria 的新网页。

[](https://www.jetbrains.com/idea/) [## IntelliJ IDEA:JetBrains 为专业开发人员提供的 Java IDE

### 适用于企业 Java、Scala、Kotlin 等的功能强大且符合人体工程学的 Java IDE...

www.jetbrains.com](https://www.jetbrains.com/idea/) 

当 Galleria 图片库完成时，我有了一个(大部分)工作网站。下一步是将网页转移到 S3 AWS。

S3 有一个基于网络的用户界面，允许用户移动文件到 S3 和创建目录。不幸的是，如果不费力地创建一个目录，然后移动该目录中的所有文件，就无法移动本地计算机上的目录。以这种方式移动全部 2300 个 bearcave.com 网页将会非常耗时并且容易出错。

我编写了 Java S3Update 应用程序来将目录树从本地系统移动到 S3。这段代码可以从 GitHub 下载(你需要知道如何构建、编译和执行 Java 代码)。

[](https://github.com/IanLKaplan/S3Update) [## IanLKaplan/S3 更新

### S3 update——一个 Java 应用程序，它使用来自本地的文件复制/更新 Amazon Web Services S3 存储桶中的文件

github.com](https://github.com/IanLKaplan/S3Update) 

S3Update 应用程序是用 Eclipse 开发的，GitHub 存储库包含 Eclipse 项目文件。

当本地系统上的副本发生变化时，S3Update 应用程序还将更新 S3 上的文件。这使得维护 S3 网站变得更加容易。

S3 存储是一种 web 资源，可以通过 HTTP 访问。访问 S3 文件的操作花费大部分时间等待 HTTP 事务完成。为了提高性能，S3Update 应用程序是多线程的。这允许一个线程在另一个线程等待时读取或写入。对于 32 线程，这使我的本地网络连接饱和(我只有 10Mb/秒的网络连接，所以你的里程可能会有所不同)。

要使用 S3Update 代码，您需要使用 IAM 服务创建一个 AWS ID 和密钥。这些键应该具有读写 S3 的权限(AmazonS3FullAccess)。

在你把网站搬到 S3 之前，你需要在亚马逊上建立一个静态网站。他们已经出版了一个相当好的指南来指导如何做这件事:

[](https://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-custom-domain-walkthrough.html) [## 示例:使用自定义域设置静态网站——Amazon 简单存储服务

### 浏览一个如何使用自定义域建立静态网站的代码示例。

docs.aws.amazon.com](https://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-custom-domain-walkthrough.html) 

这将需要一个亚马逊网络服务帐户。

当你设置你的 S3 主机时，你需要改变你的域名系统，以便它由亚马逊处理。这将需要您登录到您的域名提供商(例如，godaddy.com，networksolutions.com 等)，以改变亚马逊提供的 DNS 服务器的 DNS。

在 S3 上托管一个网站的低成本使它成为托管静态网站的一个有吸引力的选择。S3Update 工具使托管和维护网站变得更加容易。

最后一个警告:建立和维护一个 S3 网站的过程是复杂的，可能超出了许多非技术用户的舒适区。像 wix.com 这样的网站使建立一个网站变得更加容易，并且包括各种吸引人的模板，你可以用它们来建立简单的网站。虽然这些商业虚拟主机平台比 AWS S3 贵，但对于不是 web 开发人员的人来说，这个价格可能是值得的。