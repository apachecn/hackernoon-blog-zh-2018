# 基本数据库交互-Python

> 原文：<https://medium.com/hackernoon/basic-database-interaction-python-7e584c04e10f>

![](img/05aa5b2838430c8a5208c54fb42b1a7a.png)

我最近有一个项目，需要在另一个集合中创建对一个集合的引用。引用数据库或集合是数据的常见需求。对于基于文档的数据库，如果从头开始创建数据，创建这些引用是相当简单的，但是如果已经有了集合，有时会变得有点复杂，需要在集合项之间追溯创建引用，这就是我的情况。

我的项目涉及演讲中的文本，我将简要解释我的场景，以及我如何用 Python 解决它，并希望浏览一些可以集成到您自己的项目中的工具。下面是基本的场景:

## 1-集合之间的关系:

![](img/beeac26e8a28674c5b8657003ae9be7f.png)

对我来说，我有一个演讲集。然后，我在一个单独的集合中有信息。在每次“演讲”中，演讲者可能会引用用户可能想要更多信息的任意数量的主题。信息集合中的每一条记录基本上都是描述一个特定主题的小段落，而演讲则很长，并且涵盖多个主题。所以这两个集合之间的关系应该是一对多，或者多对多，因为一个演讲将引用多个主题，而一个主题将与多个演讲相关联。因为我将使用基于文档的数据库，所以我不会使用表，但是我将使用 python 来添加和创建这些关系。我想到要做的第一件事是，如果我找到一个匹配，就将整个 info 部分添加到第一个数据库中。该结构可能如下所示:

## 解决方案 1 —将两个集合合并为一个集合:

![](img/d94e540a0faeffb0e4e17ab322ff7d2e.png)

然而，这不是一个好主意，你很快就能明白为什么。每个集合中有大量的文本。如果用户必须获取整个数据库，或者甚至一次只获取一个条目，那么加载时间会非常长，并且不会是一个好的用户体验。更好的解决方案是只将一个数据库中的引用添加到另一个数据库中。这类似于 SQL 数据库的工作方式，只是将引用集合的 id 添加到第一个集合或表中。所以我们基本上是在 mongo 数据库中添加相同的引用。新结构将如下所示:

## 解决方案 2 —将引用从一个集合添加到另一个集合:

![](img/0a6572940a8605762da9faa4182809e0.png)

现在，寻找这些联系的过程也可能是一个过程。通常，在 SQL 数据库中，您会有一个用于这些多对多关系的连接表。例如，我们可能有一个主题表。如果一个演讲包含该表中的一个主题，并且一个信息记录也包含该主题，我们将通过联合主题表链接信息和演讲。我将用 Python 做一个类似的方案，但不是一个表，甚至不是第二个数据库，我只是有一个我要搜索的主题的数组或列表，添加到每个集合的连接，然后我可以删除列表，或者保存它供以后使用。但是无论哪种方式，我都不需要将该列表作为单独的集合添加。下面是这个过程的伪代码:

## 用“连接”集合标识相关集合:

![](img/9981070a8855b19142061038c795cd50.png)

你可以在这里看到，我在这方面的一般思维过程是拥有这个数组，最有可能的是，我会从循环遍历 speeches 集合开始。然后，如果主题数组中有任何主题，我将遍历信息集合…如果在任何记录中也发现了该主题，我将从信息集合中获取 ID，并将其添加到 speeches 集合中的引用对象中。

很简单的东西。至少有几种方法可以开始这个过程，这在很大程度上取决于你的收藏存放在哪里。如果它们存储在 mlab 这样的在线数据库中，那么我们需要导入请求来访问数据库。但是，如果它们存储在本地数据库或 json 文件中，我们可以简单地在本地访问它们，或者从 json 文件中读取。

# 从本地数据库读取:

```
import pymongo
from pymongo import MongoClient
client = MongoClient('mongodb://localhost:27017/')
db = client.politics
speeches = db.speeches
```

如果我们已经有了数据库，但是它存储在本地，下面是我们如何在 python 中访问它。我们需要导入 pymongo 和 MongoClient，然后访问我们的数据库，然后是我们想要的集合。在这里，我们当前的集合，speeches，现在保存为名为`speeches`的变量，此时我们可以随意循环。

# 从 JSON 文件中读取:

```
import json
with open('speeches.json') as f:
    speeches = json.load(f)
```

