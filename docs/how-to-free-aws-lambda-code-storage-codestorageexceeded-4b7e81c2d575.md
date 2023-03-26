# 如何释放 AWS Lambda 代码存储(CodeStorageExceeded)

> 原文：<https://medium.com/hackernoon/how-to-free-aws-lambda-code-storage-codestorageexceeded-4b7e81c2d575>

![](img/b3803dda51f793ceb965105b21303664.png)

您已经使用 AWS Lambda 运行了几个月，突然您得到以下错误:

> ***出现错误:TestDashdeliveryLambdaFunction—超出代码存储限制。(服务:AWSLambda 状态码:400；错误代码:CodeStorageExceededException 请求 ID:05d 3a e68-e7f 6–11e 8–948 e-41c 27396380 e)。***

# 有什么问题？

对于高达 75GB 的[Lambda 函数，AWS 限制了它在内部 S3 上节省的“代码存储量”。](https://docs.aws.amazon.com/lambda/latest/dg/limits.html)

虽然听起来空间很大，但是你很容易达到那个极限。如果您使用的是[无服务器框架](http://serverless.com/)，那么它的默认设置是[为您进行的每个部署](https://serverless.com/framework/docs/providers/aws/guide/functions/#versioning-deployed-functions)存储一个版本。

# 解决问题

如果您不需要为每个部署存储一个版本(像我们中的许多人一样)，您可以通过在您的 *serverless.yml* 文件中添加以下内容来轻松地取消它:

```
provider:
  name: aws
  runtime: python3.6
  versionFunctions: false
  region: ${opt:region, 'us-east-1'}
  stage: ${opt:stage, 'dev'}
```

添加 *versionFunctions: false* 参数将取消版本存储。

# 清除-λ-存储:自动清除旧版本

如果您确实希望保留旧版本(例如，为了能够快速回滚)，您将需要手动清理旧版本。幸运的是，在 Epsagon，我们喜欢共享开源工具(例如，[发现死函数](https://epsagon.com/blog/the-curse-of-dead-lambda-functions/))！这次是[透明λ存储](https://github.com/epsagon/clear-lambda-storage)。听起来很简单，它将负责从每个 Lambda 函数和每个区域中删除旧的和未使用的版本(即，既不是当前部署的也不是$LATEST)。运行它非常简单:

```
git clone https://github.com/epsagon/clear-lambda-storage
cd clear-lambda-storage/
pip install -r requirements.txt
python clear_lambda_storage.py
```

它将输出已经删除了多少个版本，从多少个函数中删除了多少个版本，以及释放了多少 MB:

```
Deleted 9412 versions from 502 functions
Freed 42056 MBs
```

你觉得这个工具有用吗？随时要求额外的功能，并贡献自己。

快乐的无服务器⚡！

*原载于 2018 年 12 月 10 日 epsagon.com**T21*[。](https://epsagon.com/blog/how-to-free-aws-lambda-code-storage-codestorageexceeded/)