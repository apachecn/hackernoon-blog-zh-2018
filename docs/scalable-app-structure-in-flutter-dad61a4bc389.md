# flutter 中可扩展的应用程序结构

> 原文：<https://medium.com/hackernoon/scalable-app-structure-in-flutter-dad61a4bc389>

我最近在探索 flutter，并在一天内构建了一个非常小的应用程序来测试这个框架。我建立了一个最小的应用程序，从用户那里接受一些输入，添加验证，数字格式，并在用户按下提交时显示一个警告框。

我对框架本身感到惊讶，所以我决定尝试一个中等大小的应用程序，我们应该能够做到以下几点

1.  以用户身份登录/注册。这可能是通过谷歌或脸书。
2.  一旦用户登录注册显示他(或她)的仪表板和其他细节。
3.  如果用户想放置他可以通过添加项目到购物车，然后继续付款。

这基本上是一个电子商务应用程序的迷你版本。

现在，在最终确定一个文件夹结构之前，我们需要了解一个关于可伸缩性的简单事情；可伸缩的结构应该是模块化的，即使模块被移除或改变，应用程序也不会中断。

我决定使用与我的 react-native 应用程序相似的项目结构。

所以让我们开始吧。

这是 flutter 提供的核心文件夹结构。

```
flutter-app/
|- android
|- build
|- ios
|- lib
|- test
```

现在，让我们深入到`lib`文件夹，其中有应用程序的主要代码。

1.  `screens` —包含应用程序的屏幕。这里的所有文件都会被导入到`routes.dart`
2.  `util` —包含应用程序的实用程序/常用功能
3.  `widgets` —包含应用程序的常用小部件。比如`Button`、`TextField`等。
4.  `routes.dart` —包含应用程序的路径并导入所有屏幕。

```
lib/
|- main.dart
|- routes.dart
|- screens/
|- util/
|- widgets/
|- data/
|- services/
```

**屏幕-**

```
screens/
|- auth
   |- auth.dart
   |- index.dart
   |- widgets
      |- googleButton
        |- google_button.dart
        |- google_button_container.dart
        |- index.dart
      |- facebookButton
        |- facebook_button.dart
        |- facebook_button_container.dart
        |- index.dart
```

该屏幕包含 2 个按钮，为用户提供从`Google`或`Facebook`登录的选项。

现在我们知道这两个按钮不会在应用程序的其他地方使用，所以我们将它们嵌套在这个屏幕中。

> 仅在特定屏幕内使用的`Widgets`应该放在该屏幕内，而**不要放在**通用部件文件夹内。

如果你看到这种嵌套真的有帮助，万一我想在我的代码后面:-

1.  如果我想修改这些按钮中的某些东西，我可以直接进入这个文件夹，否则如果我把它们放在那里，我必须在公共文件夹中搜索它们。
2.  移除这个模块真的很容易，因为我们知道代码的其他部分不会受此影响。

现在，如果我们再深入一点，我们会看到在`googleButton`和`facebookButton`中使用的按钮是常见的，并且是从`widgets`文件夹中使用的，只有样式被覆盖。这使得整个应用程序中的按钮大小一致。

```
widgets/
|- button.dart // Common button
```

**UTIL-**

```
util
|- date_utils.dart
|- format_utils.dart
```

Util 文件夹包含应用程序的业务逻辑。例如，如果您正在开发一个电子商务应用程序，那么很少有可能是这样的

1.  计算购物车中的总金额。
2.  显示日期、货币等。以适当的格式。

**WIDGETS-**

正如我上面提到的，小部件将包含应用程序中使用的公共部件。

> 我们在这里遵循与屏幕相同的嵌套模式。只有单个小部件需要的小部件应该嵌套在那个小部件中，并且应该只有直接的父级可以访问。

```
widgets/
|- app_button/
  |- app_button.dart
  |- index.dart
```

**数据-**

一旦将 redux(或其他)商店集成到应用程序中，这个文件夹就会出现。你可以在这里输入你的减速器，动作等等。

```
data/
|- auth
   |- access_token
      |- actions.dart
      |- reducer.dart
   |- refresh_token
      |- actions.dart
      |- reducer.dart
   |- reducer.dart
```

**服务-**

这将处理应用程序的所有网络和业务逻辑。例如，一旦你通过了 facebook/google 的认证，你需要用访问令牌更新后端，你可以把那个`authentication`放在这个文件夹中。

```
services/
|- authentication.dart
```

这就是我如何用 flutter 构建我的移动应用程序。

我很想听听你们是怎么做的。

我还创建了一个[存储库](https://github.com/jinxac/flutter-folder-structure)，在这里你可以看到实际的结构。