如果我们的数据存储为 json 文件，我们可以简单地导入 JSON，然后使用 with open 命令并以这种方式访问我们的数据。这里，我们的数据现在保存在变量`speeches`中，我们可以根据需要循环访问。

# 从在线数据库中读取:

```
import requests
import json
speeches = requests.get("online/database/speeches/url")
```

然而，如果我们试图从网上访问数据库，我们将导入请求和 json。在这里，我们的发言被保存在变量 speechs 中，我们可以通过使用类似于`for i in speeches.json()`的东西来循环它们:

此时，如果我们已经导入了我们的演讲，无论它们在哪里，我们也需要使用类似的方法导入我们的话题信息集合。

# 循环浏览数据:

可能也有很多方法可以做到这一点，但是假设我们的主题列表不太大，我们可能很快就可以在演讲和主题信息集合中提取出我们想要搜索的关键词和主题。手动操作可能更容易。或者也有很棒的从大量数据中提取关键词的 API，这可能是一个选项。现在，让我们假设我们的演讲和主题信息的数据结构是相同的。类似这样的演讲:

## 数据库结构:

```
{"_id": "54893893",
"title":"speech on the economy",
"text":"...blab, blah, blah...",
"references": []
}
```

主题信息也有类似的结构:

```
{"_id": "HEJENSHENAH",
"topic":"speech on the economy",
"text":"...blab, blah, blah...",
}
```

我们可以在 python 中设置循环，如下所示:

## 基本 Python For 循环:

```
for s in speeches:
   speechArr = s['text'].split(" ")
   for i in speechArr:
      if i in topics:
         for info in information:
            infoArr = info['text'].split(" ")
            for x in infoArr:
                if x == i:
                  s['references'].append(info["_id"])
```

现在，可能有比我想出的更简单更有效的方法来做到这一点，但基本上我们循环通过演讲的文本，分解成一个数组。如果任何一个单词与主题中的一个单词匹配，那么我们就遍历主题信息集合，看看这些段落中的文本是否也与当前主题匹配。如果有匹配，我们将该记录的 id 添加到当前发言记录的 references 数组中。

如果我们的代码中有一个数组，我们可以将修改后的数据放入一个新的数组中，然后我们可以将它写入一个新的数据库，保存为一个新的 JSON 文件，或者用。

```
import json
with open('speeches.json') as f:
    speeches = json.load(f)newSpeeches = []for s in speeches:
   speechArr = s['text'].split(" ")
   for i in speechArr:
      if i in topics:
         for info in information:
            infoArr = info['text'].split(" ")
            for x in infoArr:
                if x == i:
                  s['references'].append(info["_id"]) newSpeeches.append(s) 
```

在这个循环中，我们还可以对集合中的每条记录做其他事情。例如，如果我们想在集合中添加字段，我们可以在 For 循环中这样做。我们可以这样做，在我们的循环中使用下面的代码行:

`i[‘new_field’] = []`

如果我们希望新字段是一个列表。然后，我们可以将数据追加到集合内的列表中。

此时，在遍历完数据之后，我们可以将新的集合推送到一个在线数据库，或者将其保存到一个本地文件中，该文件包含在我们的变量 newSpeeches 中。

# 插入本地收藏:

在前面的例子中，我提到了如何访问本地数据库。如果这是我们的方法，我们现在想将新记录插入本地数据库，我们可以这样做:

```
speechObject_id = speeches.insert_one(speechObject).inserted_id
```

我们需要确保在这里写入适当的变量。

# 保存到本地 JSON 文件:

如果我们打开了一个 JSON 文件，或者甚至使用了另一种方法，我们可能希望将新的集合保存到一个本地 JSON 文件中。我认为这是一个好主意，因为如果一个已部署的数据库发生问题，您将有一个备份。你可以这样做:

```
speeches = []with open('myspeech.json', 'w') as outfile:
    json.dump(speeches, outfile)
```

如果我们所有新引用的集合都存储在 speeches 变量中，那么我们将使用它，并用该信息编写一个新的 json 文件，显然需要在这里导入 json。

很明显，我们有很多方法可以调整这一点，可能还有很多我做错的事情，或者可以改善这个过程，所以如果你有任何反馈，或者想指出我所有的错误，请随意！谢谢！

[https://www.linkedin.com/in/ethanjarrell/](https://www.linkedin.com/in/ethanjarrell/)