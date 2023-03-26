# 使用 React、Node.js、Express 和 Webpack 的全栈 Web 应用程序

> 原文：<https://medium.com/hackernoon/full-stack-web-application-using-react-node-js-express-and-webpack-97dbd5b9d708>

![](img/8933e4fa3854e49902a096a7f2866351.png)

[创建 React 应用](https://github.com/facebook/create-react-app)是一种快速开始 React 开发的方法，它不需要构建配置。但是它完全隐藏了构建配置，这使得它很难扩展。它还需要一些额外的工作来将其与现有的 Node.js/Express 后端应用程序集成。

在本指南中，我们将带着一个 [Node.js](https://nodejs.org/en/) 和 [Express](https://expressjs.com/) 后端，完成一个简单的全栈 React 应用程序的设置。客户端代码用 React 编写，后端 API 用 Express 编写。这个应用程序配置了 Airbnb 的 ESLint 规则，并通过漂亮的格式进行格式化。

这个应用程序的源代码可以在[这里](https://github.com/crsandeep/simple-react-full-stack)找到。

# 快速启动

```
# Clone the repository
git clone https://github.com/crsandeep/simple-react-full-stack# Go inside the directory
cd simple-react-full-stack# Install dependencies
yarn (or npm install)# Start development server
yarn dev (or npm run dev)# Build for production
yarn build (or npm run build)# Start production server
yarn start (or npm start)
```

## 发展模式

在开发模式下，我们将有 2 台服务器运行。前端代码将由 [webpack 开发服务器](https://webpack.js.org/configuration/dev-server/)提供，这有助于热实时重新加载。服务器端 Express 代码将由使用 [nodemon](https://nodemon.io/) 的节点服务器提供，这有助于在服务器端代码改变时自动重启服务器。

## 生产模式

在生产模式下，我们将只有 1 台服务器在运行。所有的客户端代码将使用 webpack 捆绑到静态文件中，并由 Node.js/Express 应用程序提供服务。

# 细节

## 文件夹结构

所有的源代码都在 **src** 目录下。在 src 中，有客户端和服务器目录。所有的前端代码(反应，css，js 和任何其他资产)将在客户端目录。后端 Node.js/Express 代码将在服务器目录中。

## 巴比伦式的城市

[Babel](https://babeljs.io/) 帮助我们用最新版本的 JavaScript 编写代码。如果一个环境本身不支持某些特性，Babel 将帮助我们把这些特性编译成一个受支持的版本。它还帮助我们将 JSX 转换成 Javascript。

[。babelrc 文件](https://babeljs.io/docs/usage/babelrc/)用于描述 Babel 所需的配置。下面是。我正在使用的 babelrc 文件。

*更新:自从 babel 7 最近发布以来，我一直在更新代码以反映变化*

```
{
    "presets": ["@babel/preset-env", "@babel/preset-react"],      
    "plugins": ["@babel/plugin-proposal-class-properties"]
}
```

巴别塔需要插件来完成转换。预设是 Babel 定义的插件的集合。预设 **@babel/preset-env** 允许使用 babel-preset-es2015、babel-preset-es2016 和 babel-preset-es2017，它会将它们转换为 ES5。preset**@ babel/preset-react**允许我们使用 JSX 语法，它会将 JSX 转换成 Javascript。

**@ babel/plugin-proposal-class-properties**插件转换静态类属性以及用属性初始化器语法声明的属性。

## 埃斯林特

ESLint 是一个可插拔和可配置的 linter 工具，用于识别和报告 JavaScript 中的模式。

. eslintrc.json 文件(也可以用 Javascript 或 YAML 编写配置)用于描述 ESLint 所需的配置。下面是我正在使用的. eslintrc.json 文件。

```
{
  "parser": "babel-eslint",
  "extends": ["airbnb"],
  "env": {
    "browser": true,
    "node": true
  },
  "rules": {
    "no-console": "off",
    "comma-dangle": "off",
    "react/jsx-filename-extension": "off"
  }
}
```

[我正在使用 Airbnb 的 JavaScript 风格指南](https://github.com/airbnb/javascript)，它被世界上许多 Javascript 开发者所使用。因为我们要编写客户端(浏览器)和服务器端(Node.js)代码，所以我将 **env** 设置为 browser 和 Node。或者，我们可以覆盖 Airbnb 的配置来满足我们的需求。我已经关闭了 [**no-console**](https://eslint.org/docs/rules/no-console) ， [**逗号【**】和](https://eslint.org/docs/rules/comma-dangle)[**react/jsx-filename-extension**](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-filename-extension.md)规则。

## 网络包

Webpack 是一个模块捆绑器。它的主要目的是捆绑 JavaScript 文件，以便在浏览器中使用。

[webpack.config.js](https://webpack.js.org/configuration/) 文件用于描述 webpack 需要的配置。下面是我正在使用的 webpack.config.js 文件。

```
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const CleanWebpackPlugin = require("clean-webpack-plugin");const outputDirectory = "dist";module.exports = {
  entry: ['babel-polyfill', './src/client/index.js'],
  output: {
    path: path.join(__dirname, outputDirectory),
    filename: "bundle.js"
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader"]
      }
    ]
  },
  devServer: {
    port: 3000,
    open: true,
    proxy: {
      "/api": "http://localhost:8080"
    }
  },
  plugins: [
    new CleanWebpackPlugin([outputDirectory]),
    new HtmlWebpackPlugin({
      template: "./public/index.html",
      favicon: "./public/favicon.ico"
    })
  ]
};
```

1.  **条目:**。/src/client/index.js 是应用程序开始执行和 webpack 开始捆绑的地方。
    **注:**增加 babel-polyfill 支持 async/await。在这里阅读更多。
2.  **输出路径和文件名:**捆绑输出的目标目录和文件名
3.  **模块加载器:**模块加载器是应用于模块源代码的转换。我们将所有的 js 文件通过[的 babel-loader](https://github.com/babel/babel-loader) 来将 JSX 转换成 Javascript。css 文件通过 [css 加载器](https://github.com/webpack-contrib/css-loader)和[样式加载器](https://github.com/webpack-contrib/style-loader)来加载和捆绑 CSS 文件。
4.  **开发服务器:**web pack-Dev-Server 的配置将在下一节中介绍。
5.  **插件:**[clean-webpack-plugin](https://github.com/johnagan/clean-webpack-plugin)是一个 web pack 插件，用于在构建之前删除构建文件夹。 [html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin) 简化 html 文件的创建，为您的 webpack 包提供服务。它加载模板(public/index.html)并注入输出包。

## Webpack 开发服务器

[Webpack 开发服务器](https://webpack.js.org/configuration/dev-server/)与 Webpack 一起使用。它提供了一个开发服务器，为客户端代码提供实时重载。这应该仅用于开发。

webpack.config.js 的 devServer 部分包含运行 webpack-dev-server 所需的配置，如下所示。

```
devServer: {
    port: 3000,
    open: true,
    proxy: {
        "/api": "http://localhost:8080"
    }
}
```

[**端口**](https://webpack.js.org/configuration/dev-server/#devserver-port) 指定 Webpack dev 服务器监听这个特定的端口(本例中为 3000)。当 [**打开**](https://webpack.js.org/configuration/dev-server/#devserver-open) 设置为 true 时，启动时会自动打开主页。当我们有一个单独的 API 后端开发服务器，并且我们想在同一个域上发送 API 请求时，代理 URL 会很有用。在我们的例子中，我们有一个 Node.js/Express 后端，我们希望将 API 请求发送到这个后端。

## Nodemon

Nodemon 是一个实用程序，它将监视服务器源代码中的任何更改，并自动重启服务器。这仅用于开发。

json 文件用于描述 nodemon 的配置。下面是我正在使用的 nodemon.json 文件。

```
{
  "watch": ["src/server/"]
}
```

这里，我们告诉 nodemon 监视服务器端代码所在的 src/server 目录中的文件。每当 src/server 目录下的文件被修改时，Nodemon 将重新启动节点服务器。

## 表达

Express 是 Node.js 的 web 应用程序框架，用于构建我们的后端 API。

src/server/index.js 是服务器应用程序的入口点。下面是 src/server/index.js 文件

```
const express = require("express");
const os = require("os");const app = express();app.use(express.static("dist"));
app.get("/api/getUsername", (req, res) =>
  res.send({ username: os.userInfo().username })
);
app.listen(8080, () => console.log("Listening on port 8080!"));
```

这将启动一个服务器，并在端口 8080 上监听连接。对于对 URL (/api/getUsername)的请求，应用程序使用`{username: <username>}`进行响应。它还被配置为服务于来自 **dist** 目录的静态文件。

## 同时地

[并发](https://github.com/kimmobrunfeldt/concurrently)用于并发运行多个命令。我用它在开发环境中同时运行 webpack dev 服务器和后端节点服务器。下面是使用的 npm/yarn 脚本命令。

```
"client": "webpack-dev-server --mode development --devtool inline-source-map --hot",
"server": "nodemon src/server/index.js",
"dev": "concurrently \"npm run server\" \"npm run client\""
```

## VSCode + ESLint +更漂亮

[VSCode](https://code.visualstudio.com/) 是一个轻量级但功能强大的源代码编辑器。ESLint 负责代码质量。更漂亮的负责所有的格式。

**安装指南**

1.  安装 [VSCode](https://code.visualstudio.com/)
2.  安装 [ESLint 扩展](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
3.  安装[漂亮的加长件](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
4.  修改 VSCode 用户设置以添加以下配置

```
"eslint.alwaysShowStatus": true,"eslint.autoFixOnSave": true,"editor.formatOnSave": true,"prettier.eslintIntegration": true
```

上面，我们修改了编辑器配置。或者，这可以按照本文中的[在项目级别进行配置。](/@netczuk/your-last-eslint-config-9e35bace2f99)

这个应用程序的源代码可以在[这里](https://github.com/crsandeep/simple-react-full-stack)找到。