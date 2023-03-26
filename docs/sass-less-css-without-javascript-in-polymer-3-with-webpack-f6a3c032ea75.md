# SASS，LESS，Polymer 3 中不带 JavaScript 的 CSS，带 Webpack

> 原文：<https://medium.com/hackernoon/sass-less-css-without-javascript-in-polymer-3-with-webpack-f6a3c032ea75>

从一开始，你就不得不将你的 CSS 编写或注入到 [HTML](https://hackernoon.com/tagged/html) 中，以便对聚合物组件进行样式化。曾经，Polymer 确实包含了导入外部样式表[的能力，尽管这是一个实验性的特性，并且因为支持样式模块而被否决。随着 Polymer 3 的最新版本，我们抛弃了 HTML(最终，不再有无声的错误🎉！)赞成好的 ol…呃我是说好的新的](https://www.polymer-project.org/2.0/docs/devguide/style-shadow-dom#external-stylesheets) [JavaScript](https://hackernoon.com/tagged/javascript) (ES2015 类)。

尽管如此，还是有一个问题。在 HTML 中，我们可以通过 IDE 或文本编辑器获得 CSS 支持。用 JavaScript 编写它们使得静态分析和自动完成代码变得更加困难。

幸运的是，因为它是 JavaScript，这意味着我们可以利用它强大的生态系统，并使用我们可用的工具。

`polymer-css-loader` [是一个 Webpack Loader](https://github.com/superjose/polymer-css-loader) ，它为您创建 JavaScript 样式，并通过将 CSS 导入到聚合物组件 JavaScript 文件中来“使 CSS 再次变得伟大”。

# 如何使用

安装装载机:

```
npm install save-dev polymer-css-loader extract-loader css
```

或者

```
yarn add save-dev polymer-css-loader extract-loader css-loader -D
```

将其添加到 Webpack.config 文件中:

```
module.exports = {
  entry: './src/index.js',
  module: {
    rules: [
     {
        test: /**\.**css|**\.**s(c|a)ss$/,
        use: [{
          loader: 'polymer-css-loader',
          options: {
            minify: true, // defaults to false
          },
        }, 'extract-loader', 'css-loader', 'sass-loader'],
      },
    ],
  },
};
```

注意:您可以使用 sass-loader 或 less-loader。把 polymer-css-loader 留到最后。

并在 JavaScript 组件文件中导入 css

```
import { html, PolymerElement } from '@polymer/polymer/polymer-element';

import './style-1.scss';
// The ?name will specify the name to be used in the include.
import './style-2.css?name=maria';
class PolymerTestComponent extends PolymerElement {
  static get template() {
    return html`
      <style include="style-1 maria">    
      </style>
      <p>This is the test component</p>
      <p>This is the propertie's value: {{prop1}} </p>
      <div>This font size should be bigger</div>
    `;
  }

  static get properties() {
    return {
      prop1: {
        type: String,
        value: 'polymer3-app',
      },
    };
  }
}

window.customElements.define('polymer-test-component', PolymerTestComponent);
```

就是这样！该文件的名称将用于`<style include=””>`

# 其他功能

## 为

通过在导入**的末尾添加`?name=`来添加自定义名称(不要用引号""来表示值)**。

```
import './style-1.scss?name=my-custom-style';// Then:
// Code omitted:
static get template() {
    return html`
      <style include="my-custom-style"> </style>// Code omitted:
```

## 跳过 css 文件

您可以通过在导入结束时添加`?skip`来跳过 css 文件。这个**将把 CSS 包含到你的 Webpack 包中，但是它不会被解析为一个聚合 JavaScript 样式文件。**

```
import './style-1.scss?skip';
```

## 跳过所有文件，明确包含您需要的文件

合并多个库时非常有用。这个**将把 CSS 包含到 Webpack 包中，但是它不会被解析为一个聚合 JavaScript 样式文件。**

```
entry: './src/index.js',
  module: {
    rules: [
     {
        test: /**\.**css|**\.**s(c|a)ss$/,
        use: [{
          loader: 'polymer-css-loader',
          options: {
            minify: true, // defaults to false
            defaultSkip: true // will skip all the files },
        }, 'extract-loader', 'css-loader', 'sass-loader'],
      },
    ],
  },
};
```

然后，你可以用`?include`来表示你希望被加载器解析的那些。

```
import './style-1.scss?include&name=my-custom-style';// Will still be included, but not in the JavaScript
// Polymer-Web Components fashion
import './style-2.scss' // Then:
// Code omitted:
static get template() {
    return html`
      <style include="my-custom-style"></style>// Code omitted:
```