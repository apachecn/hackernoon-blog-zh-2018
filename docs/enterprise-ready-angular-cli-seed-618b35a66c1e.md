# 企业就绪 Angular-CLI 种子

> 原文：<https://medium.com/hackernoon/enterprise-ready-angular-cli-seed-618b35a66c1e>

在过去的一年里，我帮助我的团队开发并交付了许多 Angular 应用程序。这包括桌面网络、RWD、混合网络等。在这段时间里，我开始感觉到需要为团队准备一个现成的初学者工具包——包括更多的企业特性。Angular CLI 是设置 Angular 应用程序的绝佳入门套件。它给了你一个基础，但这就是它留给你的！因此，我决定使用 Angular-CLI 作为基础，为 Angular 构建一个企业级 seed 应用程序。

![](img/67040fef59a42725379c9f82ac19f75d.png)

Photo by [Helloquence](https://unsplash.com/photos/5fNmWej4tAA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/enterprise?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 目标

如果有好的目标，每个项目都有可能成功。该项目的目标是:

*   添加一些库/定制实现来提供企业级的特性——繁忙指示器、模板中的授权检查和功能(当然还有路由守卫)
*   默认情况下提供路由的延迟加载，包括主页，从而将主包的包大小减少到绝对最小。
*   每次迭代后，检查包的大小。丢弃任何证明会增加过多负载的库。
*   添加通知-在页面中以及浮动
*   添加表单验证示例
*   细长滚动条
*   条件日志记录(自动禁用生产中的某些日志)
*   动画片
*   更多…
*   让它在麻省理工学院许可下开源！

我计划在这里写一系列基于这个种子如何形成的最佳实践(在代码级别)。

# TL；DR；我的代码在哪里？

[这里的](https://github.com/Infosys/enterprise-angular-seed)是我开发的基于 Angular CLI 的企业应用 Seed 的代码！记住🌟github 上的书签项目！

但是我强烈建议你通读整篇文章，理解种子的各种代码片段以及它们存在的原因。

这段代码实现了许多最佳实践，因此我决定将这篇文章分成多篇，每次包含 3-4 个最佳实践。

# 架构上重要的最佳实践—第一部分

## 1.默认为 SCSS

SCSS 提供了比普通 CSS 更多的优势，例如变量支持(有助于全局变化，主题化等)，通过调整树结构，混合，代码生成等紧凑的风格定义。几乎所有成熟的样式库和组件库都使用 SCSS 作为它们的样式定义引擎。任何企业应用程序都应该使用 SCSS。

默认情况下，Angular CLI `new`命令使用 css 作为样式选项来生成应用程序。虽然这对于一些幼稚的实现来说是可以的，但是任何使用 bootstrap 或任何其他 css 系统的应用程序都应该知道，通过添加整个分发 css 文件，他们添加了太多的 CSS。Bootstrap4 启用了 OOTB sass。引导 v3 有完全支持的`bootstrap-sass` npm 包。

因此，我们的应用程序支持 SCSS，并且使用 scss 只包含任何 CSS 库的必需部分，这一点很重要。

如果您要运行`new`，请添加额外选项`--style scss`。就是这样！现在新生成的代码将默认为 SCSS。您将生成的任何新组件也将具有。scss 作为样式的默认扩展名。如果你已经生成了应用程序，你仍然可以在`defaults`部分的`.angular-cli.json`文件中为将来的组件生成做修改。

**Pro 提示**:当你这么做的时候——如果你不打算写测试，或者如果你不想让 Angular CLI 开始运行安装(如果你已经安装了 Yarn，使用 Yarn)，你可能还想添加`--skip-test`和`--skip-install`选项。`--routing`对于企业应用程序，也强烈推荐使用该选项。

## 2.CSS 重置(正常化)

使用良好的样式重置非常重要，这样 html 组件的大多数基本呈现在所有浏览器中都是一样的。如果你使用 bootstrap/Zurb foundation——它们内置了重置功能。如果你是从零开始，重置是必须的。你可以使用其中一个[normalize . CSS](https://github.com/necolas/normalize.css)+[sanitize . CSS](https://github.com/jonathantneal/sanitize.css)/[Meyer web reset](https://meyerweb.com/eric/tools/css/reset/reset.css)/[simple CSS reset](https://github.com/mirego/simple-css-reset)/[html 5 样板重置](https://github.com/h5bp/html5-boilerplate/blob/master/src/css/main.css)。

对于这个种子，我使用了[的](https://github.com/Infosys/enterprise-angular-seed/blob/master/src/styles.scss#L2-L3) normalize.css + sanitize.css 选项。而 normalize 是一个很好重置。它不提供`box-sizing: border-box`和其他类似的默认值。这些是由 sanitize.css 添加的，sanitize . CSS 是 normalize.css 的合著者。

如果您计划使用 bootstrap —您可以删除此组合，因为这些东西由 bootstrap 处理。

## 3.现成的授权指令

所有企业应用程序的另一个关键要求是拥有授权支持。在筛选了多个库之后，我将选择范围缩小到了 [ngx-permissions](https://github.com/AlexKhymenko/ngx-permissions) 。

*   ngx-permissions 提供了 3 个指令来快速描述模板中的授权需求。
*   指令在 AOT 准备好了。
*   它还提供全面的路由引导，并在用户未被授权使用另一个路由时重定向到该路由。
*   可激活和可加载保护都受支持..因此，当用户没有正确的权限时，您可以禁止下载模块。

如何在代码中包含这一点？

`[package.json](https://github.com/Infosys/enterprise-angular-seed/blob/master/package.json)` — ( `yarn add ngx-permissions` )
`[src/app/app.module.ts](https://github.com/Infosys/enterprise-angular-seed/blob/9eed90dd0fc7644ae9ab4121c81bcb4c34517666/src/app/app.module.ts#L36)` —在进口中增加`NgxPermissionsModule.forRoot()`。重要提示:这必须在您的应用程序主模块中(在 Angular-CLI 生成的项目中通常是 app . module)
`[src/app/app.component.ts](https://github.com/Infosys/enterprise-angular-seed/blob/9eed90dd0fc7644ae9ab4121c81bcb4c34517666/src/app/app.component.ts#L27-L38)` —从后端服务调用/ OAuth2 重定向头等中检索权限集，并将它们推送到 ngx-permissions 的 PermissionService 和 RoleService 实例中。
`[src/app/<yourcompoent>/<your comp.html>](https://github.com/Infosys/enterprise-angular-seed/blob/9eed90dd0fc7644ae9ab4121c81bcb4c34517666/src/app/home/components/home/home.component.html#L12-L23)` —添加 ngxPermisssions *指令。例如

```
<div *ngxPermissionsOnly=”[‘listExperts’]”>
   <div>Viewable List of Experts here.. accessible to permission “listExperts”</div>
</div>
```

## 4.使用共享模块

如果您没有在 Angular 应用程序中创建模块；你现在应该停下来，首先[阅读](https://johnpapa.net/introducing-angular-modules-feature-modules-2/)关于[这样做的好处](/@cyrilletuzi/understanding-angular-modules-ngmodule-and-their-scopes-81e4ed6f7407)。其中一个重要的原因是应用程序其余部分的延迟加载。另一个是可重用性。

[共享模块](https://angular.io/docs/ts/latest/guide/ngmodule.html#!#shared-module)有助于提供可在多个应用模块间重用的公共服务和组件。我可以想象，任何企业应用都至少有一个共享模块。

看一看`[src/app/appcommon/appcommon.module.ts](https://github.com/Infosys/enterprise-angular-seed/blob/9eed90dd0fc7644ae9ab4121c81bcb4c34517666/src/app/appcommon/appcommon.module.ts)`了解如何编写公共模块..特别是，请参见`[exports](https://github.com/Infosys/enterprise-angular-seed/blob/9eed90dd0fc7644ae9ab4121c81bcb4c34517666/src/app/appcommon/appcommon.module.ts#L16)`属性，了解我们如何使共享模块代码对世界其他地方可访问。

出于内容长度的考虑，我就讲到这里。这篇文章的下一部分将详细阐述以下项目。github 上的代码已经有了许多这些项目所需的实现:

*   UX 改进——细滚动条
*   高生产率—灌注组件库
*   在应用程序的不同部分使用动画
*   动态日志级别设置来做有效的调试
*   懒惰加载一切，包括主页。
*   使用 HTTP_INTERCEPTORS 的通用 HTTP 头
*   使用 AppState 的全局共享状态
*   跟踪你的包裹大小
*   在 NPM 和纱线之间沉降(通过纱线或模具)
*   IE11 支持
*   json-server 和一定程度上的后端集成自由
*   对共享模块中的各种第三方模块调用 forRoot()调用的不利影响。
*   为什么在模板中使用异步管道(`| async`)很棒
*   PageNotFoundComponent
*   针对持久性的 LocalStorage / IndexedDB。

欢迎您通过代码评审、特性请求、PRs 和其他方式对代码做出贡献。

为了确保你能看到剩下的帖子，请点击我名字旁边的“关注”按钮，在 Medium 上关注我。

# 连接

请一如既往地在[媒体](/@dharapvj)、[推特](https://twitter.com/dharapvj)上鼓掌、评论、关注，保持联系，进行精彩的技术对话！