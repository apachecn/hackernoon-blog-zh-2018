# 解释了 Rails 对块的响应

> 原文：<https://medium.com/hackernoon/rails-respond-to-block-explained-1563773d23a3>

当您启动第一个 Rails 应用程序时，您可能会认为**控制器动作**只能呈现 HTML。那么为什么以及如何使用 **respond_to** 块呢？在这里，我们将向您展示 **respond_to** 块有多方便。

第一步是创建一个新项目，并在 Rails 中生成一个[脚手架](https://kolosek.com/rails-scaffold/)。让我们来看看我们的一些控制器动作。

```
def index
  @blogs = Blog.all
end
```

如您所见，`index`动作没有`respond_to`块。所以，这在渲染 HTML 时是可行的。但是，如果客户要求获得文本格式的页面，该怎么办呢？这将导致一个**异常**:

`ActionView::MissingTemplate (Missing template blogs/index ... with { ... :formats=>[:text], ...})`

> Rails 检查注册的格式，并试图为请求中的 [MIME 类型找到兼容的格式。如果没有处理程序，它将引发一个错误。最大的](https://github.com/rails/rails/blob/4f6f43338f7e4d3a3fe5ad86d024007ee95a7389/actionpack/lib/action_dispatch/http/mime_types.rb)[信用](https://stackoverflow.com/questions/30600864/rails-respond-to-blocks-and-format/30601162#30601162)。

我们不是告诉我们的客户**文件丢失了**，而是想告诉他们**请求格式不被支持**。您可以在您的`index`动作中添加一个`respond_to`块:

```
def index
  @blogs = Blog.all respond_to do |format|
     format.html # index.html
     format.js # index.js
     format.xml { render xml: @blogs }
   end
end
```

更改后，客户端会在不支持格式时得到`406 error`。另外，您的`index`动作将响应两种新格式:js 和 xml。

如果你需要一个简单快捷的方法来渲染你的对象，你可以使用这个**快捷键**，它的工作方式和上面的例子一样:

```
def index
  @blogs = Blog.all
  respond_to :html, :json, :xml
end
```

不确定这是否真的有效？继续使用 [RSpec](https://kolosek.com/rails-rspec-setup/) 和 [FactoryBot](https://kolosek.com/factory-girl-associations/) 进行测试！

如果你能有一个`respond_to`来影响*整个*控制器就好了。通常，控制器中的每个动作都可以使用相同的格式，你可以通过使用`respond_with`来实现这一点。下面是一个如何实现它的示例:

```
class BlogController < ApplicationController
  respond_to :html, :json, :xml # GET /blogs
  # GET /blogs.json
  # GET /blogs.xml
  def inde
    @blogs = Blog.all
    respond_with(@blogs)
  end
end
```

如果您需要更多的控制，并希望能够有几个不同的动作，您总是可以使用完整的 respond_to 块。

在 Rails 4.2 中，`respond_with`不再包含在内。但是如果你[安装了响应宝石](https://kolosek.com/rails-bundle-install-and-gemfile/)就可以拿回来。一旦你安装并生成了一个[轨道脚手架](https://kolosek.com/rails-scaffold/)，生成器将使用`respond_with`而不是`respond_to`来创建控制器。

```
class BlogController < ApplicationController
  before_action :set_blog, only: [:show, :edit, :update, :destroy]
  respond_to :html, :json def index
    @blogs = Blog.all
    respond_with(@blogs)
  end
  ...
end
```

# 全部格式化还是任意格式化？

如果您想指定一个`respond_to`来呈现所有格式的内容，同时保持其他选项不变，您可以按以下方式使用 **format.all** :

```
respond_to do |format|
  format.csv { render_csv }
  format.all { render_404 }
end
```

使用 **format.any** 如果你想指定一个不同格式的公共块:

```
def index
  @blogs = Blog.all respond_to do |format|
    format.html
    format.any(:xml, :json) {
      render request.format.to_sym @blogs
    }
  end
end
```

所有的[控制器动作](https://kolosek.com/rspec-controller-test/)都需要测试，以确保它们按预期工作！

# 定义自定义 MIME 类型

如果您需要使用默认情况下不支持的 MIME 类型，您可以在`config/initializers/mime_types.rb`中注册自己的处理程序:

```
Mime::Type.register "text/markdown", :markdown
```

本文到此为止！感谢您花时间阅读它！

*原载于 2018 年 3 月 5 日*[*kolosek.com*](https://kolosek.com/rails-respond_to-block/)*。*