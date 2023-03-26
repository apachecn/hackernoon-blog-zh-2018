# Angular Providers:如何注入第三方库？

> 原文：<https://medium.com/hackernoon/angular-providers-how-to-inject-3rd-party-library-af4a78722864>

![](img/29a3110b644ba5ef29015c23b985b4d0.png)

本文将描述使用一些没有 Angular [集成](https://hackernoon.com/tagged/integration)的库的常见场景。例如，我将使用 [**HashWords**](https://github.com/jjt/hashwords) 库，它将散列字符串转换为人类可读的随机单词。如果你懒得看一篇文章，可以查看 [**Github repo**](https://github.com/kobvel/ng-providers-common/tree/3rd-party-lib-injection) 的代码示例。

通过运行`$ npm install --save hashwords`安装库

将其导入到。angular-cli.json :

`*“scripts”*: [“../node_modules/hashwords/dist/hashwords.min.js”]`

通过最后一个动作，我们使它在**窗口**对象上可用。

现在，我们可以在`NgModule`的**提供商**属性中注册它

```
providers: [
    { provide: 'Hashwords', useValue: window['hashwords']() }
]
```

> ***提供*** *应用程序用* ***值*** `*window['hashwords']()*` *对任何注入调用****hash words****！*

下一步是将这个新创建的值注入到`app.component.ts`的构造函数中

**Hashwords** injection

使用`@Inject({ token: any })`装饰器，我们可以手动注入注册的依赖项。当`@[Inject](https://angular.io/api/core/Inject)()`不存在时，`[Injector](https://angular.io/api/core/Injector)`将使用参数的类型注释。这就是我们使用`constructor(apiService: ApiService)`的应用中实际发生的情况。

# 用 InjectionToken 改进示例

前面的例子是完全可行的。但是我们错过了 Angular + [TypeScript](https://hackernoon.com/tagged/typescript) 应用程序的出色特性——**type check**！当然，并不是你需要的每个库都有现成的类型定义。但是通过 **InjectionToken** 机制，我们可以为我们的第三方库定义一些基本接口，并为我们的应用程序提供我们将要使用的属性和方法的知识。

## #1 创建新文件，该文件将包含我们第三方提供商的配置

libs.providers.ts

*   LOC 3–8:用我们将在应用程序中使用的方法和属性描述我们库的接口；
*   LOC 10:创建一个定制的 **InjectionToken** ,带有我们之前创建的通用类型的接口；
*   LOC 11:创建一个 ValueProvider 对象，这样我们可以在 **NgModule** 中注册它

## #2 注册新创建的价值提供商

LOC 12: Adding **HashwordsProvider** to providers array

## #3 用组件中定义的接口类型注入我们的依赖

LOC 3:从`libs.providers.ts`导入所需的依赖项；

LOC 12:现在我们可以注入`Hashword` **InjectionToken，**并为 hashwords 变量定义一个类型，它将是 interface—`IHashwords`；

LOC 15，16:在这个动作之后，我们可以使用智能感知的`hashwords`方法！

# 结论

通过在 Angular 应用中注册**提供者**的灵活机制，我们可以轻松地包装第三方库，并在应用中使用它们，就像它们是 **Angular 库一样。**

有了注入令牌机制，我们甚至可以**定义自定义类型**，让我们的应用程序知道一个导入库的 API！

> *如果你想从马嘴中直接得到一个信息，请跟我上*[***Twitter***](https://twitter.com/Kobvel)**[***Medium***](/@kobvel)*账号！***