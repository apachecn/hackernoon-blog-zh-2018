# 与 React 和布尔玛一起建立网站

> 原文：<https://medium.com/hackernoon/building-a-website-with-react-and-bulma-d655214bff2a>

## React 博客系列:第一部分

这篇文章是关于用 React 创建博客的每周系列文章的第一部分，也是本系列其他文章的基础。

## React 博客系列

**第一部分:**用 React 和布尔玛
[**建立一个网站第二部分:**用 React 和 Contentful 建立一个博客](/@aaron.klaser/building-a-blog-with-react-and-contentful-fd538f68f6fb)
[**第三部分:**将你的媒体提要导入 React](/@aaron.klaser/import-your-medium-feed-into-react-ceadbaf785c7)
[**第四部分:**向 React 博客添加一个 Redux](/@aaron.klaser/adding-redux-to-a-react-blog-97f5fea606c2)
[**第五部分:**用 Redux Sagas 替换 Redux Thunks](https://hackernoon.com/replacing-redux-thunks-with-redux-sagas-4aa306854925)

# 设置

所以让我们安装**反应**

```
npm install -g create-react-app aaronklaser
cd aaronklaser
npm start
```

接下来，让我们安装一些我们知道会需要的东西:

反应头盔
布尔玛
布尔玛扩展
瞬间
样式组件

```
npm install bulma bulma-extensions moment react-helmet styled-components --save
```

## 安装程序对使用 Sass 做出反应

这可能不是完全必要的，因为我们将主要使用样式化的组件，但这将使导入布尔玛更加容易。

现在，对于棘手的部分，我们需要设置 Sass，但我不想从创建-反应-应用程序弹出。我跟着这个[教程](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-a-css-preprocessor-sass-less-etc)。

```
npm install --save node-sass-chokidar
```

这将创造。我们的 css 文件。scss 文件，我对它不是很感兴趣，但是现在让我们开始吧。我们希望忽略 git 中的 css 文件，所以将`src/**/*.css`添加到我们的。gitignore 文件。

安装 npm-run-all，这样我们就可以在 npm start 中包含我们的 scss 构建

```
npm install --save npm-run-all
```

在 npm 脚本的 package.json 中，添加 build-css 和 watch-css、build-js 和 watch-js，修改 build 并开始使用我们所有的新 scss 内容。

```
"scripts": {
  "build-css": "node-sass-chokidar --include-path ./src --include-path ./node_modules src/ -o src/",
  "watch-css": "npm run build-css && node-sass-chokidar --include-path ./src --include-path ./node_modules src/ -o src/ --watch --recursive",
  "start-js": "react-scripts start",
  "start": "npm-run-all -p watch-css start-js",
  "build-js": "react-scripts build",
  "build": "npm-run-all build-css build-js",  
  "test": "react-scripts test --env=jsdom",
  "eject": "react-scripts eject"
}
```

注意:在 build-css 和 watch-css 中都包含`--include-path ./src --include-path ./node-modules`是很重要的。这允许我们的代码在节点模块文件夹中找到像布尔玛这样的库。

## 添加布尔玛和布尔玛扩展

我们将在索引组件中包括布尔玛和任何布尔玛配置。首先将`index.css`重命名为`index.scss`，然后使用`npm start`重启你的应用

如果什么都没有发生，并且您的项目现在包括 index.scss **和** index.css，那么这意味着一切都正常工作。

接下来，在 index.scss 中导入 bulma。

```
@import 'bulma/bulma';
@import 'bulma-extensions/extensions';
```

但是，我要做一些额外的步骤，包括一个额外的颜色，因为布尔玛不包括紫色在其默认的颜色，我想要紫色。

```
*// 1\. Import the initial variables* @import "bulma/sass/utilities/initial-variables";
@import "bulma/sass/utilities/functions";*// 2\. Setup your Custom Colors* $purple: hsl(275,87%,45%);
$purple-invert: findColorInvert($purple);*// 3\. Add new color variables to the color map.* @import "bulma/sass/utilities/derived-variables.sass";$addColors: (
  "purple":($purple, $purple-invert),
);$colors: map-merge($colors, $addColors);@import 'bulma/bulma';
@import 'bulma-extensions/extensions';
```

一切都应该启动了，你的标准 create-react-app 主页上的字体应该看起来不同了。

现在，我们已经完成了所有的设置，让我们使用布尔玛来设置一些基本的布局项目。

# 基本布局和页眉

让我花几分钟时间来谈谈我的文件结构，因为它与您可能习惯看到的略有不同。

## 分形文件结构

我第一次发现[分形文件结构的想法是在 Hackernoon](https://hackernoon.com/fractal-a-react-app-structure-for-infinite-scale-4dab943092af) 的一篇文章上。当我第一次读这篇文章的时候，我的想法不是很清楚，但是有一件事突然出现在我眼前，我想我明白了。我可能完全错了，但尽管如此，在过去的几个月里，我的方法一直很有效。这是 ReDucks 模式的一种扩展，除了用文件创建一种键值或父子对象结构。

```
src
|- index.js
|- App.js
|- app
   |- Layout.js
   |- layout
      |- Header.js
      |- Content.js
      |- Footer.js
|- User.js
|- user
   |- Profile.js
   |- PasswordReset.js
   |- Address.js
|- components
   |- SharedCompOne.js
   |- SharedCompTwo.js
```

所以你可以看到我们开始按模块或特性对代码进行分组，但是我们不会为每个组件创建十亿个文件夹。相反，我们将组件保存在它们的父文件夹中，对每个组件名称使用**Pascal case**，对组件子文件夹使用**camel case**。这里唯一真正的例外是 App.js 在技术上是 index 的子代，但我不确定这算不算🤔。

这样做的好处是，我们可以清楚地看到，页眉、内容和页脚都是应用程序组件中使用的布局组件中使用的组件。如果文件结构像这样更扁平:

```
/* This is NOT fractal */src
|- index.js
|- App.js
|- layout
   |- Layout.js
   |- Header.js
   |- Content.js
   |- Footer.js
```

然后，我们没有任何真正的迹象表明组件的层次结构，或者 Layout.js 甚至在哪些组件上使用，因为分形模式向您显示了谁在哪里使用了哪些组件。这是一个清晰的父子结构，布局甚至可以包含一个共享文件夹，用于只在布局子元素中共享的组件。

还记得我之前提到的键值结构吗？正是这个想法让这一切在我脑海中闪现。如果你举我的第一个例子，你会这样想:

```
src: {
  Index.js,
  App.js: {
    Layout.js: {
      Header.js
      Content.js
      Footer.js
    }
  }
  User.js: {
    Profile.js
    PasswordReset.js
    Address.js
  }
  components: {
    SharedCompOne.js
    SharedCompTwo.js
  }
}
```

我们的网站开始看起来像 JSON 数据…有点像，但是你明白了。“密钥”始终是一个组件。js 文件。“value”是一个带有其关键字名称的 always 文件夹，它包含所有子组件。

这真正开始发光的地方是在你的商店，我把它和我的表示层(app)分开。我们现在可以开始像构建数据结构一样构建我们的存储。这对 NoSQL 来说真的很好。

稍后我们将对此进行更深入的探讨。

## 让我们从应用程序开始

1.  在根目录下创建一个名为 app 的文件夹，并在其中创建一个名为 Layout.js 的文件
2.  在 app 文件夹中，创建名为 layout 的文件夹
3.  在布局文件夹中，创建 4 个文件:Site.js、Header.js、Content.js 和 Footer.js

**App.js** 会调用布局组件，之后会包含我们的 Redux Store。

**Layout.js** 将包含 **Site.js** 和 **Content.js** ，它们只是站点和内容的*样式组件*包装器，这样我们就可以正确设置 flexbox，以及 **Header.js** 和 **Footer.js** 组件。

为了我们双方的方便，这里把这 5 个文件放在一个单一的要点中。

# 设置到某些页面的传送

```
npm install react-router-dom
```

我们正在建立一个简单的网站/博客，所以我们将使用 BrowserRouter。你可以探索其他的选择，但这不是讨论的主题；)

在`src/index.js`中，我们的应用程序的基础是用路由器包装应用程序。

```
import ReactDOM from 'react-dom'
import registerServiceWorker from './registerServiceWorker'
import { BrowserRouter as Router } from 'react-router-dom'
import App from './App'
import './index.css'ReactDOM.render((
  <Router>
    <App />
  </Router>
), document.getElementById('root'))registerServiceWorker()
```

然后在应用程序文件夹中，让我们创建两个简单的页面`Home.js`和`Blog.js`

```
import React from 'react'const Home = () => (
  <p>This is the Home Page</p>
)
export default Home
```

和

```
import React from 'react'const Blog = () => (
  <p>This is the Blog Page</p>
)
export default Blog
```

现在我们也有一些页面要路由，让我们设置一个路由器组件。现在，我们不打算尝试用它做任何新奇的事情，只是简单、直接的开关和路由。

在我们的布局文件夹中添加一个名为`Router.js`的新文件。这个路由器将存在于我们的`Layout.js`文件的**内容**中。这将容纳我们的基本顶层页面。

```
<Content>
  <Router />
</Content>
```

可以在页面中创建附加的路由器组件，以添加更深层次的嵌套路由。我们未来的命名惯例将是它们的父组件名称和路由器。比如 BlogRouter.js 或者 HomeRoute.js. **还记得**吗，它们会生活在一个叫 blog 的文件夹里。

在我们的路由器组件中，我们需要在交换机组件及其内部，使用路由组件列出我们的路由。

```
import React from 'react'
import { Switch, Route } from 'react-router-dom'
import Home from './../Home'
import Blog from './../Blog'const Router = () => (
  <Switch>
    <Route exact path='/' component={Home}/>
    <Route path='/blog' component={Blog}/>
  </Switch>
)export default Router
```

最后，让我们设置我们的标题来调用这些路线。

现在，让我们只对我们有路由的两个页面进行更改。我们将修改我们在博客和图标标题导航项目上的`<a>`。

```
<NavLink
  className="navbar-item"
  to="/"
  activeClassName="is-active"
>
  <img 
    style={{
      borderTopLeftRadius: '50%',
      borderTopRightRadius: '50%',
      borderBottomLeftRadius: '50%',
      borderBottomRightRadius: '50%',
      marginRight: 15
    }}
    src="https://media-exp2.licdn.com/mpr/mpr/shrinknp_400_400/AAEAAQAAAAAAAAU3AAAAJGE1MzYxNzYzLTE1NTUtNDEyYi04MzRjLTgzZjNkOGU0MGIzNg.jpg"
    width="30px" 
    alt="" 
  />
  <span>AaronKlaser.com</span>
</NavLink>
```

和

```
<NavLink
  className="navbar-item"
  to="/blog"
  activeClassName="is-active"
>
  <span className="icon has-text-primary" style={{ marginRight: 5 }}
    <i className="fas fa-code"></i>
  </span>
  Code Blog
</NavLink>
```

请注意，我们添加了`activeClassName=”is-active”`这是因为它是布尔玛导航活动类。NavLink 很酷的一点是它总是检查你在哪个页面上，如果你的路线与那个`NavLink to`匹配，它会自动添加**处于活动状态。如果你不想添加一个活动类，只需要简单的链接组件。**

# 结论

这是应用程序的基本框架。

回顾

*   使用 create-react-app 设置 React 站点
*   设置 React 以构建 SassI
*   安装布尔玛
*   我们学习了分形文件结构
*   创建了站点布局
*   设置路由
*   添加了一些要路由到的页面

> **下一个**——[用反应和满足建立一个博客](/@aaron.klaser/building-a-blog-with-react-and-contentful-fd538f68f6fb)

更多即将推出！我会相应地更新这个帖子。