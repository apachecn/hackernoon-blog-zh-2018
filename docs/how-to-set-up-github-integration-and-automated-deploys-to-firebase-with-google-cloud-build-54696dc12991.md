# 如何使用 Google Cloud Build 设置 GitHub 集成并自动部署到 Firebase

> 原文：<https://medium.com/hackernoon/how-to-set-up-github-integration-and-automated-deploys-to-firebase-with-google-cloud-build-54696dc12991>

使用 Firebase CLI 将静态文件部署到 Firebase 主机非常容易，但是随着您的团队或项目的增长，设置可靠的自动部署可以省去一些麻烦。如果没有 CI/CD(持续集成/持续交付，自动化部署的另一个术语)，时间可能会浪费在测试您忘记部署的特性，或者修复失败的测试上，因为在分支被合并之前没有人在分支上运行测试。我不想担心我的使用 Firebase 的 Create-React-App 项目中的东西，所以我使用了 [Google Cloud Build](https://cloud.google.com/cloud-build/) 来:

*   测试我的存储库中的每一个分支，这样当所有测试都通过时，我就可以在 GitHub 上看到那个甜甜的绿点
*   在每次合并到 master 时部署到 Beta 项目
*   在每个新的发布标签上部署到生产项目

![](img/7e24c2d9b63def29085cca44018b3d74.png)

Photo by [Ali Yahya](https://unsplash.com/photos/V4BS2agsRYI?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

## 我为什么选择谷歌云构建

有许多服务可以为您自动部署。云构建对我来说似乎是显而易见的选择，因为我已经在使用 Firebase 进行身份验证和数据处理。其他选择似乎很昂贵，除非你的项目是开源的。由于我的项目只是一个个人项目，69 美元/月对特拉维斯-CI 来说太多了。

# 要求

您需要将一个 web 项目签入到版本控制中，包括测试和构建步骤。Google Cloud Build 可以轻松地与 GitHub、Bitbucket 集成，当然还有 Googles Cloud Source Repository。我的项目使用 Create-React-App 和 GitHub。你需要一个已经创建好的 Firebase 或者 GCP 项目。我的项目有两个，beta 和 production，但是不管项目数量多少，步骤都是一样的——你只需要决定如何设置触发器。
再次重申，为了跟上进度，你需要具备以下条件:

*   为部署构建静态文件的 web 项目(我的项目使用 Create-React-App)
*   托管在 GitHub、Bitbucket 或 Googles Cloud Source Repository 上的版本控制 repo
*   至少一个你手动部署的 Firebase 项目([此处引导](https://firebase.google.com/docs/hosting/deploying))
*   Google Cloud SDK 已安装([下载此处](https://cloud.google.com/sdk/install))

不管怎样，让我们继续吧！

# 自动测试每个分支和拉取请求

## 创建用于测试的配置文件

首先，我们将创建测试构建步骤。Google Cloud Build 使用一个构建配置文件(yaml 或 json，我更喜欢 json)和一个构建步骤列表。这些构建步骤将在您推送到 GitHub 的每个分支上运行。一些受支持的构建步骤是 npm、yarn、git 和 docker。[点击此处查看完整列表](https://github.com/GoogleCloudPlatform/cloud-builders)。

大多数用 Node 构建的 web 项目使用相同的标准命令。例如，这些是您在本地测试大多数 web 应用程序时会运行的命令。

```
npm install
npm test
```

我们希望将这些命令转化为构建步骤。创建一个文件`cloudbuild.json`并添加以下构建步骤:

很简单。如果您的任何步骤需要环境变量、额外的参数或其他定制的东西，Googles 关于构建配置文件的文档可以在[这里](https://cloud.google.com/cloud-build/docs/build-config)找到。

## 确保测试配置文件正常工作

在让 GitHub 触发我们的测试之前，我们将通过从命令行触发测试过程来确保我们的配置文件正在工作。您需要安装 Google Cloud SDK，并且想要登录。这里的安装步骤是[这里是](https://cloud.google.com/sdk/install)，登录方向是[这里是](https://cloud.google.com/sdk/docs/initializing)。一旦安装完毕，运行下面的命令，替换掉`[PROJECT_ID]`。你可以运行`gcloud projects list`来找到你的项目 ID。

```
gcloud builds submit --config=cloudbuild.json . --project=[PROJECT_ID]
```

> 如果您还没有为您的项目启用云构建，将会询问您是否要启用它。点击`y`并按下回车键。出于某种原因，我不得不这样做两次。

这将在云中运行您的构建过程，并在您的本地命令行中显示输出。去[https://console.cloud.google.com/cloud-build/builds](https://console.cloud.google.com/cloud-build/builds)看看建造历史等等。

## 支持 GitHub 集成

如果您使用 GitHub 进行版本控制，您可以添加 GitHub 集成，它将运行您的测试，以确定您是否在每个分支和拉请求上看到一个绿点或红点。

确保您将您的测试配置文件命名为`cloudbuild.json`或`cloudbuild.yaml`，因为这是集成将寻找的。提交并推送该文件。

然后，我们所要做的就是从 GitHub marketplace 添加 GitHub 集成，可以在这里找到。我们现在有绿色和红色的点！

# 自动化构建和部署

## 创建自定义 Firebase CLI 构建步骤

在我们最后的构建配置文件中，我们所有的构建步骤都只使用了`npm`。因为这是受支持的构建步骤之一，所以很容易使用。部署到 Firebase 需要 Firebase CLI，遗憾的是不支持，这意味着我们需要创建一个定制的构建步骤。

定制构建步骤是一个 docker 映像，它存储在特定于您的项目的云工作区中。这将使我们受益，因为这意味着构建过程不需要在每次构建时下载和安装 Firebase CLI，因为它将重用它。

要在您的项目云工作区中创建容器映像，创建一个名为`firebase-build-step`的文件夹并添加以下文件。不要担心替换这里的`$PROJECT_ID`，云构建会为你做到这一点。

接下来，运行以下命令，替换`[PROJECT_ID]`。

```
gcloud builds submit --config=./firebase-build-step/cloudbuild.yaml ./firebase-build-step/ --project=[PROJECT_ID]
```

好了，我们刚刚在我们的项目云工作区中创建了一个 docker 映像，其中包含了一个`firebase-tools` npm 包的安装。现在我们可以在构建步骤中使用这个包，而不用每次都安装它。

如果您正在为多个项目这样做(我这样做是因为我想要测试版和生产项目)，现在是一个好时机，可以返回并更改命令中的项目 id，然后再次运行它。

完成这一步后，如果您愿意，您可以删除`firebase-build-step`文件夹及其内容。因为 docker 容器已经在您的项目中创建，所以在本指南的其余部分中不会用到它们，但是我不推荐使用它们，因为当您添加另一个项目或定期更新 firebase-tools 的版本时，拥有它们是很好的。

## 创建用于构建和部署的配置文件

现在我们想创建一个配置文件来进行构建和部署。与测试步骤一样，我们从本地运行的命令开始。

```
npm install
npm test
npm build
firebase deploy --project [PROJECT_ID] --only hosting
```

构建步骤如下所示:

我把这个文件叫做`cloudbuild.deploy.json`。这里的最后一个构建步骤使用了我们创建的定制构建步骤。

`$PROJECT_ID`由云构建自动替换。`$_FIREBASE_DEPLOY_TOKEN`另一方面，它是一个定制的替代变量，正如开头的下划线所示。如果我们从命令行触发构建，我们必须将这个令牌作为参数传入。

当从我们的本地环境部署时，我们不需要这个令牌，因为 Firebase CLI 登录到您的 Google 帐户。但是 Cloud Build 在部署的时候，是不会登录的。我们在本地使用的`firebase login`命令不能用作构建步骤，因为它会打开你的浏览器让你登录，而云构建不理解这一点。

## 如何获得 Firebase 部署令牌

要获得 Firebase 部署令牌，需要在本地安装 Firebase CLI 并运行`firebase login:ci`。登录。授予访问 CLI 的权限。将令牌保存在某个地方。绝对不要犯。

如果您需要撤销对令牌的访问，请使用以下命令，替换`[FIREBASE_DEPLOY_TOKEN]`。

```
firebase logout --token [FIREBASE_DEPLOY_TOKEN]
```

## 确保构建配置文件正常工作

我们将通过从命令行触发构建过程，在提交之前测试构建配置文件。运行以下命令，替换`[PROJECT_ID]`和`[FIREBASE_DEPLOY_TOKEN]`。

```
gcloud builds submit --config=cloudbuild.deploy.json . --project=[PROJECT_ID] --substitutions=_FIREBASE_DEPLOY_TOKEN="[FIREBASE_DEPLOY_TOKEN]"
```

再次参观 https://console.cloud.google.com/cloud-build/builds 的建筑历史。如果此时一切运行顺利，我们可以继续创建触发器。

## 从代码更改中触发构建

到目前为止，我们已经完成了很多工作，但是所有这些工作的重点是让您的部署自动运行！让我们现在设置它。转到[https://console.cloud.google.com/cloud-build/triggers](https://console.cloud.google.com/cloud-build/triggers)并创建一个触发器。步骤 1、2 和 3 应该非常明显。到达触发设置时停止。

1.  如果您想在每次对 master 进行更改时部署这个项目，请将“branch (regex)”更改为`master`
2.  如果您想在标记一个新版本时部署这个项目，那么将“触发器类型”更改为“标记”，并将“标记(正则表达式)”更改为`v*`(如果您使用语义版本控制，否则只需`*`)
3.  将“构建配置”更改为“cloudbuild.yaml”
4.  将“cloudbuild.yaml location”更改为`cloudbuild.deploy.json`
5.  添加一个名为`_FIREBASE_DEPLOY_TOKEN`的替代变量，并将之前的令牌粘贴到值中。

点击保存后，您的构建将自动发生😄。如果您有另一个项目，请使用不同的触发器设置执行相同的步骤。

# 结论

所以我们自动化了测试过程，并将其与 GitHub 集成。我们创建了自己的构建步骤，因此可以在构建步骤中使用 Firebase CLI。最后，我们创建了用于构建和部署的构建步骤，并设置它在我们控制或标记发布时运行。对于任何手动部署的 web 应用程序来说，这应该是一个很大的升级。让我知道你是否在任何步骤上卡住了，或者你是否因为你的项目有点不同而在挣扎。

这是我的第一篇媒体文章，我绝对欢迎任何反馈或对话，感谢阅读！

***有用链接***

[创建基本构建配置文件](https://cloud.google.com/cloud-build/docs/configuring-builds/create-basic-configuration)

[从云构建部署到 Firebase】](https://cloud.google.com/cloud-build/docs/configuring-builds/build-test-deploy-artifacts#deploying_artifacts)