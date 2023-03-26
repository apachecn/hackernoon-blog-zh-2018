# 如何为 React Native 开发 Android UI 组件

> 原文：<https://medium.com/hackernoon/how-to-develop-android-ui-component-for-react-native-43b121272eb8>

![](img/c5ed7bc740bd456c0f5b8e50b2dcce00.png)![](img/29be0406380ff6451116adee9395fcd8.png)

Android UI Component for React Native (Source: [47Billion](http://47billion.com))

在我们用 React Native 开发的一个项目中，我们遇到了一个问题。我们想使用带有文本覆盖的视频播放器。尽管 React Native 中有很多视频播放器的实现，但没有一个提供这样的功能。所以我们决定开发自己的组件，并在 React 本机代码中使用它。

本文描述了如何将任何 Android 视图组件转换为 React 原生组件。当您需要使用任何 Android 视图或 React Native 应用程序中的自定义视图组件时，这是必需的。

## 创建视图

创建一个 React 本地项目。在这个项目中，我们为本地代码准备了 Android 和 IOS 文件夹。在 Android Studio 中打开 Android 代码，并使用原生 java 代码创建一个视图。

## 实现 SimpleViewManager

编写一个从 ViewManager 继承的类。在这个类中，指定在 React 本地代码中使用 Android 代码的哪个视图。

```
public class VideoViewManager extends SimpleViewManager<VideoView>
```

超类**simple viewmanager<VideoView>**指定我们要通过这个类暴露 Android 的 video view 来进行原生反应。一个**视图管理器**是一个 React 本地接口，负责实例化和更新应用程序中的视图。 **SimpleViewManager** 是一个使用我们的视图的泛型类。我们可以使用 Android 中已经存在的任何视图，如 ImageView、VideoView、TextView、LinearLayout，或者我们可以实现和使用自定义视图。这里我们使用的是视频视图。

在管理器中，使用以下步骤对组件进行初始设置:

1.  编写一个继承自 **ViewManager** 或其子类( **SimpleViewManager** )的类
2.  实现方法 **getName** ，它返回一个我们用来从 React Native 获取管理器的字符串常量
3.  实现**createview instance(ThemedReactContext react context)**方法，在该方法中，我们创建组件的一个实例并返回对象。

```
public class **VideoViewManager** extends **SimpleViewManager<VideoView>** {public static final String **REACT_CLASS** = “VideoView”;@Override
public String getName() {
  return **REACT_CLASS**;
}@Override
protected VideoView **createViewInstance**(ThemedReactContext reactContext) {
  VideoView videoView = new VideoView(reactContext);
  return videoView;
}
```

4.如果我们想使用 **props** 将一些数据从 React 本地代码发送到我们的组件，那么我们必须编写一个附加方法来接受组件中的数据。参见下面代码中的 **setVideoPath** 方法。

```
public class **VideoViewManager** extends **SimpleViewManager<VideoView>** {public static final String **REACT_CLASS** = “VideoView”;@Override
public String getName() {
  return **REACT_CLASS**;
}@Override
protected VideoView **createViewInstance**(ThemedReactContext reactContext) {
  VideoView videoView = new VideoView(reactContext);
  return videoView;
}@ReactProp(name=”url”)
public void **setVideoPath**(VideoView videoView, String urlPath) {
  Uri uri = Uri.parse(urlPath);
  videoView.setVideoURI(uri);
  videoView.start();
 }
}
```

## 创建包模块

为了从 React Native、调用 **VideoViewManager** ，我们必须使用一个包模块注册它。编写一个实现 **ReactPackage** 接口的类。

在 **createViewManagers()** 方法中，实例化我们想要暴露以反应本机的 ViewManager。

```
public class **VideoViewPackage** implements **ReactPackage** {@Override
  public List<NativeModule>    **createNativeModules**(ReactApplicationContext reactContext) {
    return Collections.emptyList();
  }@Override
  public List<ViewManager> **createViewManagers**(ReactApplicationContext reactContext) {
    return Collections.<ViewManager>singletonList(
      new VideoViewManager()
    );
  }
}
```

## 将包模块添加到应用程序类

在 React 原生项目的应用类中，在 **getPackages()** 方法**中添加包模块。**

```
@Override
protected List<ReactPackage> **getPackages**() {return Arrays.<ReactPackage>asList(
     new MainReactPackage(),
     new VideoViewPackage()
  );}
```

## 实现本机端反应

我们必须创建一个 JS 文件并实现 **requireNativeComponent** 函数。这个函数接收两个参数。第一个参数是我们在 view manager 类中定义的视图管理器的名称，并通过 **getName()** 方法返回。第二个参数是一个有**道具**的对象。

在 src 文件夹中创建 VideoView.js。我们需要从这个文件中导入组件，以便以后使用。

```
import PropTypes from ‘prop-types’;import { requireNativeComponent, ViewPropTypes } from ‘react-native’;var viewProps = {
  name: ‘VideoView’,
  propTypes: {
    url: PropTypes.string,
    …ViewPropTypes,
  }
}
module.exports = **requireNativeComponent**(‘VideoView’, viewProps);
```

## **使用组件**

现在，我们可以在 React 本机应用程序中使用我们的本机组件。

```
import React, { Component } from 'react';
import { StyleSheet, View } from 'react-native';
import VideoView from './src/VideoView';export default class **App** extends **Component** {constructor() {
    super();
  }
  render() {
    return (
      <View 
      style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}><**VideoView** style={{ flex: 1, width: '100%', height: '100%' }}       
      url="https://www.radiantmediaplayer.com/media/bbb-360p.mp4" /></View>
    );
  }
}
```

*感谢阅读。如果你喜欢这篇文章，请随意点击那个按钮👏帮助其他人找到它。*

*感谢 Atul Sharma @*[*470 亿*](http://47billion.com) *详细代码。本文是与移动技术相关的系列文章的一部分。如果您正在寻找一个移动应用程序开发团队来构建您的解决方案，请通过*[*info@47billion.com*](mailto:info@47billion.com)*联系我们。*