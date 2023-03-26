# Npm 全局作为本地依赖项

> 原文：<https://medium.com/hackernoon/avoiding-the-npm-global-flag-in-package-json-based-projects-5a1e7a50706>

为了方便起见，许多基于 npm 的开发工具[指导用户在全球范围内安装。如果该工具用于启动/创建一个项目，这是有意义的，但是许多这样的模块也用于一个现有的项目。这些应该作为本地(项目)依赖项安装，这有几个优点:](https://hackernoon.com/tagged/tools)

*   使用中的版本与其他依赖项一起管理，使团队保持一致。这对于用于发布的构建工具尤其重要。遵循这一实践可以提高构建的可再现性，从而使[部署](https://hackernoon.com/tagged/deployment)和故障排除更加容易和可靠。
*   新开发人员开始工作所需的步骤数量减少了，因为所有必要的工具都与其他项目依赖项一起安装。
*   一个模块的多个版本可以在同一台计算机上使用，例如，当处理多个使用该工具但不能同时升级的项目时。

这种做法的一个小障碍是本地依赖项不能直接执行，因为它们的二进制文件存在于项目文件夹中。一种解决方法是将项目`node_modules/.bin`文件夹添加到 PATH 中，但是不要这样做。首先，需要使用该命令的每个开发人员都需要这样做。

另一个选择是给 *package.json* 添加一个脚本来充当 runner。例如，要使`exp`在您的项目中作为命令可用，作为项目依赖项安装(使用`--dev`)并添加脚本条目作为您的模块的运行程序:

```
# in package.json:
"scripts": {
  "exp": "exp"
  ...
}
```

`exp`模块现在将在安装步骤中与其他模块一起安装，并且由于参数转发，开发人员可以使用 npm 运行任何子命令:

```
$ npm run exp -- build:ios
$ npm run exp -- build:status
$ npm run exp -- publish
```

如果使用 yarn，则不需要添加脚本条目。Yarn 自动搜索`node_modules/.bin`中的二进制文件，如果存在就使用它们。也不需要`--`进行参数转发:

```
$ yarn exp build:ios
$ yarn exp build:status
$ yarn exp publish
```

添加脚本条目作为常用命令的快捷方式可能仍然有用:

```
# in package.json:
"scripts": {
  "publish: "exp publish"
  ...
}# usage
$ npm run publish
$ yarn publish
```

正如[凯文·基普](https://medium.com/u/d79769cd010a?source=post_page-----5a1e7a50706--------------------------------)指出的，另一个选项现在是可用的， [npx](http://blog.npmjs.org/post/162869356040/introducing-npx-an-npm-package-runner) ，从 npm 5.2.0 开始，它与 npm 一起安装。用`$ npx`而不是`$ npm`调用命令将自动检查并运行本地依赖关系(如果存在的话):

```
$ npm install --save-dev exp
$ npx exp publish
```

就像 yarn 一样，本地依赖优于相同模块的全局版本。然而，Npx 走得更远，它自动安装调用的软件包，这些软件包既不是本地安装的，也不是全局安装的。在我看来，这个特性不可避免地会引起一些混乱、意外和错误，这是令人遗憾的。

我很高兴看到这种将所有东西都安装为本地依赖项的做法最终被社区所接受和支持。鉴于这些优势，我期待这成为标准做法。它需要一段时间才能流行起来，所以如果你是一个项目维护者，你的项目是指导用户全局安装，请考虑改变指令，使用通过 npx 或 yarn 调用的本地依赖。