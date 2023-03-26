# 解决方法节点 JS 文件系统

> 原文：<https://medium.com/hackernoon/workaround-the-node-js-file-system-a596408b1dcf>

![](img/d2ead0c39f11d31afcb68773299ab14e.png)

Photo by [Mr Cup / Fabien Barral](https://unsplash.com/photos/o6GEPQXnqMY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/files?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

你好，守夜人的成员们。我们将看到 Node 的核心 API 之一，即文件系统。我知道我们一直在关注代码分割、捆绑包分割、预加载和 Internet Explorer 的永无止境的灾难，但我们将学习 Node 如何与我们的文件和目录交互。这有助于我们处理服务器上的图像或资产，正确存储它们，读取其他文件的内容，写入文件等。

我们将使用文件系统构建一个基本的 CRUD 应用程序。我想我们可以开始了。

我们的小文件夹结构应该是这样的:

```
Database-----(folder)
crud.js------(file)
```

“数据库”文件夹将包含一个文件，该文件将保存我们的汽车业务数据。这些数据将包括汽车的名称和价格。它将存储在 JSON 中。显然，这在生产中是做不到的，为了确保没有人想到这一点，我毫不含糊地声明了这一点。

# 创造

现在，我们将从我们的 ***crud.js*** 文件创建我们的 ***cars.json*** 文件。首先，我们需要文件系统模块和路径模块。

```
const fs = require('fs');
const path = require('path');
```

然后，我们将创建 crud 对象及其基目录，该目录包含从 crud.js 文件到数据库文件夹的路径。

```
const crud = {};
crud.baseDir = path.join(__dirname,'./database');
```

现在，我们将添加一个创建函数，我们的 crud.js 文件应该如下所示:

“创建”函数保存我们想要创建的文件的名称，以及我们想要写入文件的数据。因此，我们在基目录中定义的路径下打开一个新文件，它采用我们指定的任何名称。

‘wx’表示我们可以 **w** 写入文件，但是如果文件已经存在，操作将会失败。我们可以将许多其他的[操作](http://www.tutorialsteacher.com/nodejs/nodejs-file-system)归于该文件。

如果没有错误，Node 会为文件指定一个标识符或文件描述符。如果找到了这个标识符，我们就可以写入文件。

我们希望我们的数据库看起来像这样:

```
[
    {name: '', price: ''},
    {name: '', price: ''}
]
```

这就是原因，我们将数据推送到 **jsonArray** 变量，然后再发送到文件。

我们可以转到页面底部，运行以下命令:

```
crud.create('cars',{'name': 'innoson','price':'$4000'});//Then Obviously(in your console)
node crud
```

如果一切都相同，这将创建一个包含数组的 **cars.json** 文件。

# 阅读

现在，我们应该能够从刚刚创建的文件中读取数据了。我们的读取函数应该是这样的:

这个函数转到基目录，也就是数据库文件夹，然后从我们传递给函数的文件中读取数据。编码格式是 [UTF-8](https://en.wikipedia.org/wiki/UTF-8) ，如果没有错误，我们的数据将被记录。

我们可以在页面底部运行这个:

```
crud.read('cars');
```

# 更新

现在，我们可以使用 **fs.truncate()** 来更改整个文件的内容，但是我们将使用 **fs.appendFile()** 来将数据追加到我们的文件中。

如果我们转到页面底部，运行更新功能

```
crud.update('cars',{name:'Tesla',price:'$10'})
```

我们将得到如下输出:

```
[
   {name: 'innoson',price:'#4000'}
]
,
{name: 'Tesla',price:'$10'}
```

这不是我们想要的，我们需要对象在数组中。为此，我想出了一个计划。

*   我们将读取 **cars.json** 文件的当前内容
*   然后，我们将追加我们的更新
*   **然后**，我们将截断文件并替换。

重点放在“T10”，然后是“T11”，我们需要使用承诺。我们将使用另一个节点 API util。

转到 crud.js 文件的顶部，包括:

```
const { promisify } = require("util");const readFile = promisify(fs.readFile);
```

因此,' readFile '变量将返回承诺。我们新的 crud.update 函数应该如下所示:

因此，我们可以在数据库文件夹中创建一个新的 JSON 文件，并测试我们的更新功能。

```
crud.create('cars-updated',{name:'mercedes',price:'$400'})crud.update('cars-updated',{name:'Toyota',price:'$550'})
```

cars-updated.json 文件应该具有所需的格式。

# 删除

要删除一个文件，我们使用 fs.unlink 方法。所以我们的 crud.delete 函数应该是这样的:

因此，我们可以删除 cars.json 文件，因为不再需要它了。

```
crud.delete('cars')
```

你可以在这里看到 crud.js 文件

*非常感谢你的阅读，如果你喜欢，请鼓掌。如有任何问题或需要进一步澄清，可以留言或发邮件(paschalobba@yahoo.com)。*