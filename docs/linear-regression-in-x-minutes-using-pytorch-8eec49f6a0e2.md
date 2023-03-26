# 2 分钟内线性回归(使用 PyTorch)

> 原文：<https://medium.com/hackernoon/linear-regression-in-x-minutes-using-pytorch-8eec49f6a0e2>

![](img/de89fe61c263da88452679d96b39d6b3.png)

> [*你可以在这个 Github 回购*](https://github.com/init27Lab/DL-Toolkit) 中找到所有附带的代码

这是 PyTorch [# nn.linear is defined in nn.Module

**def** forward(self, x):
*# Here the forward pass is simply a linear function*

out = self.linear(x)
**return** out

input_dim = 1
output_dim = 1](/init27-labs/pytorch-primer-series-0-e2e5df9b31c6# Calling Super Class's constructor</em><br/>        self.linear = nn.Linear(input_dim, output_dim)<br/>        <em class=)

## [步伐](/init27-labs/pytorch-primer-series-0-e2e5df9b31c6# Calling Super Class's constructor</em><br/>        self.linear = nn.Linear(input_dim, output_dim)<br/>        <em class=)

1.  [创建模型的实例](/init27-labs/pytorch-primer-series-0-e2e5df9b31c6# Calling Super Class's constructor</em><br/>        self.linear = nn.Linear(input_dim, output_dim)<br/>        <em class=)
2.  [选择损失标准](/init27-labs/pytorch-primer-series-0-e2e5df9b31c6# Calling Super Class's constructor</em><br/>        self.linear = nn.Linear(input_dim, output_dim)<br/>        <em class=)
3.  [选择超级参数](/init27-labs/pytorch-primer-series-0-e2e5df9b31c6# Calling Super Class's constructor</em><br/>        self.linear = nn.Linear(input_dim, output_dim)<br/>        <em class=)

```
model = LinearRegressionModel(input_dim,output_dim)

criterion = nn.MSELoss()*# Mean Squared Loss*
l_rate = 0.01
optimiser = torch.optim.SGD(model.parameters(), lr = l_rate) *#Stochastic Gradient Descent*

epochs = 2000
```

[**训练模型**](/init27-labs/pytorch-primer-series-0-e2e5df9b31c6# Calling Super Class's constructor</em><br/>        self.linear = nn.Linear(input_dim, output_dim)<br/>        <em class=)

```
**for** epoch **in** range(epochs):

    epoch +=1
    #increase the number of epochs by 1 every time inputs = Variable(torch.from_numpy(x_train))
    labels = Variable(torch.from_numpy(y_correct))

    *#clear grads as discussed in prev post* optimiser.zero_grad() *#forward to get predicted values* outputs = model.forward(inputs)
    loss = criterion(outputs, labels)
    loss.backward()*# back props*
    optimiser.step()*# update the parameters*
    print('epoch **{}**, loss **{}**'.format(epoch,loss.data[0]))
```

[**最后，打印预测值**](/init27-labs/pytorch-primer-series-0-e2e5df9b31c6# Calling Super Class's constructor</em><br/>        self.linear = nn.Linear(input_dim, output_dim)<br/>        <em class=)

```
predicted =model.forward(Variable(torch.from_numpy(x_train))).data.numpy()

plt.plot(x_train, y_correct, 'go', label = 'from data', alpha = .5)
plt.plot(x_train, predicted, label = 'prediction', alpha = 0.5)
plt.legend()
plt.show()
print(model.state_dict())
```

[如果你想阅读《我的自驾游》第二周的内容，这里有一篇博文](/@init_27/a-self-driving-new-year-2-d1bbc5a83570?source=user_profile---------2----------------)

本系列的下一部分将讨论线性回归。

> [你可以在推特@bhutanisanyam1](http://twitter.com/bhutanisanyam1) 上找到我，在 [Linkedin 上联系我](https://www.linkedin.com/in/sanyambhutani/)
> 
> [订阅我的时事通讯，获取深度学习和计算机视觉阅读的每周精选列表](http://tinyletter.com/sanyambhutani/)