# 使用 Pwned 密码 API 通知您的用户密码被破解

> 原文：<https://medium.com/hackernoon/inform-your-users-about-breached-passwords-using-pwned-passwords-api-2c4b1f2d3f04>

![](img/045cfb472fda227273e34f57311b85f2.png)

Img source: dev.to

密码是我们访问数字账户最常用的方式之一。拥有安全的密码非常重要，这也是我们需要注意的最重要的方面之一。作为开发人员，我们还需要确保为我们的服务提供高水平的安全性。去年 2017 NIST(国家标准和技术研究所)建议针对现有的公开数据泄露检查用户密码，幸运的是，有一个 API 可以让你非常容易地做到这一点。

Pwned Passwords API 是一种服务，您可以使用它来检查密码是否已经作为过去发生过多次的大量数据泄露的一部分而暴露。这些数据包含超过 500，000，000 个以前使用过的密码。

您可以使用以下 gem 作为包装器将它安装到您的 Ruby 应用程序中:

```
gem ‘pwned’
```

安装后，您可以创建一个新的 this Pwned::Password 对象，然后检查它是否被破坏:

```
password = Pwned::Password.new(“password”)
```

您还可以检查密码在数据集中出现的次数。

```
password = Pwned::Password.new(“password”)
password.pwned_count
#=> 3303003
```

由于您可能会在注册过程中使用该服务，因此您还应该考虑到该服务有时可能无法正常工作的事实:

```
begin
 password = Pwned::Password.new(“password”)
 password.pwned?
rescue Pwned::Error => e
 # Ummm… don’t worry about it, I guess?
End
```

您还可以使用以下方法使这个 API 调用变得更加容易:

大多数情况下，您只关心密码之前是否被输入过。您可以使用简化的访问器来检查密码是否已被密码化，或者密码被密码化的次数:

```
Pwned.pwned?(“password”)
#=> true
Pwned.pwned_count(“password”)
#=> 3303003
```

您还可以验证您的模型，如下所示:

```
class User < ApplicationRecord
 validates :password, not_pwned: true
 # or
 validates :password, not_pwned: { message: “has been pwned %{count} times” }
end
```

您甚至可以设置一个阈值，您认为应该使用该阈值来警告用户密码已被破坏。例如，您可能认为只出现两次的密码没有问题:

```
class User < ApplicationRecord
 # The record is marked as valid if the password has been used once in the breached data
 validates :password, not_pwned: { threshold: 2 }
end
```

如果您使用 Devise 进行身份验证，还有另一个专门为此准备的特定 gem。

您可以通过访问 Github 页面来了解关于这个 gem 及其实现的更多信息。