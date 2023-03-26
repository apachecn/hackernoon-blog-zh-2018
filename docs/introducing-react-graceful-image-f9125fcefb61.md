# 介绍反应优雅的形象

> 原文：<https://medium.com/hackernoon/introducing-react-graceful-image-f9125fcefb61>

一个图像组件，通过提供可选的延迟加载、可选的占位符和失败重试来优雅地处理图像错误。尤其适用于信号较差的应用场合，例如乘坐火车、公共汽车或汽车。

# 视觉示例

![](img/026148d71dafb8cebd099f4a8487f01b.png)

Browser loading images in various states

1.  当图像由于信号不好而失败时的默认浏览器行为
2.  带有反应优雅的图像占位符
3.  禁用反应优雅图像的占位符
4.  使用 react-graceful-image 重试—如果映像加载失败，包将再次尝试加载映像

*(注意:这些并不相互排斥，例如，默认行为同时使用占位符和重试。)*

# 履行

**占位符**

*   实现为嵌入到数据 URI 中的 SVG 这意味着您不必为了下载占位符而触发额外的 HTTP 请求，占位符就嵌入到组件中
*   该组件还提供了一种定制占位符的方法，即通过`placeholderColor`属性改变占位符的颜色，以及通过常规的`styles`或`className`属性*(注意，通过这种方式传递的样式也会传递到实际的图像中。)*
*   它还为您提供了禁用占位符的选项，在这种情况下，图像在加载之前根本不会呈现，因为图像在成功加载之前不会呈现，这也意味着如果图像失败，它将不会占用页面上的预期空间，您也不会得到一个损坏的图像图标

**重试**

*   使用`setTimeout`实现，它会调整延迟和所需的重试次数，以尝试优雅地重新加载映像
*   默认情况下，每次重试之间的延迟会加倍，但是可以通过`retry.accumulate`属性进行修改，该属性接受以下值之一“乘”、“加”、“不加”，并相应地更新重试算法*(注意，您也可以更改延迟时间和重试次数)*

# 用法示例

下面的代码片段将显示一个蓝色的 SVG 占位符，如果它在可视视窗中，那么它将加载实际的给定图像，并在加载完成后淡入。如果加载映像失败，它将再次尝试加载映像，最多 8 次，初始延迟为 2 秒，然后增加到 4 秒，8 秒，16 秒，依此类推(默认重试配置)。它还会将“content-image”类名中定义的任何样式应用到占位符*(以及图像加载后的图像)*。

```
import React, { Component } from 'react'
import Image from 'react-graceful-image'class YourComponent extends Component {
  render() {
    return (
      <Image
        src="path_to_image"
        className="content-image"
        alt="My awesome image"
        placeholderColor="#0083FE"
      />
    );
  }
}
```

下面的代码片段将显示一个浅灰色(默认)的 SVG 占位符，如果它在可视视窗中，那么它将加载实际的给定图像，并在加载完成后淡入。如果加载映像失败，它将再次尝试加载映像，最多 15 次，初始延迟为 3 秒，然后增加到 6 秒，然后增加到 9 秒，然后增加到 12 秒，依此类推。它会将 20px 的填充应用到占位符*(以及图像加载后的图像)。*

```
import React, { Component } from 'react'
import Image from 'react-graceful-image'class YourComponent extends Component {
  render() {
    return (
      <Image
        src="path_to_image"
        width="150"
        height="150"
        style={{padding: '20px'}}
        alt="My awesome image"
        retry={{count: 15, delay: 3, accumulate: 'add'}}
      />
    );
  }
}
```

感谢阅读，更多使用细节请查看 github 项目页面:[https://github.com/linasmnew/react-graceful-image](https://github.com/linasmnew/react-graceful-image)。您也可以使用:`npm install --save react-graceful-image`通过 npm 安装它