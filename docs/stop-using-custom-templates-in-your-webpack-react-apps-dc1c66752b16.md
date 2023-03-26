# 停止在 Webpack React 应用程序中使用自定义模板

> 原文：<https://medium.com/hackernoon/stop-using-custom-templates-in-your-webpack-react-apps-dc1c66752b16>

![](img/74877086dc41125df3c3609624ae9436.png)

[谷歌](https://hackernoon.com/tagged/google)“web pack react”，你会注意到顶部结果中的指南都有一个共同点:他们为自己的 React 应用程序创建定制的 HTML 模板。

看，React 应用程序需要一个根节点，在渲染到 [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction) 时作为挂载点，所以大多数时候你只会在一个基本的 HTML 页面中看到一个 ID 为`root`的`<div>`，文件中没有其他内容。