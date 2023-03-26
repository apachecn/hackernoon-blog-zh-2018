# 网络活动

> 原文：<https://medium.com/hackernoon/web-activities-a04e1bae54a6>

# 介绍

这是一个有点返璞归真的话题。对于一个 web 页面来说，打开另一个页面(弹出窗口)执行一些操作并将其结果返回给调用者页面是一项常见的任务。这可能是同源或跨源弹出窗口。典型的用例包括位置选择器、联系人选择器、支付表单、社交登录/单点登录等等。

Android SDK 为此提供了一个简单而干净的 API—[startActivityForResult](https://developer.android.com/training/basics/intents/result.html)。例如，参见[位置拾取器](https://developers.google.com/places/android-api/placepicker)。然而，Web 没有这样清晰的 API。早在 IE 时代，就有一个非标准的 [showModalDialog](https://developer.mozilla.org/en-US/docs/Web/API/Window/showModalDialog) API，它的设计和实现都很糟糕，并且已经在大多数浏览器中被弃用。因此，如果您今天试图在 web 上支持这样一个用例，您必须涵盖以下问题:

1.  可以使用`window.open(url, target)` API 打开一个弹出窗口。
2.  一旦弹出窗口被打开，你可以通过`window.opener`使用自定义窗口消息(`postMessage`)与它通信。弹出窗口必须正确打开，以确保`window.opener`可用(无`rel=noopener`)。
3.  你还必须通过`window.opener`来应对一系列可能的 [XSS 攻击](https://gitlab.com/gitlab-org/gitlab-ce/issues/15331)。显然，您会信任您打开的页面本身，但是弹出网站也有可能受到威胁，这也将使调用者网站受到攻击。
4.  手机浏览器在后台卸载页面。因此，调用方页面可能会被卸载，无法从弹出窗口接收消息。
5.  并非所有环境都支持多窗口和弹出窗口。例如，一些 web 视图或主屏幕 Web 应用程序，不是打开一个带有指定目标的弹出窗口，而是像`target=_top`一样重定向呼叫者页面。在这种情况下，`window.opener`将不可用，也没有办法向它发送消息。通常，这是通过使用重定向来解决的，同样，使用自定义协议通过重定向 URL 传递数据。
6.  有些浏览器允许打开弹出窗口，但不支持`window.opener`。
7.  打开几个页面和几个弹出窗口，它会很快变得非常强大 UX:哪个弹出窗口属于哪个页面？一个好的实现应该是给调用者页面加阴影，当被点击时，会试图将焦点带回弹出窗口。对于一些非常基本和期望的东西来说，这是一个很大的工作量。
8.  如果用户关闭了调用者窗口会怎样？
9.  通过窗口消息传递的数据通过起点控制是安全的。重定向一点也不安全。显然，SSL 覆盖了许多数据泄漏媒介，但最终，用户仍可能复制/粘贴 URL 并通过电子邮件发送。某些环境(如 Web 视图)也可以观察到重定向 URL。
10.  在重定向的情况下，也不可能返回到“以前”的历史状态。这排除了`history.state`和相关 API 的一些用途。

这些只是 Web 开发人员必须应对的一些重要问题。不同的浏览器会带来额外的问题。这是很难做到的，解决方案往往是脆弱的，最终的 UX 是穷人。

当然，网络平台可以做得更好。

# 弹出窗口与 iframes

这篇文章中会提到很多弹出窗口，所以我想立刻回答这个问题:为什么不用 iframes 代替弹出窗口呢？有了 iframe，父页面对弹出窗口的位置和大小有了更多的控制，iframe 避免了上面描述的大多数问题——最明显的是没有重定向回退或卸载，这可能是最难解决的问题。事实上，iframes 已经在 web 上的类似用例中使用了。弹出窗口的使用已经减少了很多:部分是因为上面列出的问题，部分是因为 iframes 提供了更好的 UX 和更简单的实现。

然而，对于许多用例，尤其是安全敏感的用例(如 SSO 和支付)，iframes 的使用受到限制，原因如下:

1.  点击/输入-顶起。一种非常常见的攻击手段，攻击者试图将内容放在 iframe 的顶部，以误导用户激活敏感操作或在他们意想不到的地方输入敏感数据。例如，一个攻击者可以在 iframe“购买”按钮上放置“立即免费获取”UI，这样用户就可以购买他们没有想到的东西。另一种攻击可以在 iframe 的信用卡输入上放置输入控件，从而窃取 iframe 的信用卡。
2.  使用 cookies 和本地存储时的 Iframe 限制。弹出窗口作为顶层浏览上下文，获取未分区的 cookies 和存储。因此，社交登录表单不需要不断地再次提示用户输入密码。然而，在后 ITP 时代，Iframes 在这个领域受到越来越多的限制。

过去的一些提议考虑为 iframes 创建一个全屏模式的特殊子类，以提供防止点击/输入劫持的保证。然而，考虑到 iframe cookie 分区，这是不够的。最重要的是，保证 iframe 的不可点击 jackable 模式需要客户机和服务器的参与，这以`X-Frame-Options`和 CSP 的形式定义了限制。服务器堆栈需要客户端提供一些严格的保证来解除 iframing 限制——这将是一个主要的向后兼容性问题。

鉴于这些限制，我们将主要关注弹出窗口作为解决方案空间。

# 弹出窗口的其他替代方法

Web 平台可以继续使用 [API](https://hackernoon.com/tagged/api) 来支持需要弹出窗口的关键用例。正如使用[支付请求 UI](https://developers.google.com/web/fundamentals/payments/deep-dive-into-payment-request) 一样，网络平台可以为社交登录、日历共享等定义 API。对于这样的用例来说，这将是一个很大的改进。

然而，这种 API 的设计和实现需要时间。迁移需要聚合填充。最终 Web 平台无法支持所有这样的用例。因此，我欢迎新的 Web APIs，但不管怎样，仍然非常需要“获得结果”弹出窗口的通用解决方案。

# Android 活动 API

在我们开始研究处理“得到结果”弹出窗口的不同方法之前，让我们看看 Android APIs 是如何安排这些用例的。

Android 提供了[startActivityForResult](https://developer.android.com/training/basics/intents/result.html)API。它由三部分组成:`startActivityForResult`、`onActivityResult`和`setResult`。例如，[位置选取器](https://developers.google.com/places/android-api/placepicker) API 可以这样使用:

```
[@Override](http://twitter.com/Override)
protected void onActivityResult(int requestCode, int resultCode,
    Intent data) {
  if (resultCode != RESULT_OK) {
    return;
  }
  if (requestCode == PICK_PLACE_REQUEST) {
    // Process result.
  }
}private void pickPlace() {
  startActivityForResult(
      new PlacePicker.IntentBuilder().build(this),
      PICK_PLACE_REQUEST);
}
```

位置选取器活动本身必须调用`setResult`方法:

```
private void clickHandler() {
  Intent intent = new Intent();  
  intent.putExtra("place", selectedPlaceData);  
  setResult(RESULT_OK, intent);
}
```

这个 API 明确存在的事实是一个很好的迹象，表明 Android 认真对待这些用例。但是有一些重要的属性需要考虑:

1.  API 编纂了返回和接收活动结果的标准方法。不需要涉及一些定制的消息传递协议——如果我们只需要将一包数据从被调用的活动转发给调用者，那就太过分了。
2.  即使 Android 决定卸载调用者活动，这个 API 自然仍然可以工作。当调用方活动重新启动时，将调用`onActivityResult`处理程序。
3.  打开的活动窗口的呈现与数据交换的机制是分开的。Android 通常会全屏显示新的活动，完全掩盖了呼叫者。然而，它也可能部分地模糊了呼叫者，等等。

# 网上支持活动

Web 平台可以考虑几种方法来使这种“得到一个结果”弹出窗口的编码变得更容易和不那么脆弱。这里有两个相互支持的主要方向:上下文弹出窗口和用于结果交换的 API。

## 上下文弹出窗口(窗口弹出窗口)

这是上下文弹出窗口的`window.open` API 的专门版本(例如`window.openInContext`，或者可能使用新的`window.open`选项)——也就是说，弹出窗口的“画中画”模式。这种弹出窗口将在呼叫者页面的上下文中打开。构建这个 API 是为了避免引言部分列出的大多数缺陷。特别是，不需要重定向回退，调用者(上下文)页面永远不会被卸载。它仍然依赖自定义窗口消息将结果传递给调用者。

为了说明这个选项，考虑 Web 上弹出窗口的当前状态。桌面和移动浏览器的 UX 总是很差:打开一个单独的标签或窗口。如果用户切换到另一个标签，很难找到弹出窗口。

这方面的一个模型可以是[支付请求 UI](https://developers.google.com/web/fundamentals/payments/deep-dive-into-payment-request) 。在[桌面](https://developers.google.com/web/fundamentals/payments/deep-dive-into-payment-request)和[移动](https://developers.google.com/web/updates/2016/07/payment-request)上，该 UX 确保:

1.  很明显，支付 UI 显示在调用者页面的上下文中。
2.  仍然有明确的起源归属。
3.  弹出窗口的内容不能被页面本身或另一个弹出窗口遮挡，它是模态的。因此有一个固有的点击/输入劫持保护。
4.  在[桌面](https://developers.google.com/web/fundamentals/payments/deep-dive-into-payment-request)用户可以切换选项卡。弹出窗口将停留在选项卡中，当用户返回时，它还会在那里。用户不需要跟踪两个窗口。

如果弹出窗口可以作为顶级浏览上下文打开，但仍然在结构上和视觉上呈现在调用者页面的上下文中，那就太好了。

一个重要的细微差别是:这样的弹出窗口不能打开嵌套的弹出窗口——没有好的方法使这成为一个好的 UX。但这也会通过减缓标签的扩散给浏览器带来更好的性能参数。

额外的大承诺:上下文弹出窗口可以完全消除重定向回退，这反过来也可以简化许多 API。正如您将在本文的其他地方看到的，重定向回退(由于单窗口浏览器或页面卸载)是“获得结果”模式的主要复杂性。

关于这个话题的最后一句话:网络浏览量。今天的网页浏览量占了移动流量的很大一部分(有人估计接近 50%)，这使得这种模式变得非常复杂。默认情况下，Web 视图是单窗口浏览器。支持多窗口模式是一项非常复杂的任务，需要权衡内存。理想情况下，上下文弹出窗口将在开箱即用的现代 Web 视图中实现。默认情况下不必启用它们，但是实现本身对于减少浏览器和 Web 视图之间的解决方案空间碎片非常重要。

## 用于结果交换的 API

当弹出窗口实际打开时，可以使用窗口消息返回结果。为此，调用者和弹出窗口必须同意一个定制的消息传递协议。对于像返回单个结果这样简单的事情来说，这有点矫枉过正，而且充满了安全隐患。但这很有效。

然而，消息传递并不总是可用的。消息传递不可用的一些情况:

1.  在重定向模式下，调用者的上下文被弹出式菜单代替。`window.opener`字段不可用，无法发送信息。请注意，重定向模式有时是 UX 的选择，但有时是环境所迫:单窗口浏览器、弹出窗口拦截器等。
2.  调用者窗口可以在移动设备上卸载，使得消息传递不可能。

在重定向模式中，将数据返回给调用者的唯一方式是将 URL 中的结果重定向回调用者，例如 URL 片段中的结果。当结果敏感时，必须对其进行加密，以避免不必要的泄露。正确支持重定向模式是一项艰巨的任务，任何解决方案都非常脆弱。

这里要考虑两种方法:提供一种在重定向模式下返回数据的安全方法，或者采用一种支持弹出窗口和重定向的新 API，类似于 Android Activity API。

我们想把重点放在 Web 的 Activity API 上，但是首先，说几句重定向模式。

## 在重定向模式下返回数据的附加 API

我们可以提供一个专门针对重定向模式的特殊 API 来安全可靠地返回数据。例如，我们可以扩展 Web 历史 API。在重定向回调用者之前，弹出窗口会将数据推入历史堆栈:

```
history.pushResult(resultData, "[https://caller.com](https://caller.com)");
window.location.replace("[https://caller.com/continue](https://caller.com/continue)");
```

调用者将能够在启动时从历史堆栈中读取数据:

```
var result = history.popResult("[https://popup.com](https://popup.com)");
if (result) {
  // Result is already available.
  ...
}
```

请注意，push 和 pop 都被限制在调用者和弹出窗口的特定来源，以确保安全地交换数据。

# 提议——用于 Web 的类似 Android 的活动 API

在 Web 上采用类似 Android 的 Activities API 是一个更激进的解决方案。但是它可以很自然地解决重定向和弹出模式。它也可以很好地发挥上下文弹出窗口的想法，以改善 UX。

## 应用程序接口

遵循 Android API，我们可以在 Web 上引入类似的 API，包括:`window.openForResult()`、`window.onResult()`和`window.setResult()`。上面的位置选取器调用者在 Web 上看起来像这样:

```
// Anticipate that the result might arrive at some point, even
// if openForResult has not been called in the instance of this
// page.
window.onResult(
    'pickPlace',
    '[https://maps.google.com'](https://maps.google.com'),
    (response) => {
      if (response.ok) {
        // Process result.
      }
    });// Call openForResult.
button.onclick = () => {
  window.openForResult(
      'pickPlace',
      '[https://maps.google.com/pickplace'](https://maps.google.com/pickplace'),
      target);
};
```

弹出页面将返回如下结果:

```
window.setResult(ok, payload);
```

让我们更详细地看看这个 API。

## window.openForResult

```
window.openForResult(
    requestId: string,
    url: string,
    target: string,
    opt_options: string
): void
```

这种方法类似于`window.open`，但是也有一些关键的区别。这些论点是:

*   `requestId` —稍后将在`window.onResult`回调中可用的字符串 ID。
*   `url` —弹出的网址。
*   `target` —窗口目标:这使它成为弹出窗口或重定向窗口。
*   `opt_options` —附加选项，类似于`window.open`

与`window.open`不同，`window.openForResult` API 中既不需要弹出窗口引用，也不需要`window.opener`。虽然这些仍然可以提供，删除它们将减少 XSS 漏洞的表面。

## windows . on 结果

```
window.onResult(
    requestId: string,
    resultOrigin: string,
    callback: function(Response)
): void
```

该方法用于注册 requestId 的回调，该回调将在`window.openForResult`中进一步指定。

抛弃这种方法，简单地让`window.openForResult`回复一个承诺，这很有诱惑力。然而，由于重定向和页面卸载，这并不那么简单。如果调用者页面已经被重定向或卸载，结果将由浏览器保存在某个临时存储中(如历史堆栈),一旦使用`window.onResult`注册了回调，它将立即被调用并得到结果。此功能对于下面某一部分中的重定向聚合填充也很有用。另一方面，如果上下文弹出窗口成为现实，它可以帮助简化这个 API。

另一个细微差别是，`resultOrigin`参数通常是不必要的，因为原点在`window.openForResult`调用中的`url`中是清楚的。然而，这是一个很好的额外保护，也将有助于多填充物。

## window.setResult

```
window.setResult(
    ok: boolean,
    payload: Object
): void
```

一旦结果可用，弹出窗口将调用此方法，并关闭弹出窗口返回给调用者。

这些论点是:

*   `ok` —完成信号:`true`表示成功，`false`表示取消或失败。
*   `payload` —在弹出窗口中采取的操作的数据有效负载。出错时—失败的原因。

# 聚合填料

聚合填充这个 API 是有挑战性的，但也是可能的。

## window.openForResult 聚合填充

API 调用是:

```
window.openForResult(requestId, url, target, opt_options)
```

聚合填充将执行以下步骤:

1.  设置窗口消息监听器。
2.  叫`popup = window.open(url, target, opt_options)`。主动将`#ACTIVITY={requestId, returnUrl}`片段添加到`url`中，以防浏览器环境会像`_top`一样静默打开窗口(见步骤 7)。
3.  如果`window.open`失败或返回无效的弹出对象，转到步骤 7。
4.  开始保活轮询以检查`popup.closed`。我们需要它，以防弹出窗口被用户关闭，产生“取消”信号。
5.  当“结果”消息到达时，将`requestId`和`response{ok, payload}`传递给结果处理代码。
6.  将“结果-确认”消息发送回弹出窗口。结束了。
7.  如果`window.open`调用失败，使用添加的片段`#ACTIVITY={requestId, returnUrl}`将当前页面重定向到`url`。调用方页面的执行被中止，但是我们希望在弹出页面通过重定向完成后返回。结束了。

如果`window.open`polyfill 返回重定向失败(步骤 7)，弹出页面重定向回调用者时:

1.  解码将包含结构`{requestId, ok, payload}`的`#ACTIVITY={…}`片段参数。
2.  检查`document.referer`和`url`是否来自同一原点。如果不是同一原点，则失败。
3.  将`requestId`和`response{resultOrigin, ok, payload}`传给结果处理代码。结束了。

在所有这些操作之后，`#ACTIVITY`被从片段中删除。

## window . on 结果聚合填充

API 调用是:

```
window.onResult(requestId, resultOrigin, function(response) {})
```

多填充的执行取决于响应是在调用`window.onResult()`之前还是之后到达。

如果响应在调用`window.onResult`之前到达，polyfill 会简单地将响应存储在`requestId`的内存或历史堆栈中。一旦`window.onResult`被调用并且相应的响应可用，执行以下步骤:

1.  检查`resultOrigin`参数是否与`response.resultOrigin`相同。
2.  调用`callback(response{ok, payload})`。
3.  从内存中删除响应。

## window.setResult 多填充

API 调用是:

```
window.setResult(ok, payload);
```

弹出页面中的聚合填充考虑了两种模式:弹出或重定向。如果`window.opener`可用，采取弹出模式并执行以下步骤:

1.  设置消息监听器并等待“结果确认”消息。
2.  通过`window.opener.postMessage`发送带有`{ok, payload}`的“结果”消息。
3.  一旦“结果-确认”信息到达，关闭自己。结束了。
4.  如果“result-acknowledge”未在超时内到达，则假定呼叫者寻呼机已卸载，并尝试使用重定向模式进行恢复。继续重定向步骤。

如果`window.opener`不可用或消息传递失败，则采用重定向模式并执行以下步骤:

1.  解析`#ACTIVITY={requestId, returnUrl}`。
2.  检查`returnUrl`和`document.referrer`是否具有相同的原点。如果没有，就失败。
3.  如果`window.opener`不可用，重定向到添加了片段`#ACTIVITY={requestId, ok, payload}`的`returnUrl`。结束了。
4.  如果`window.opener`可用(即消息传递失败)，打开与目标`_blank`相同的 URL，即`window.open(‘returnUrl#ACTIVITY={…}’, ‘_blank’)`。结束了。

## 聚合填充、重定向和数据敏感性

重定向确实使数据交换机制变得复杂了。在某些用例中，将敏感数据作为重定向 URL 中的`payload`传递可能会有问题。如果这被认为是一个问题，弹出窗口和调用者页面必须同意不以纯文本形式发送数据，而是使用某种形式的加密。例如:

```
window.onResult(
    'sensitive-request',
    '[https://popup-domain'](https://popup-domain'),
    (response) => {
      if (response.ok) {
        fetch('[https://popup.com/decrypt](https://popup-domain/get-payload.json?nonce=${response.payload.nonce}')', {
          method: 'POST',
          body: response.payload.encrypted,
        })
        .then(response => {
           // Process the payload as the actual popup response.
        });
     }
   });
```

这样，敏感数据永远不会在重定向 URL 中传递，服务获取可以控制 CORS 源。`https://popup.com/decrypt.json`将解密并返回有效载荷，并依靠 CORS `Origin`报头来确保端到端的安全性。关键的是，该请求还必须依赖一些内部会话标识符来防止会话固定攻击(SFA):

```
app.post('/decrypt.json', (req, res) => {
  // "decrypted" is a structure:
  // {forOrigin: string, sessionId: string, data: Object}
  var decrypted = decrypt(req.body['encryped']); // The CORS origin must match the indented origin:
  if (decrypted.forOrigin != req.headers.origin) {
    res.sendError(403);
    return;
  } // The CORS cookie should correspond to the intended sessionId:
  if (decrypted.sessionId != getSessionId(req.cookies)) {
    res.sendError(403);
    return;
  } // All good: send back the data.
  res.send(decrypted.data);
});
```

# 网络活动实施

虽然不是完全相同的 API，但 GitHub 中的 [web-activities](https://github.com/google/web-activities) 项目实现了一个形状非常相似的 API。

# 其他考虑

## 本地集成和定制协议

目前，`window.open`在理论上可以被转发给 Android 上的一个匹配的本地活动。但是，没有办法将结果返回给调用者页面。让我们想象一下这是如何成为可能的。

如果浏览器实现了提议的 API，那么使用相同的 API 将它扩展到本地活动将会很简单。然而，`openForResult` API 如何允许本地调用呢？Android 已经允许意图过滤器拦截 URL，但是另外，我们可以扩展`openForResult`来支持[意图 URL](https://developer.chrome.com/multidevice/android/intents)和[自定义协议](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/registerProtocolHandler)的替代 URL。例如:

```
window.openForResult(
    requestId,
    [
      // First try an intent URL.
      'intent://get-place/#Intent;scheme=a;package=com.a;end',
      // Then, try a custom protocol.
      'web+location://get-place',
      // Finally, fallback to web URL.
      '[https://maps.google.com/placepicker'](https://maps.google.com/placepicker')
    ]);
```

请注意，在这种情况下,`window.onResult`也将接受一个用于`resultOrigin`参数的原点数组。

如果来源对于敏感通信很重要，呼叫者页面可能需要“严格来源模式”，在这种情况下，浏览器/Android 平台可能需要类似于 Android 的[数字资产链接](https://developers.google.com/digital-asset-links/v1/getting-started)协议的来源验证。例如:

```
window.openForResult(requestId, url, {origin: 'strict'});
```

本机支持目前不可聚合填充。如果指定了多个 URL，polyfill 将只使用数组中的最后一个(后备)URL。

# 摘要

今天在 Web 上实现高质量的弹出结果模式太复杂了。此外，许多此类案例都有重要的安全和隐私需求，这一事实更是雪上加霜。Web 平台可以以上下文弹出窗口的形式，和/或通过遵循 Android SDK 现有的`startActivityForResult`协议来实现对这种模式的改进。其中任何一个或两个都可以显著改善开发体验、稳定性、安全性、跨浏览器支持和 UX。

# 参考

*   [Window.open API](https://developer.mozilla.org/en-US/docs/Web/API/Window/open)
*   [window . showmodaldialog API](https://developer.mozilla.org/en-US/docs/Web/API/Window/showModalDialog)
*   [安卓的 startActivityForResult API](https://developer.android.com/training/basics/intents/result.html)
*   [付款申请界面](https://developers.google.com/web/fundamentals/payments/deep-dive-into-payment-request)
*   [GitHub 中的网络活动项目](https://github.com/google/web-activities)