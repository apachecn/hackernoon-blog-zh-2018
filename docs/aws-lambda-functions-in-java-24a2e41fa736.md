# Java 中的 AWS Lambda 函数

> 原文：<https://medium.com/hackernoon/aws-lambda-functions-in-java-24a2e41fa736>

![](img/0b65223478da37f0935834666a3eb23c.png)

# 亚马逊网络服务

在过去，支持 web 应用程序需要购买一个或多个计算机系统来充当 web 服务器。其他硬件可能包括负载平衡器，用于在 web 服务器和数据库服务器之间分配 web 应用程序负载。这些计算机系统需要安装在空调设备中。需要报警系统和其他安全措施来提供人身安全。需要购买足够的互联网带宽来支持预期的高峰使用。

除了计算机硬件和设施的资本费用外，还需要工作人员来管理计算机系统。

亚马逊网络服务(AWS)极大地降低了网络应用的成本。计算、互联网带宽和数据库服务没有前期成本(尽管预付某些服务可以省钱)。亚马逊计算和 RDS 数据库服务无需维护。

尽管 AWS 在降低 web 应用程序成本方面具有革命性，但每周 7 天、每天 24 小时(24/7)运行弹性计算云(EC2)服务器的成本仍然很高(尤其是对于一家试图节约每一分钱的初创公司而言)。对于随着 web 应用程序需求的增加而扩展的 AWS 应用程序，应用程序的成本将随着负载均衡器添加额外的服务器而增加。

运行在 web 服务器上的应用程序将分配多个服务器线程来支持应用程序用户。当这些用户执行消耗内存或处理资源的操作时，负载平衡器可以分配额外的服务器来处理负载。在新服务器可以联机处理增加的负载之前，通常有一段时间延迟。为了避免这种延迟，可以分配额外的 24/7 运行的服务器(EC2 实例)来处理高峰需求。为处理峰值负载而配置的 EC2 资源增加了 web 应用程序的成本，并且可能在大多数时间被轻度利用。

## 自动气象站λ

AWS Lambda 允许将内存或计算密集型操作卸载到 Lambda 函数。Lambda 函数具有高度的可伸缩性和并发性，可以快速满足高峰需求。Lambda 函数缩放不会影响 web 服务器，也不会导致向 web 应用程序添加额外的 EC2 资源。

