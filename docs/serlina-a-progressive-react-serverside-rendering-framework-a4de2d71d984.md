# Serlina:一个渐进式 React 服务器端渲染框架

> 原文：<https://medium.com/hackernoon/serlina-a-progressive-react-serverside-rendering-framework-a4de2d71d984>

由于 [Next.js](https://github.com/zeit/next.js/) 是一个轻量级且体验良好的服务器端渲染[框架](https://hackernoon.com/tagged/framework)，我喜欢在我的项目中使用它。它节省了我处理样板代码[的时间。我写了 React 代码，它就工作了。](https://hackernoon.com/tagged/code)

但是当我想在一个运行它自己的服务器的项目上使用它时，发生了一些事情。当调用`app.render`时，Next.js 完全控制 http 上下文，而[我们的服务器对上下文](https://github.com/eggjs/egg/issues/328#issuecomment-321962783)有一些影响。

我读了 Next.js 的代码，试图找到类似于`nextjs/core`的东西，但是没有。

所以我决定做一个不运行服务器的服务器端渲染框架。它只进行编译，把你的页面转换成字符串。并让您自己的服务器将字符串呈现给客户端。并像 Next.js 一样保持良好的体验。

这就是[瑟琳娜](https://github.com/djyde/serlina)是什么。让我们看看最简单的 Serlina 应用程序是怎样的:

```
npm i serlina react react-dom --save
```

创建如下所示的文件夹结构:

```
├── index.js # Your server
├── page # Your React pages
│   └── page1.js
```

您的服务器:

```
// index.js

const { Serlina } = require('serlina')
const path = require('path')

const http = require('http')

const serlina = new Serlina({
  baseDir: path.resolve(__dirname, './')
})

serlina.prepare()
  .then(() => {
    http.createServer(async (req, res) => {
        res.writeHead(200, { 'Content-Type': 'text/html' })
        if (req.url === '/page1') {
          const rendered = await serlina.render('page1')
          res.write(rendered.string)
        } else {
          res.write('works!')
        }
        res.end()
    }).listen(8090)
  })
  .catch(console.error)
```

您的页面:

```
// page/page1.js

export default () => {
  return <div>Hello Serlina!</div>
}
```

最后，运行`node index.js`并访问[http://localhost:8090/page 1。](http://localhost:8090.)您将看到 React 页面。

如您所见，有两种主要方法:

*   prepare()做编译工作。
*   render()呈现一个页面，并获取将被发送到客户端的呈现字符串。

与 Next.js 不同，Serlina 需要手动注入您在`getInitialProps`中使用的有效负载:

```
// index.js

const { Serlina } = require('serlina')
const path = require('path')

const http = require('http')

const serlina = new Serlina({
  baseDir: path.resolve(__dirname, './')
})

serlina.prepare()
  .then(() => {
    http.createServer(async (req, res) => {
+       serlina.injectPayload({ req }) // manually inject. globally res.writeHead(200, { 'Content-Type': 'text/html' })
        if (req.url === '/page1') {
+         const rendered = await serlina.render('page1', { foo: 'bar' }) // manually inject. specifically.
          res.write(rendered.string)
        } else {
          res.write('works!')
        }
        res.end()
    }).listen(8090)
  })
  .catch(console.error)
```

然后，您可以使用有效负载:

```
// page/page1.js
import * as React from 'react'

export default class Page1 extends React.Component {

  static async getInitialProps ({ req }) {
    return {
      url: req.url
    }
  }

  render () {
    return <div>You are now at {this.props.url}</div>
  }
}
```

你可以通过[http://serlina.js.org](http://serlina.js.org)或[https://github.com/djyde/serlina](https://github.com/djyde/serlina)深入了解塞雷娜

您可以在现有的代码库中轻松使用它。只需编写一个新的路由器，`prepare()`在启动你的服务器之前，`render()`一个页面，并将渲染后的字符串返回给客户端。那就是。这就是我称之为进步的原因。

再比如 [egg-serlina](https://github.com/serlina-community/egg-serlina) ，这也是我创作 serlina 的主要原因。它为 [Egg.js](https://eggjs.org) 带来了最好的服务器端渲染解决方案。