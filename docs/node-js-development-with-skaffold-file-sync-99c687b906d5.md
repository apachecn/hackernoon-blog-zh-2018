# 使用 Skaffold 文件同步在 Kubernetes 上开发 Node.js

> 原文：<https://medium.com/hackernoon/node-js-development-with-skaffold-file-sync-99c687b906d5>

## Skaffold 团队最近宣布了一个令人兴奋的新特性，它允许在你的机器和开发容器之间同步文件，在支持像 Node.js 这样的解释语言方面向前迈进了一大步。

[ska fold](https://github.com/GoogleContainerTools/skaffold)是一个命令行工具，用于开发针对 Kubernetes 集群(本地 Minikube 或远程集群)的应用程序。Skaffold 在代码更改时处理映像的构建、推送和部署过程。直到今天，Skaffold(IMHO)还不太适合像 [Node.js](https://hackernoon.com/tagged/nodejs) 这样的解释型语言，因为这个过程固有的缓慢。在 0.16.0 版本中，Skaffold [支持](https://matt-rickard.com/fast-kubernetes-development/)一种混合方法，允许利用 Node.js 开发人员常用的自动重载机制(例如:`nodemon`):

*   当 js 文件改变时，Skaffold 将它与容器同步，应用程序由`nodemon`重启
*   当文件更改需要重新构建容器时(例如，对`package.json`的更改)，Skaffold 会进行完整的重新构建、推送和部署

这种混合方法非常适合一大类技术栈，如 Node.js、React、Angular、Python 等。

# 快速教程

代码在 [Skaffold](https://github.com/GoogleContainerTools/skaffold/tree/master/integration/examples/nodejs) `[master](https://github.com/GoogleContainerTools/skaffold/tree/master/integration/examples/nodejs)` [分支](https://github.com/GoogleContainerTools/skaffold/tree/master/integration/examples/nodejs)中可用。

## 安装斯卡福德

*   安装斯卡福德需要一个 Kubernetes 集群。一个很好的本地选择是 Minikube 或最近的(edge) [Docker](https://hackernoon.com/tagged/docker) for Mac 或 Docker for Windows。

## 创建一个节点。JS 应用程序

*   初始化`npm`:

```
npm init
```

*   将 Express web 框架添加到`package.json`:

```
npm install express nodemon --save
```

*   本地移除`node_modules`:

```
rm -rf node_modules
```

*   在`package.json`的`scripts`部分增加`nodemon`运行命令:

```
"scripts": {
    "dev": "nodemon index.js"
},
```

*   创造`index.js`:

index.js

## 把它归档

*   创建一个`Dockerfile`:

*   创建 Kubernetes pod 定义`k8s-pod.yaml`:

`k8s-pod.yaml`

如果您使用编写临时文件的编辑器(如`vim`)，您需要一个`.dockerignore`文件来确保临时文件不会触发容器构建:

[.dockerignore](https://gist.github.com/mfornasa/82ece60500ef30e4b17849ed7cff08c3#file-dockerignore)

## 斯卡福德构型

*   创建一个`skaffold.yaml`:

`deploy`部分指向 pod 定义，而`build`部分指向当前目录，即`Dockerfile`所在的目录。`sync`子句对`*.js`文件更改禁用完全重建/推送/部署，并启用这些文件到容器的同步过程。

## 跑步滑板

*   `skaffold dev`

该命令启动 Skaffold 文件监视器，构建映像并将其部署在集群上；它还向您显示应用程序日志，并为您处理端口转发。当您看到:

```
...
[node] Example app listening on port 3000!
```

您已经准备好访问应用程序:

```
$ curl localhost:3000
Hello World!
```

## 开发工作流程

对`index.js`进行一些修改。您将看到修改后的文件被同步到容器中，并且`nodemon`重新启动应用程序:

```
Syncing 1 files for gcr.io/k8s-skaffold/node-example:dirty-11d1880
Watching for changes every 1s...
[node] [nodemon] starting `node index.js`
[node] Example app listening on port 3000!$ curl localhost:3000
Hello World - changed!
```

**快速简单！**

如果您更改一个需要重新构建容器的文件(比如在`package.json`中添加一个依赖项),那么完整的构建/推送/部署机制将被触发:

```
Starting build...
Found [minikube] context, using local docker daemon.
Building [gcr.io/k8s-skaffold/node-example]...
Sending build context to Docker daemon  10.34MB
Step 1/5 : FROM node:8.12.0-alpine
 ---> df48b68da02a...Build complete in 8.236026621s...[node] Example app listening on port 3000!
```

需要的时间取决于你`package.json`的复杂程度。

## 未来的改进

即使改变`package.json`不经常发生，重建一个复杂的应用程序也可能是漫长的。需要一种机制来利用`npm`缓存来加速这个过程。

# 我的演讲

如果你想通过 Kubernetes 了解更多关于当地发展的信息，你可以在 2018 年全天 DevOps 大会上观看我的[演讲](https://m.fornasa.it/talk/alldaydevops2018/)。