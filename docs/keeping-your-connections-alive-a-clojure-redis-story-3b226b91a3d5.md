# 保持人脉——clo jure-Redis 的故事

> 原文：<https://medium.com/hackernoon/keeping-your-connections-alive-a-clojure-redis-story-3b226b91a3d5>

![](img/d23404d6001bbac1854cadb164f0b5ec.png)

Created with https://canva.com

双关语肯定是有意的，保持一些[联系](https://hackernoon.com/tagged/connections)是现实和生产中健康生活的必要条件。

Redis Pubsub 非常棒，还有瑞士军刀风格的[Carmine——全 Clojure Redis 客户端](https://github.com/ptaoussanis/carmine) ( [Clojure](https://hackernoon.com/tagged/clojure) 就像我们在(realized? (:future l))
; false(def l-status (future (while (not (realized? (:future l)))
(println "realized" (:future l)))))
;;realized #object[clojure.core$future_call$reify__6962 0x154a8d5 {:status :pending, :val nil}];;...... A lot of logs later ......;;realized #object[clojure.core$future_call$reify__6962 0x154a8d5 {:status :failed, :val #error {
; :cause nil
; :via
; [{:type java.util.concurrent.ExecutionException
; :message java.io.EOFException
; :at [java.util.concurrent.FutureTask report FutureTask.java 122]}
; {:type java.io.EOFException
; :message nil
; :at [java.io.DataInputStream readByte DataInputStream.java 267]}]
; :trace
; [... <<truncated>> ...]}}]
;;

这听起来不像是对 CPU 周期的最佳利用。所以首先，在`future-call`中添加一个回调错误处理程序的方法——这将允许终端客户端得到连接失败的通知，并基于异常重试。

接下来，添加一个未来的 PING 调用，以防在指定的超时(conn-spec 中的`ping-ms`，默认为 30 秒)后没有收到消息。当连接空闲时添加及时的心跳。

有了这两个地方([使用我的新库](https://github.com/aravindbaskaran/redis-pubsub):))，下面是它的样子，

```
(require '[taoensso.carmine :as car])
(require '[redis-pubsub.core :as pubsub])
(def keepalive-l (**pubsub/with-new-keepalive-pubsub-listener**
  conn-spec
  {
    "ps-foo" #(println %)
    "pubsub:ping" #(println "ping" %)
    "pubsub:listener:fail" #(println "listener failed - add try again" %)}
  (car/subscribe "ps-foo")));; prints ping [pong pubsub:ping] after 30 seconds of idleness;; When listener fails, immediately a callback is fired
; listener failed - add try again [pubsub:error pubsub:listener:fail #error {
; :cause nil
; :via
; [{:type java.io.EOFException
;   :message nil
;   :at [java.io.DataInputStream readByte DataInputStream.java 267]}]
; :trace
; [... <<truncated>> ...]}]
```

成功运行测试用例后，是时候与其他人分享优点了。有一个 [Pull 请求](https://github.com/ptaoussanis/carmine/pull/207)在主库项目上打开，但这可能需要一段时间，因为我可能已经破坏了其他更深层次的测试用例(我希望没有)。所以这是一个附加库([我的第一个 clojars](https://clojars.org/com.aravindbaskaran/redis-pubsub) ，耶！)可以和胭脂红包含在一起。有了这个，你就可以给你的连接提供他们需要的心跳。

感谢阅读！请尝试一下，并让我知道您在 Clojure 服务中是如何处理它的。

不要脸的插头——如果你对不仅仅是一个读者感兴趣，让我们一起努力——[我们积极寻求与像你这样的酷人合作](https://angel.co/swym/jobs):)！

## 参考

1.  [https://groups.google.com/forum/#!topic/redis-db/MwHnkLLs070](https://groups.google.com/forum/#!topic/redis-db/MwHnkLLs070)
2.  [https://github.com/andymccurdy/redis-py/issues/386](https://github.com/andymccurdy/redis-py/issues/386)
3.  [https://stack overflow . com/questions/8678349/subscription-to-redis-channel-does-not-keep-alive](https://stackoverflow.com/questions/8678349/subscription-to-redis-channel-does-not-keep-alive)
4.  [https://github.com/ptaoussanis/carmine/issues/15,](https://github.com/ptaoussanis/carmine/issues/15)https://github.com/ptaoussanis/carmine/issues/201
5.  [http://rossi pedia . com/blog/2014/01/redis-I-like-you-but-you-crazy/](http://rossipedia.com/blog/2014/01/redis-i-like-you-but-youre-crazy/)

# 页（page 的缩写）学生:

你已经走了这么远:)，也许你也喜欢我的其他 Clojure 帖子，只是把它放在那里-

[](https://hackernoon.com/clojure-macros-lessons-from-unspoken-symbols-c4945d8ed8bf) [## Clojure 宏——未言符号的教训

### 在 Swym，我们使用 Clojure 做很多事情。不变的是，我们已经结束了宏被认为是…

hackernoon.com](https://hackernoon.com/clojure-macros-lessons-from-unspoken-symbols-c4945d8ed8bf)  [## 用 Javascript 析构 Clojure

### Clojure 是我在 Swym Corporation 工作时学习的一门语言。当我去年加入时，我…

hackernoon.com](https://hackernoon.com/destructuring-clojure-with-javascript-bd1398bdacb6)