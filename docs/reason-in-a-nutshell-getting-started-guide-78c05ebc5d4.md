# 简而言之——入门指南

> 原文：<https://medium.com/hackernoon/reason-in-a-nutshell-getting-started-guide-78c05ebc5d4>

![](img/e43e4d228d6a91264001fdbd82d50cff.png)

本教程旨在提供一个全面的，但相对较短的理性介绍。

[原因](https://reasonml.github.io/)是一种建立在 [OCaml](https://ocaml.org/) 之上的编程语言。它提供了功能性和面向对象的特性，具有类型安全性，并关注性能。它是在[脸书](https://hackernoon.com/tagged/facebook)创建的。它的语法类似于 [JavaScript](https://hackernoon.com/tagged/javascript) 。其目的是使与 JavaScript 的互操作和 JavaScript 程序员的采用更容易。Reason 可以同时访问 JavaScript 和 OCaml 生态系统。OCaml 创建于 1996 年。它是一种带有推断类型的函数式编程语言。

原因网站包含一个[在线游乐场](https://reasonml.github.io/en/try.html)。它允许使用这种语言，看看生成的 JavaScript 是什么样子。它还可以从 OCaml 转换为 Reason。

# 为什么

1.  在 JavaScript 类型注释中，林挺或统一格式作为外部依赖提供，如 Flow、TypeScript、ESLint 或 appearlier。Reason 提供了这些现成的特性。这使得开发过程更加简化和方便。
2.  Reason 为 React with [ReasonReact](https://reasonml.github.io/reason-react/) 提供支持。它还支持现成的 JSX 语法(React 中使用的类似 HTML 的语法)。
3.  Reason 也有能力生成本地二进制文件。生成的代码是高性能的。没有虚拟机开销。它提供了一个简化部署过程的二进制文件。

# 它是如何工作的

Reason 编译成 OCaml 的抽象语法树。这使得理性成为一个透明的东西。OCaml 不能直接在浏览器中运行。AST 可以转换成各种目标。BuckleScript 可用于将 AST 编译成 JavaScript。它还提供了 OCaml 和 JavaScript 生态系统之间的互操作。

BuckleScript 非常快，可以生成可读的 JavaScript。它还提供了外部函数接口(FFI ),允许与现有的 JavaScript 库进行互操作。检查 [BuckleScript 基准](https://github.com/neonsquare/bucklescript-benchmark)。在脸书，Messanger 团队使用 BuckleScript，在谷歌，WebAssembly spec interpreter 使用 BuckleScript。点击此处查看 [Bucklescript 演示](http://bloomberg.github.io/bucklescript/js-demo/)。BuckleScript 是由张洪波创造的。

# 你好原因

我们将使用 BuckleScript 来生成一个 Reason 项目。该工具提供了称为`themes`的现成项目模板。

让我们从全球安装`bs-platform`开始:

```
npm install -g bs-platform
```

我们现在可以使用由`bs-platform`提供的`bsb`二进制来生成一个项目支架。我们将使用`basic-reason`模板从最基本的原因项目结构开始。

```
bsb -init reason-1 -theme basic-reason Making directory reason-1 
Symlink bs-platform in /Users/zaiste/code/reason-1
```

下面是通过 BuckleScript 从`basic-reason`模板生成的原因目录结构:

```
. 
├── README.md 
├── bsconfig.json 
├── lib 
├── node_modules 
├── package.json 
└── src 
└── Demo.re
```

`bsconfig.json`包含原因项目的 BuckleScript 配置。它允许通过`sources`指定要编译的文件，通过`bs-dependencies`指定 BuckleScript 依赖项，编译器的附加标志等等。

下一步是构建项目。这将采用 Reason 代码，并通过 BuckleScript 来生成 JavaScript。默认情况下，编译器会将 Node.js 作为目标。

```
npm run build > reason-1@0.1.0 build /Users/zaiste/code/reason-1 
> bsb -make-world ninja: Entering directory `lib/bs' 
[3/3] Building src/Demo.mlast.d 
[1/1] Building src/Demo-MyFirstReasonml.cmj
```

最后，我们可以通过对 BuckleScript 生成的文件使用`node`来运行我们的应用程序。

```
node src/Demo.bs.js Hello, BuckleScript and Reason!
```

# 语法 101

在这一节中，我将回顾我发现的奇特的、新的或不同的语法元素。

# 模块

按理说，文件是模块。JavaScript 或类似的编程语言中没有`require`或`import`语句。模块定义必须以模块名为前缀才能在外部工作。这个特性来自 OCaml。因此，您可以在文件系统中自由移动模块文件，而无需修改代码。

# 功能

使用`let`和`=>`定义功能。

```
let greet = name => 
  Js.log("Hello, " ++ name "!"); greet("Zaiste");
```

`++`操作符用于连接字符串。

函数的输入参数可以被标记。这使得函数调用更加明确:传入的值不再需要遵循函数定义中的参数顺序。在参数名前面加上`~`会使其带有标签。

```
let greet = (~name, ~location) => 
  Js.log("Hello, " ++ name "! You're in " ++ location); greet(~location="Vienna", ~name="Zaiste")
```

# 数据结构

## 变体

[变量](https://en.wikipedia.org/wiki/Tagged_union)是一种数据结构，它保存一组固定值中的一个值。这也称为标记或不相交的联合或代数数据类型。变体中的每种情况都必须大写。可选地，它可以接收参数。

```
type animal = 
| Dog 
| Cat 
| Bird;
```

## 记录

这是一项记录

```
let p = { 
  name: "Zaiste", 
  age: 13 
}
```

记录需要显式类型定义。

```
type person = { 
  name: string, 
  age: int 
};
```

在模块的范围内，类型将被继承:`p`绑定将被识别为`person`类型。在模块之外，您可以通过在类型前面加上文件名来引用它。

```
let p: Person.person = { 
  name: 'Sean', 
  age: 12 
};
```

按照惯例，为每种类型创建一个模块，并将该类型命名为`t`以避免重复，即`Person.t`而不是`Person.person`。

# 异步编程和承诺

通过 BuckleScript 有一个对承诺的内置支持，作为`JS.Promise`模块提供。下面是一个使用[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 进行 API 调用的例子:

```
Js.Promise.( 
  Fetch.fetch(endpoint) 
  |> then_(Fetch.Response.json) 
  |> then_(json => doSomethingOnResponse(json) |> resolve) 
)
```

你需要使用`then_`，因为`then`是 OCaml 中的保留字。

# 模式匹配

模式匹配是一种基于所提供值的形状的调度机制。按理说，模式匹配是用`switch`语句实现的。它可以用于变体类型或作为析构机制。

```
switch pet { 
| Dog => "woof" 
| Cat => "meow" 
| Bird => "chirp" 
};
```

我们可以使用模式匹配来解构列表:

```
let numbers = ["1", "2", "3", "4"]; 
switch numbers { 
| [] => "Empty" 
| [n1] => "Only one number: " ++ n1 
| [n1, n2] => "Only two numbers" 
| [n1, _, n3, ...rest] => "At least three numbers" 
};
```

或者，我们可以用它来销毁记录

```
let project = {
  name: "Huncwot", 
  size: 101101, 
  forks: 42, 
  deps: [{name: "axios"}, {name: "sqlite3"}]
} switch project { 
| {name: "Huncwot", deps} => "Matching by `name`" 
| {location, years: [{name: "axios"}, ...rest]} => "Matching by one of `deps`" 
| project => "Any other situation" }
```

# 可选值

`option()`是 Reason 中描述“可空”值的内置变量:

```
type option('a) = None | Some('a);
```

# 巴利安

*   `unit`表示“一无所有”
*   `unit => unit`是不接受任何输入参数且不返回任何值的函数的签名；主要用于回调函数

# 理智地反应

# 你好，反应

[React](https://reasonml.github.io/reason-react/)是一个用于创建 React 应用程序的 React 内置特性。

让我们使用 BuckleScript 及其`react`模板创建一个 ReasonReact 项目。

```
bsb -init reasonreact-1 -theme react
```

Reason 团队建议将这种方法用于搭建 ReasonReact 项目。也可以使用带有[原因脚本](https://github.com/reasonml-community/reason-scripts)模板的 yarn 来获得更完整的起点。

ReasonReact 提供两种类型的组件:`statelessComponent`和`reducerComponent`。与无状态组件相反，reducer 组件是有状态的，提供类似 Redux 的 Redux。

```
let s = ReasonReact.string let component = ReasonReact.statelessComponent("App"); let make = (~message, _children) => { 
  ...component, 
  render: _self => 
    <h1 class="header">(s(message))</h1> 
};
```

如前所述，`~`指定了一个带标签的自变量来自由排序函数的输入参数。绑定名称中的`_`告诉编译器该函数的主体中没有使用该参数。在`component`旁边的扩展操作符(`...`)意味着我们扩展了一个现有的组件。在这个例子中，我们还覆盖了`render`函数。

Reason 中的 JSX 比 React 中的更严格:我们需要用`ReasonReact.string()`显式包装字符串。为了方便起见，我创建了一个名为`s`的较短绑定，以便在 JSX 块中方便地使用它。

# 构建重要的 ReactReason 应用程序

让我们构建一个不仅仅显示预定义数据的 ReactReason 应用程序。我们将为趋势库创建一个 GitHub 查看器。目的是展示如何与外部 API 集成，如何管理状态以及如何使用 React 的生命周期方法。

出于这个例子的目的，我们将使用 [reason-scripts](https://github.com/reasonml-community/reason-scripts) 来引导我们的 reason 项目。

```
yarn create react-app reasonreact-github --scripts-version reason-scripts
```

安装依赖项:

```
cd reasonreact-github 
yarn
```

从以下内容开始:

```
yarn start
```

**存储库**是这个应用程序的核心概念。让我们首先定义一个类型来描述这个实体。我们将把它放在一个名为`Repo`的独立模块中。

```
type t = { 
  name: string, 
  size: int, 
  forks: int 
};
```

从现在开始，我们可以在应用程序中的任何 Reason 文件中使用`Repo.t`来引用这种类型，而不需要用到它。

# 管理状态

我们已经看到了无状态组件。现在让我们创建一个有状态的组件。在我们的上下文中，我们将使用`RepoList`组件管理从 GitHub 的 API 获取的趋势库列表。

让我们从定义由`RepoList`组件管理的状态类型开始。

```
type state = { 
  repos: list(Repo.t) 
};
```

然而，有一个问题。最初，在从 GitHub API 获取趋势库列表之前，`repos`是未定义的。但是原因类型系统不允许我们有未定义的值。我们可以用一个空列表来模拟初始状态，但是这不是最佳的。空列表也可能意味着我们获取趋势库的查询没有返回任何结果。

让我们使用 Reason 的可选值来处理这种情况。

```
type state = { 
  repos: option(list(Repo.t)) 
}
```

下一步是为该组件定义可能的操作。在 ReasonReact 中，动作被表示为变体。现在我们只有一个动作叫做`ReposFetched`。

```
type action = 
| ReposFetched(list(Repo.t));
```

为了在 ReasonReact 中创建有状态的组件，我们需要使用`reducerComponent()`函数。

```
let component = ReasonReact.reducerComponent("App");
```

这种组件允许定义一个描述状态如何响应动作而转换的缩减器。缩减器将一个动作和当前状态一起作为输入，并将新状态作为输出返回。减速器必须是纯函数。

```
reducer: (action, _prevState) => { 
  switch action { 
  | ReposFetched(repos) => ReasonReact.Update({repos: Some(repos)}) 
  } 
}
```

我们正在根据 reducer()方法中接收的参数进行模式匹配操作。模式匹配必须是详尽的。所有变量值必须匹配。`reducer`定义放在组件的`main`函数中。

为了完成组件的定义，让我们定义它的初始状态:

```
initialState: () => {  
  repos: Some([ {name: "Huncwot", size: 11011, forks: 42} ]) 
}
```

# 与 API 集成

我们将使用`[bs-fetch](https://github.com/reasonml-community/bs-fetch)`从外部 API 获取数据。它是一个 BuckleScript 库，充当 Fetch API 之上的一个薄层。一旦获取了数据，我们将使用`bs-json`来提取我们感兴趣的字段。

从安装`bs-fetch`和`bs-json`开始:

```
npm i bs-fetch @glennsl/bs-json
```

将它们添加到`bsconfig.json`中的`bs-dependencies`:

```
{ 
  "bs-dependencies": [ 
    ..., 
    "bs-fetch", 
    "@glennsl/bs-json" 
  ] 
}
```

我们将我们的`Repo`类型定义为一组三个字段:`name`、`size`和`forks`。一旦从 GitHub API 获取了有效载荷，我们就解析它以提取这三个字段。

```
let parse = json => Json.Decode.{ 
  name: json |> field("name", string), 
  size: json |> field("size", int), 
  forks: json |> field("forks", int), 
};
```

`field`是`Json.Decode`的一种方法。`Json.Decode.{ ... }`(注意圆点)打开`Json.Decode`模块。它的属性现在可以在这些花括号中使用，而不需要加上前缀`Json.Decode`。

既然 GitHub 在`items`下返回 repos，那么让我们定义另一个函数来获得这个列表。

```
let extract = (fields, json) => Json.Decode.(
  json |> at(fields, list(parse))
);
```

最后，我们可以发出请求，并通过解析函数传递返回的数据:

```
let list = () => Js.Promise.( 
  Fetch.fetch(endpoint) 
  |> then_(Fetch.Response.json) 
  |> then_(text => extract(["items"], text) |> resolve) 
);
```

# 反应生命周期方法

让我们使用`didMount`生命周期方法来触发从 GitHub API 获取存储库。

```
didMount: self => { 
  let handle = repos => self.send(ReposFetched(repos)); 
  Repo.list() 
  |> Js.Promise.then_(repos => { 
    handle(repos); 
    Js.Promise.resolve(); 
  }) 
  |> ignore; 
}
```

`handle`是将`ReposFetched`动作分派给减速器的方法。一旦承诺被解析，动作将把获取的存储库传送到缩减器。这将更新我们的状态。

# 翻译

因为我们区分了未初始化状态和库的空列表，所以处理初始的**正在加载**消息是简单的。

```
render: self => 
  <div> ( 
    switch self.state.repos { 
    | None => s("Loading repositories..."); 
    | Some([]) => s("Emtpy list") 
    | Some(repos) => <ul> ( 
      repos 
      |> List.map((repo: Repo.t) => <li> (s(repo.name)) </li>) 
      |> Array.of_list 
      |> ReasonReact.array ) 
    </ul> 
    } 
  ) 
  </div> 
};
```

# 错误处理

TBW

# CSS 中的类型

使用`[bs-css](https://github.com/SentiaAnalytics/bs-css)`的 CSS 类型。

```
yarn add bs-css "bs-dependencies": [ 
  ..., 
  "bs-css" 
] let style = 
  Css.( 
    { 
      "header": style([backgroundColor(rgba(111, 37, 35, 1.0)), display(Flex)]), 
      "title": style([color(white), fontSize(px(28)), fontWeight(Bold)]), 
    } 
  ); let make = _children => { 
  ...component, 
  render: _self => 
    <header className=style##header> 
      <h1 className=style##title> (s("This is title")) </h1> 
    </header> 
};
```

# 词汇

*   `rtop`是一个交互式命令行。
*   Merlin 是 OCaml 和 Reason 的自动补全服务文件。
*   `[@bs...]`FFI 的 Bucklescript 注释

# 额外资源

# TBD

```
module History = { 
  type h; 
  [@bs.send] external goBack : h => unit = ""; 
  [@bs.send] external goForward : h => unit = ""; 
  [@bs.send] external go : (h, ~jumps: int) => unit = "";  
  [@bs.get] external length : h => int = ""; 
};
```

BuckleScript 允许我们将原始 JavaScript 与原因代码混合使用。

```
[%bs.raw {|require('./app.css')|}];
```

*最初发表于*[*zaiste.net*](https://zaiste.net/reason_in_nutshell_getting_started_guide/)*。*