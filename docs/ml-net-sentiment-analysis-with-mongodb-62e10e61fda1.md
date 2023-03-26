# 用 MongoDB 进行 ML.NET 情感分析

> 原文：<https://medium.com/hackernoon/ml-net-sentiment-analysis-with-mongodb-62e10e61fda1>

今年早些时候(2018 年 5 月)，微软宣布了[ML.NET](https://www.microsoft.com/net/learn/apps/machine-learning-and-ai/ml-dotnet)，这是一个开源的跨平台机器学习框架，专为。NET 开发人员。能够将定制机器学习与集成是一个令人兴奋的消息。NET/C#应用程序。尽管在撰写本文时，ML.NET 仍处于预览版 0.5.0 中，但您可以对其进行测试，以探索该框架的潜在能力。

![](img/caaf0bb6421770438dd5cca77c49dcad.png)

ml.net open source and cross-platform machine learning framework

微软和第三方已经为 ML.NET 提供了许多教程。然而，示例数据源大多是 TSV 格式的**文件*(制表符分隔值)。这篇文章是为 JSON 格式的大量可用数据集、web 事件的非结构化数据集或者已经存储在 MongoDB 中的数据集而写的。*

*这篇文章将关注如何使用存储在 [MongoDB](https://www.mongodb.com/) 中的数据开发 ML.NET 分类情感分析。这篇文章基于微软的[教程:在情感分析二进制分类中使用 ML.NET](https://docs.microsoft.com/en-gb/dotnet/machine-learning/tutorials/sentiment-analysis)，有显著的不同:*

*   *训练数据集是 JSON 格式的。*
*   *它从 MongoDB 中读取数据作为数据源，而不是文件。*
*   *它使用。NET Core (Ubuntu/Linux)。*

*完整的代码示例和数据可以在[github.com/sindbach/mlnet_mongodb](https://github.com/sindbach/mlnet_mongodb)上找到。我建议查看微软的教程以获取更多信息。*

# ***数据***

*一个好的机器学习之旅总是从一个好的数据集开始。使用的数据集来自 [Yelp 数据集挑战](https://www.yelp.com/dataset/challenge)。这些数据由 Yelp 提供，作为他们数据集挑战的一部分，将于 2018 年 12 月 31 日结束。数据大小约为 2.9GB，最重要的是，是 JSON 格式的。*

*感兴趣的数据集的一部分在*yelp _ academic _ dataset _ review . JSON*文件中。情感分析模型将根据 Yelp 评论进行训练，以预测评论的情感是积极还是消极。*

*以下是文件中的 JSON 结构示例:*

```
**{
     "business_id": "iCQpiavjjPzJ5_3gPD5Ebg",
     "cool": 0,
     "date": "2011-02-25",
     "funny": 0,
     "review_id": "x7mDIiDB3jEiPGPHOmDzyw",
     "****stars****": 2,
     "****text****": "The pizza was okay. Not the best I've had. I prefer Biaggio's on Flamingo / Fort Apache. The chef there can make a MUCH better NY style pizza. The pizzeria @ Cosmo was over priced for the quality and lack of personality in the food. Biaggio's is a much better pick if youre going for italian - family owned, home made recipes, people that actually CARE if you like their food. You dont get that at a pizzeria in a casino. I dont care what you say...",
     "useful": 0,
     "user_id": "msQe1u7Z_XuqjGoqhB0J5g"
}**
```

*从结构上看有两个重要的字段:*文字*和*星星*。*文本*字段包含用户的评论，而*星号*字段包含评论是否正面的指示。*

# ***数据库***

*将审查数据加载到数据库中的时间。在这篇文章中，数据将被加载到 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) 中，这是一个面向 MongoDB 的云托管数据库即服务。如果你也想测试数据加载，你可以按照 MongoDB 的教程[创建一个 Atlas 自由层](https://docs.mongodb.com/manual/tutorial/atlas-free-tier-setup/)。*

*可以使用 mongoimport 将数据[加载到 MongoDB Atlas 中。例如，以下命令将名为*yelp _ academic _ dataset _ review . JSON*的文件导入到 **yelp** 数据库中的 **review** 集合中:](https://docs.atlas.mongodb.com/import/mongoimport/)*

```
**mongoimport --uri "mongodb+srv://user:pwd@dataset-demo.mongodb.net/yelp" --collection review ./yelp_academic_dataset_review.json**
```

*一旦导入完成，使用 [mongo shell](https://docs.mongodb.com/manual/mongo/) 或 [MongoDB Compass](https://www.mongodb.com/products/compass) 来检查数据。*

*![](img/e33cbd6e9ba34b3faed9fe41451ff159.png)*

*MongoDB Compass Document View*

*在开始编写代码之前，还有一项准备工作需要完成。因为我们试图创建一个二进制分类，我们需要一个二进制值来确定一个评论是正/ 1 还是负/ 0。幸运的是，每个文档都有一个星级，从 1 到 5，1 表示负面评价，5 表示正面评价。*

*[MongoDB 聚合管道](https://docs.mongodb.com/manual/core/aggregation-pipeline/)可用于向数据集中添加一个名为**情感**的新字段，该字段的值基于星级。情感值将按照以下逻辑确定:任何星级值大于 3 的评论都是正面的，任何等于或小于 3 的评论都是负面的。*

*例如，使用 [$addFields](https://docs.mongodb.com/manual/reference/operator/aggregation/addFields/) 阶段添加新字段，使用 [$out](https://docs.mongodb.com/manual/reference/operator/aggregation/out/) 阶段将输出存储到单独的集合中:*

```
**db.review.aggregate([
           {“$addFields”:
              {“sentiment”:
                  {“$cond”:
                      {“if”:{“$gt”:["$stars", 3]}, 
                       “then”: 1, 
                       “else”: 0
           }}}},
           {"$out":"review_train"}]
);**
```

*![](img/fb6a367514984ed5c6ad8fbe45c021b5.png)*

*MongoDB Compass Aggregation Pipeline Builder*

***注**:你还可以在[github.com/sindbach/mlnet_mongodb:数据](https://github.com/sindbach/mlnet_mongodb/tree/master/data)上找到一小部分 JSON 数据。训练数据由 5000 条正面评论和 5000 条负面评论组成。*

# ***代码***

*这个帖子将使用[。NET Core](https://en.wikipedia.org/wiki/.NET_Core) ，一个[免费开源](https://en.wikipedia.org/wiki/Free_and_open-source) [托管](https://en.wikipedia.org/wiki/Managed_code) [框架](https://en.wikipedia.org/wiki/Software_framework)用于[Windows](https://en.wikipedia.org/wiki/Microsoft_Windows)[MAC OS](https://en.wikipedia.org/wiki/MacOS)和 [Linux](https://en.wikipedia.org/wiki/Linux) 。项目仅有的两个依赖项是:*

*   *[MongoDB。NET/C#驱动程序](http://mongodb.github.io/mongo-csharp-driver/)版本 2.7.0*
*   *ML.NET 版本 0.5.0*

*对*sentitementdata*类进行如下修改，以序列化和/或反序列化来自 MongoDB 的评审文档结构:*

```
*[BsonIgnoreExtraElements]
public class SentimentData
{
    [BsonId]
    [BsonRepresentation(BsonType.ObjectId)]
     public string Id {get; set;}
     [BsonElement("sentiment")]
     public float Label { get; set; }
     public string text { get; set; }
}*
```

**BsonIgnoreExtraElements*忽略文档中除了 *id* 、*情感*(映射到标签)和*文本*之外的所有字段。这些是我们将用于培训的字段。接下来，我们实例化一个 *MongoClient* 对象，使用连接字符串 URI 连接到 MongoDB:*

```
*static string mongoURI = "mongodb+srv://usr:pwd@dataset-demo.mongodb.net";
static readonly MongoClient client = new MongoClient(mongoURI);*
```

*使用 *MongoClient* 对象，我们可以访问 **yelp** 数据库和 **review_train** 集合中的数据:*

```
*var db = client.GetDatabase("yelp");
var collection = db.GetCollection<SentimentData>("review_train");*
```

*ML.NET[*学习管道*](https://docs.microsoft.com/en-us/dotnet/api/microsoft.ml.learningpipeline?view=ml-dotnet) 需要一个可枚举的对象，我们可以通过调用集合上的 *Find()* 轻松获得该对象:*

```
*var documents = collection.Find<SentimentData>(new BsonDocument()).ToEnumerable();
pipeline.Add(CollectionDataSource.Create(documents));*
```

*为了测试情感分析模型，我们将获取显示在 [Yelp 上的澳大利亚悉尼餐馆的四条当前评论](https://www.yelp.com.au/c/sydney/restaurants):*

*   *服务很差，咖啡质量也很差。等了这么久甚至已经试图催他们了”*
*   **“这个地方太神奇了！！我吃了经典的奶酪汉堡和薯条。毫无疑问，这是我吃过的最好的汉堡。**
*   *“如果我能给零颗星，我会的。价格高得吓人。在煮熟的澳洲鲳鱼上晒干，不加任何调料或味道。*
*   **“菜单很小，但食物相当不错。它又快又简单，是这个地区较好的选择之一。我们吃了海鲜叻沙和海鲜炒面。**

*预测结果是:*

```
*Sentiment Predictions---------------------Sentiment: Very bad service and low quality of coffee too. Waiting for so long even tried to rush them already. | Prediction: **Negative**Sentiment: This place is amazing!! I had the classic cheese burger with fries.  Hands down the best burger I have ever had | Prediction: **Positive**Sentiment: If I could give zero stars I would. Terribly overpriced. Dried over cooked barramundi with no seasoning or flavor at all | Prediction: **Negative**Sentiment: Small menu but the food is quite good. It's fast and easy, one of the better options around the area. We had the seafood laksa and seafood Pad Kee Mao | Prediction: **Positive***
```

***注**:你可以在[github.com/sindbach/mlnet_mongodb:感悟](https://github.com/sindbach/mlnet_mongodb/tree/master/sentiment)上找到完整的代码示例。*

*将 MongoDB 作为 ML.NET 数据源加载和读取数据是非常简单的。利用 ML.NET 将机器学习与存储在 MongoDB 中的数据集集成的潜力令人兴奋，我期待着 ML.NET 的未来版本。*