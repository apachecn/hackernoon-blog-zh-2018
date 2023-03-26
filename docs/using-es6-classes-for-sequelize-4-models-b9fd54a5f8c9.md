# 将 ES6 类用于 Sequelize 4 模型

> 原文：<https://medium.com/hackernoon/using-es6-classes-for-sequelize-4-models-b9fd54a5f8c9>

![](img/715aac7bd0e198c9ec6a9f7d244823a6.png)

Photo by Eugene Lim on Unsplash

ES2015 或 ES6 规范引入了`class`到 [JavaScript](https://hackernoon.com/tagged/javascript) 。像 React 这样的库从`React.createClass`发展到`class MyComponent extends React.Component`，ie 从滚动自己的构造器发展到利用内置语言来传达程序员的意图。

对于一个 [Node.js](https://hackernoon.com/tagged/nodejs) web 应用程序的持久层，我想到了一些数据库，比如 [MongoDB](https://www.mongodb.com/) (可能与[mongose](http://mongoosejs.com/)配对)…