# ssh 通过许多主机代理

> 原文：<https://medium.com/hackernoon/ssh-proxying-through-many-hosts-49b9e4f37df1>

## "那个黑客在世界各地活动，我们找不到他."

![](img/336c575c59e6d59c87e3201f8fb42b7a.png)

有一个很棒的标志可以为 ssh 设置。这是使用 ssh 作为代理的想法，或者在我们的例子中，强制 tty 从一台主机分配到另一台主机，并继续下去。

我说的命令是，***ssh-TT***

```
man ssh: 
-t Force pseudo-terminal allocation. This can be used to       execute arbitrary screen-based programs on a remote machine, which can be very useful, e.g. when implementing menu services. Multiple **-t** options force tty allocation, even if **ssh** has no local tty.
```

这很简单，也很容易做到，让我们直接开始吧。

```
ssh -tt pi@10.1.1.16 -p 22 ssh -tt pi@10.1.1.12 -p 4422 ssh -tt pi@10.1.1.17 -p 22 ssh -tt pi@10.1.1.13 -p 7722
```

在上面的例子中，我在不同的主机上使用不同的 ssh 端口，因为为什么不在非标准端口上使用 ssh，这样会让生活更有趣。当您只有一个 vlan 的入口点，然后在网络的各个层中进一步挖掘时，这非常有用。此外，如果你只是想看看有多少台电脑，你可以连接，并通过傻笑。如果没有 ssh 密钥设置，它会在每次连接时提示您输入用户名和密码，然后保持整个交互会话。

恭喜你，你现在是全球跳跃的黑客了。