# 黑魔法防御——厄里斯魔镜

> 原文：<https://medium.com/hackernoon/defense-against-dark-arts-the-mirror-of-erised-7d65fc6a333d>

![](img/8ebf2db874314cc38e2684922149f0d7.png)

The Mirror of Erised from Harry Potter and the Sorcerer’s Stone

就像在哈利波特传奇中一样，我们被未知的食死徒(read 黑客)包围，他们出于这样或那样的原因试图攻击可怜的波特(read 客户端/数据)。有时他们想要数据(像足球之石或凤凰社中的预言)，或者有时他们只是想杀死/腐蚀波特以抵御威胁/竞争。无论是哪种情况，我们都需要保护我们的数据和深度学习模型免受这种黑暗攻击。

常规的深度学习模型非常容易受到攻击，因为它通常在训练深度学习模型的集中式服务器中收集大量数据。这被称为集中学习，因为学习发生在中央服务器上。但是通过一种叫做联合学习的技术，我们不是把数据带到模型中，而是把模型带到训练数据中。

在这篇文章中，我将向你解释一个来自 [PySyft](https://github.com/OpenMined/PySyft) 库的例子，带你走过联合学习的一小步。具体来说，我们将看到一个名为“Bob”的终端客户端如何让一个集中式服务器使用其数据，而实际上却不必共享它。当我们浏览这个例子时，你会理解得更多。

在本教程中，我们将使用两台 Jupyter 笔记本。一个用于“Bob”(Bob . ipynb)，他将准备其数据，引用其数据，其中数据(实际上是其指针)可以被中央服务器使用，并最终打开连接，其中它可以接收中央服务器的请求。另一方面，集中式服务器也将有一个文件(Model.ipynb)在其终端运行，该文件将首先与 Bob 的套接字建立连接，获取指向实际数据的指针，最后仅使用指针变量运行其算法。

**Bob.ipynb**

```
##### Import files#############
import syft as sy
hook = sy.TorchHook() # Start a hook to make a connection"""#########################
Give your hook a **id**: “Bob”
**Port Address**: 3000
**is_pointer**: Will update more about it
**is_client_worker**:(bool, optional)** a boolean which determines                             whether this worker is associated with an end user client. If so,                             it assumes that the client will maintain control over when                             tensors/variables/models are instantiated or deleted as opposed to                             handling tensor/variable/model lifecycle internally
**verbose:**(bool, optional)** A flag for whether or not to print events to stdout.
##########################"""hook.local_worker = sy.SocketWorker(hook=hook,
                                   id="Bob",
                                   port=3000,
                                   is_pointer=False,
                                   is_client_worker=False,
                                   verbose=True)
sy.local_worker = hook.local_worker# create two sample Pytorch tensors for our demonstration
import torch
x = torch.FloatTensor([1, 2, 3, 4, 5])
y = torch.FloatTensor([1, 1, 1, 1, 1])
```

检查向 bob 注册对象列表，这是验证我们刚刚创建的张量是否正确配置给 Bob 的简单方法。

```
hook.local_worker._objects
```

输出:
{ 38610448342:[_ local tensor—id:38610448342 所有者:Bob]，
51021230807:[_ local tensor—id:51021230807 所有者:Bob]}

结果清楚地表明，我们作为本地张量创建的 x 和 y 张量归 Bob 所有。

```
# Set ID with which client can request for pointers for our tensorsx.set_id("#X")
y.set_id("#Y")# start listening to commands and wait for magic to happen!hook.local_worker.listen()
```

**Model.ipynb**

```
# Give yourself a name and port address
import syft as sy
hook = sy.TorchHook(local_worker = sy.SocketWorker(id=0, port=3001))# Connect with Bob
remote_client = sy.SocketWorker(hook=hook,
                               id="Bob",
                               port=3000,
                               is_pointer=True)
hook.local_worker.add_worker(remote_client)# Get pointer variables for actual data
x_set = remote_client.search(["#X"])
y_set = remote_client.search(["#Y"])
```

如果您尝试访问 x_set 或 y_set，您将立即看到类似以下的消息

[float tensor[_ pointer tensor—id:22003035565 owner:0 loc:Bob id @ loc:# Y]]

意思是说 y_set 是“PointerTensor”(而不是 Y 的实际值)，你是这个指针的所有者，但是实际数据的“location”是“Bob”，它的 ID 是#Y

```
# Perform Tensor Operation on actual data using pointer variablesimport torch
z = torch.add(x_set[0],y_set[0])
z
```

output:
float tensor[_ pointer tensor—id:12443450818 owner:0 loc:Bob id @ loc:30395812931]

同样，在我们试图访问 y_set 的情况下，当你试图访问 z 的值时，你会得到上面的消息

```
# To get actual value of z
z.get()
```

当您对 bob 的数据执行所有这些操作时，每当您试图访问 Bob 的数据时，Bob 都会收到类似“Received Command From:(' 127 . 0 . 0 . 1 '，54484)"
的通知。与此同时，还会生成一个日志文件。

仅此而已。在这个小例子中，我们看到了如何使用 PySyft 来获得 Bob 数据的指针变量，对其执行操作(运行深度学习模型)并获得结果。从隐私的角度来看，这是一个很大的进步，因为在这个完整的操作中，没有人能够访问 Bob 的实际数据内容。

事后看来，我们上面的节目与第一部电影中的**厄里斯魔镜**非常相似，因为**会给你你想要的东西…..只要你不(错误)使用它！**

现在就这样，如果你设法和我呆在一起到目前为止，我鼓励你去 [PySyft](https://github.com/OpenMined/PySyft) github 页面并开始深入了解它。你可以在我的 github repo [这里](https://github.com/PercyJaiswal/PySyft)找到两款 Jupyter 笔记本的完整代码。

像往常一样，如果你喜欢我的文章，用喜欢和评论来表达你的欣赏。你也可以在 [twitter](https://twitter.com/percyjaiswal) 上找到我和我的其他文章。

直到下一次…干杯！！