根据 Lambda 函数的调用频率，成本可能非常低(在某些情况下，没有成本)。例如，在撰写本文时，Amazon Lambda 的[免费层(例如，零成本)允许每月 100 万次调用和 400，000 千兆字节秒的计算。Lambda 的免费层对新的和现有的 AWS 客户都是无限期可用的。](https://aws.amazon.com/lambda/pricing/)

AWS Lambda 可用于支持各种 AWS 云功能。例如，Lambda 可用于提供移动应用服务，消除对网络服务器的需求(支持所谓的“无服务器计算”)。Lambda 函数也可以作为远程过程调用、卸载内存或计算密集型操作来调用。这是这里讨论的用例。

# Java 中的 Lambda 函数

关于 Lambda Java 函数的 AWS 文档主要关注为响应定义的 AWS 事件而调用的 [Lambda 函数。例如，当在 DynamoDB 表中创建条目时，或者当文件存储在 AWS S3 存储中时，可以调用 Lambda 函数。尽管事件驱动的 Lambda 函数是 Amazon 文档中经常描述的用例，但 Lambda 函数并不一定要在响应事件时被调用。Lambda 函数可以通过远程调用来调用。](https://docs.aws.amazon.com/lambda/latest/dg/invoking-lambda-function.html)

亚马逊为 Eclipse 提供了 [*AWS 工具包，这使得构建 Java Lambda 函数并将其下载到 Lambda 服务变得很容易。该工具包正确地构建并下载 Lambda 函数的代码，以及所有必需的 Java“jar”文件。*](https://docs.aws.amazon.com/toolkit-for-eclipse/v1/user-guide/getting-started.html)

下面显示了一个简单的 Lambda 函数，它回显其输入的注释副本。

```
**package** com.amazonaws.lambda.lambda_example;

**import** **com.amazonaws.services.lambda.runtime.Context**;
**import** **com.amazonaws.services.lambda.runtime.RequestHandler**;

/**
 * <p>
 * SimpleLambdaFunctionExample
 * </p>
 * <p>
 * An example of a minimal Lambda function that returns an annotated argument string.
 * </p>
 * <p>
 * Feb 16, 2018
 * </p>
 * 
 * @author Ian Kaplan, iank@bearcave.com
 */
**public** **class** **SimpleLambdaFunctionExample** **implements** RequestHandler<SimpleLambdaMessage, String> {
    **public** **final** **static** String lambdaFunctionName = "SimpleLambdaFunctionExample";

    **@Override**
    **public** String **handleRequest**(SimpleLambdaMessage message, Context context) {
        **final** String resultStr = "Returned from AWS Lambda. Input string was " + message.getMessage();
        **return** resultStr;
    }

}
```

Lambda 函数的参数必须始终是一个 Java 对象，该对象可以序列化为一个 [JSON](https://www.json.org/) 字符串。在这种情况下，Java 对象是如下所示的 SimpleLambdaMessage 对象。

```
**package** com.amazonaws.lambda.lambda_example;
/**
 * <h4>
 * SimpleLambdaMessage
 * </h4>
 * <p>
 * A class for sending message strings to an Amazon Web Services Lambda Function
 * </p>
 * <p>
 * Feb 19, 2018
 * </p>
 * 
 * @author Ian Kaplan, iank@bearcave.com
 */
**public** **class** **SimpleLambdaMessage** {
    **private** String mMessage;

    /**
     * @param message the message contents for the object
     */
    **public** **void** **setMessage**(**final** String message) {
        **this**.mMessage = message;
    }

    /**
     * @return the message string
     */
    **public** String **getMessage**() {
        **return** **this**.mMessage;
    }

    **@Override**
    **public** String **toString**() {
        **return** **this**.mMessage;
    }

}
```

当用 AWS 工具包配置 Eclipse 时，可以选择 SimpleLambdaFunction.java 文件并“右键单击”以显示 Eclipse 菜单。其中一个菜单项是“亚马逊网络服务”，其中包括一个子菜单，条目为“向 AWS Lambda 上传功能”。选择此项将调用一组对话框将函数下载到 Lambda。在将函数下载到 Lambda 之前，您需要设置 AWS IAM 权限和 S3 存储来支持 Lambda 函数。这样做的细节超出了本文的讨论范围。

要从 Java web 应用程序(或 jUnit 测试代码)调用函数，您需要构建一个 AWS Lambda 客户端，并将函数参数转换为 JSON。下面的代码显示了一个示例。要运行这段代码，您需要填写自己的 Amazon ID 和 Lambda 服务的密钥。

```
**package** com.amazonaws.lambda.lambda_example;
**import** **java.io.IOException**;
**import** **java.nio.ByteBuffer**;
**import** **java.nio.charset.StandardCharsets**;
**import** **java.util.logging.Logger**;

**import** **org.junit.Test**;

**import** **com.amazonaws.auth.AWSStaticCredentialsProvider**;
**import** **com.amazonaws.auth.BasicAWSCredentials**;
**import** **com.amazonaws.protocol.json.SdkJsonGenerator.JsonGenerationException**;
**import** **com.amazonaws.regions.Regions**;
**import** **com.amazonaws.services.lambda.AWSLambda**;
**import** **com.amazonaws.services.lambda.AWSLambdaClientBuilder**;
**import** **com.amazonaws.services.lambda.model.InvokeRequest**;
**import** **com.amazonaws.services.lambda.model.InvokeResult**;
**import** **com.amazonaws.services.s3.model.Region**;
**import** **com.fasterxml.jackson.databind.JsonMappingException**;
**import** **com.fasterxml.jackson.databind.ObjectMapper**;

/**
 * A simple test harness for invoking your Lambda function via a remote Lambda call.
 */
**public** **class** **LambdaFunctionTest** {

    /**
     * Build an Amazon Web Services Lambda client object.
     * 
     * @param accessID The AWS credential ID
     * @param accessKey The AWS credential secret key
     * @return An AWS Lambda client
     */
    **private** AWSLambda **buildClient**(String accessID, String accessKey) {
        Regions region = Regions.fromName(Region.US_West.toString());
        BasicAWSCredentials credentials = **new** BasicAWSCredentials(accessID, accessKey);
        AWSLambdaClientBuilder builder = AWSLambdaClientBuilder.standard()
                                         .withCredentials(**new** AWSStaticCredentialsProvider(credentials))
                                         .withRegion(region);
        AWSLambda client = builder.build();
        **return** client;
    }

    /**
     * See http://www.baeldung.com/jackson-inheritance
     * 
     * @param obj
     * @return
     * @throws JsonGenerationException
     * @throws JsonMappingException
     * @throws IOException
     */
    **private** **static** String **objectToJSON**( Object obj, Logger logger) {
        String json = "";
        **try** {
            ObjectMapper mapper = **new** ObjectMapper();
            json = mapper.writeValueAsString(obj);
        } **catch** (JsonGenerationException | IOException e) {
            logger.severe("Object to JSON failed: " + e.getLocalizedMessage());
        }
        **return** json;
    }

    **@Test**
    **public** **void** **testLambdaFunction**() {
        **final** String aws_access_key_id = "Your AWS ID goes here";
        **final** String aws_secret_access_key = "Your AWS secret key goes here";
        **final** Logger logger = Logger.getLogger( **this**.getClass().getName() );
        **final** String messageToLambda = "Hello Lambda Function";
        **final** SimpleLambdaMessage messageObj = **new** SimpleLambdaMessage();
        messageObj.setMessage(messageToLambda);

        AWSLambda lambdaClient = buildClient(aws_access_key_id, aws_secret_access_key);
        String lambdaMessageJSON = objectToJSON( messageObj, logger );
        InvokeRequest req = **new** InvokeRequest()
                               .withFunctionName(SimpleLambdaFunctionExample
                               .lambdaFunctionName)
                               .withPayload( lambdaMessageJSON );
        InvokeResult requestResult = lambdaClient.invoke(req);
        ByteBuffer byteBuf = requestResult.getPayload();
        **if** (byteBuf != **null**) {
            String result = StandardCharsets.UTF_8.decode(byteBuf).toString();
            logger.info("testLambdaFunction::Lambda result: " + result);
        } **else** {
            logger.severe("testLambdaFunction: result payload is null");
        }
    }
}
```

默认情况下，Eclipse 会将 Lambda 项目构建为 Maven 项目。要编译代码来调用 Lambda 函数，您需要包含以下 Maven 依赖项(当然，Java SDK 的版本会随着时间的推移而变化):

```
<dependency>
           <groupId>com.amazonaws</groupId>
           <artifactId>aws-java-sdk</artifactId>
           <version>1.11.253</version>
</dependency>
```

# 测试和调试 Lambda 函数

像 AWS Lambda 这样的服务允许将内存和处理负载转移到 Lambda，这将随着负载的增加而扩展。Lambda 还允许用最少的服务器端资源构建移动应用程序。Lambda 的吸引人的特性、亚马逊的营销和软件行业的从众心理导致了“无服务器计算”的流行。大多数写无服务器计算奇迹的人并不深究这样一个事实，即基于 Lambda 的应用程序有一个更老的名字:分布式应用程序。

众所周知，分布式应用程序很难调试，因为应用程序组件运行在通过计算机网络(在 Amazon“虚拟专用云”网络的情况下)连接的不同计算机系统上。

关于 Lambda 函数执行和错误的信息只能通过检查 Lambda 执行日志来获得。作为 Lambda 函数参数的上下文对象有一个相关联的记录器，可用于记录错误和执行状态。

```
LambdaLogger logger = context.getLogger();
```

通过读取 Lambda 执行日志来诊断 Lambda 函数错误的难度应该会促使谨慎的开发人员在将运行在 Lambda 上的代码集成到应用程序之前，尽可能彻底地对其进行测试。应该编写本地运行的 Java jUnit 测试来测试在 Lambda 下运行的代码。一旦这些测试通过，就应该编写在 Lambda 下运行代码的测试。只有当本地和远程 Lambda 测试通过时，Lambda 代码才应该集成到应用程序中。

# 用 AWS Lambda 处理 Java 图像

AWS Lambda 的一个常见用例是从较大的数字照片中生成缩略图和缩放图像的图像处理。将图像处理从 web 服务器转移到 Lambda 函数可以消除图像处理操作可能导致的内存和处理器峰值。这降低了 web 服务器的负载，并允许应用程序在更少的 web 服务器上运行。Lambda 函数将平滑缩放，并且可以支持多个 web 服务器线程中的图像处理操作。

地下社交网络(由 Topstone Software 设计和建造)用户可以在他们的板上和照片库中发布照片。当照片被下载到地下时，会为缩略图和中等比例的照片生成照片的新版本。

当照片库功能被添加到 nderground 时，额外的服务器被添加到 AWS Elastic Beanstalk 负载平衡器来管理内存和处理负载。通过添加 Lambda 函数来缩放图像，我们能够减少服务器的数量，从而降低了地下工程的每月成本。

# GitHub 上的 Java 图像缩放软件

Topstone software 在 GitHub 上发布了 Java 图像缩放软件(见[IanLKaplan/LambdaImageProcessing](https://github.com/IanLKaplan/LambdaImageProcessing))。该软件在 Apache 2 开源许可下可用。

*此文原载* [*此处*](http://topstonesoftware.com/publications/lambda_functions.html) *在* [*托普斯通软件咨询*](http://www.topstonesoftware.com) *网站上。*