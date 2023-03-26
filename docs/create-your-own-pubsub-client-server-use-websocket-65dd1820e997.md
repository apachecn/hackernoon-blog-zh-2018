# 使用 WebSocket 创建自己的 PubSub 客户端和服务器

> 原文：<https://medium.com/hackernoon/create-your-own-pubsub-client-server-use-websocket-65dd1820e997>

## 它是如何工作的？

```
const pubSub = new PubSubClient('ws://localhost:3001', {
      connect: true,
      reconnect: true,
    })

    const topicName = 'abc'

    pubSub.subscribe(topicName, (message) => {
      console.log(`Got message from topic ${topicName}`, message)
    })

    //publish a message to topic
    pubSub.publish(topicName,
      {title: 'Hello subscribers in the topic abc', body: 'How are you ?'})

    // Broadcast send message to subscribers but not me
    pubSub.broadcast(topicName, {body: 'this is broadcast message'})// Unsubscribe
pubSub.unsubscribe(topicName) 
```

## Todo@

*   如果看起来像垃圾邮件发送者，可能会限制发布者和发送消息的时间
*   如果检测到多个发布的消息都指向同一个客户端，服务器可能需要等待一段时间(短时间)，然后发送一批消息，而不是发送一条消息。
*   添加加密客户端和服务器密钥共享，以便在连接时在客户端和服务器之间解密。WebSocket 通信更安全。从客户端到服务器和服务器到客户端的消息是加密的。

## 第 1 部分:WebSocket 客户端和 WebSocket 服务器

## 第 2 部分:订阅:发布、订阅、广播

## 第 3 部分:实现自动重新连接

## 第 4 部分:处理退订

## 演示

github:[https://github.com/tabvn/pubsub](https://github.com/tabvn/pubsub)

视频播放列表[这里](https://www.youtube.com/playlist?list=PLFaW_8zE4amNkFbpUVS3V0t_HrJSQdASD)