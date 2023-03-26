# Spring 中的 Feign Client 入门

> 原文：<https://medium.com/hackernoon/getting-started-with-feign-client-in-spring-9ab33d0be152>

在本教程中，我们将看看 *FeignClient* 以及如何在 Spring Boot 应用程序中使用它。

*FeignClient* 是一个以声明方式创建 REST API 客户端的库。因此，我们声明一个客户端定义，其余的在运行时生成供使用，而不是手动为远程 API 编写客户端代码，或者使用 Springs *RestTemplate* 。

# 应用程序

我们将构建一个小的命令行应用程序，它模拟我们之前创建的看板 API 的完整测试。示例应用程序将创建一个新用户，登录，检索所有的董事会和注销用户。它捕获了最常见的用例(POST、GET、DELETE + AuthN)

# 先决条件

在本教程中，我们将使用我们的看板 API 教程项目。RESTful API 展示在[看板后端](http://kanbanbackend.com/)上，我们将使用运行的默认实现。

我将在相应的章节中介绍与本教程相关的端点。完整的 API 文档，见[此处](https://kanbanbackend.herokuapp.com/swagger-ui.html)。

看板 API 是我在 Learnletter 上运行的一个教程，你可以在博客中找到所有的部分。

# 添加依赖关系

我们在本教程中使用 Maven。由于我们不想弄乱版本号，最简单的方法是在 Maven POM 的 *dependencyManagement* 中包含 Spring Cloud 设置。

```
<dependencyManagement> 
  <dependencies>
    <dependency>
      <groupId>org.springframework.cloud</groupId>  
      <artifactId>spring-cloud-dependencies</artifactId> 
      <version>Finchley.SR1</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

现在，我们可以用一个经典的 Spring Boot 启动器将依赖关系添加到 *Feign* 中:

```
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-openfeign</artifactId> </dependency>
```

# FeignClient 基础知识

Feign 客户端使用声明性方法来访问 API。要使用它，我们必须首先在我们的 Spring Boot 应用程序上启用 Spring Cloud 对它的支持，在一个 *@Configuration* 类的类级别上使用 *@EnableFeignClients* 注释。

```
@SpringBootApplication
@EnableFeignClients 
public class FeignIntroductionApplication implements ApplicationRunner 
{ //omitted 
}
```

下一步是声明一个接口来访问我们的 API。我们将其命名为 *KanbanClient* ，因为它将提供调用我们的远程 API 的方法。

```
@FeignClient(name="KanbanClient", 
             url= "https://kanbanbackend.herokuapp.com/") 
public interface KanbanClient { }
```

要把它变成一个虚拟客户端，我们必须在接口上设置 *@FeignClient* 注释，用 *name* 属性给它命名，并且用 *url* 属性设置远程 URL。这里支持 SpEL，所以我们可以将值外部化到属性文件中。除了 URL，我们还可以在这里使用 Eureka 来使用服务发现。然而，这超出了本教程的范围。

要定义一个远程调用，我们必须声明一个方法，并使用 Spring MVC 的一些注释，这些注释通常用在服务器端的 *@Controller* 上。它们的行为是一样的，只是现在在客户端。

所以，让我们从增加功能开始。

# 拨打 POST 电话

在一个方法上添加 *@PostMapping* 注释并在其中传递一个参数，会将该方法转换为 POST 调用。在注释中，我们提供了相对于我们在 *@FeignClient* 注释中设置的 URL 的端点。

```
@PostMapping(value = "/register") String registerUser(User user);
```

*用户*是一个简单的 POJO，有一个*用户名*和*密码*字段。Feign，Spring 会自动把它转换成 JSON。

# 拨打接听电话

同样的原理，但是我们在方法上使用了 *@GetMapping* 。我们还发送认证头。API 使用 *X-Auth-Token* 。

```
@GetMapping("/boards")
List<Board> listBoards(
  @RequestHeader("X-Auth-Token") 
  String authHeader
);
```

*/boards* 端点将返回用户所有看板的列表。*板卡*是该板卡的 POJO，只包含 *id* 和*名称*。

# 拨打看跌电话

同样的，只是这次用了一个 *@PutMapping* 注释。

```
@PutMapping("/board/{id}")
Board changeBoard(
  @RequestHeader("X-Auth-Token") String authHeader, 
  @PathVariable("id") Long id, 
  Board board
);
```

我们可以通过对棋盘的端点进行 PUT 来更改棋盘的名称( */board/{id}* )。关于路径变量，请参见下面的*节使用变量*进行调用。

# 拨打删除电话

有点无聊，但它看起来和另一个一样，只是有一个 *@DeleteMapping* 注释。

```
@DeleteMapping("/unregister")
ResponseEntity<Void> unregisterUser(
  @RequestHeader("X-Auth-Token") String authToken, 
  Confirmation user
);
```

该端点需要一个带有用户密码的*确认*对象来删除帐户，如果成功，还将返回一个 200。

# 使用变量进行调用

如果我们的端点需要一个基于实体的变量，比如 ids，我们可以在方法参数上使用 *@PathVariable* 注释。它的行为与 Spring MVC @控制器相同。

然后可以在 *@PutMapping(* 上的端点声明中使用定义的变量，就像:

```
@PutMapping("/board/{id}") 
Board changeBoard(
  @RequestHeader("X-Auth-Token") String authHeader, 
  @PathVariable("id") Long id, 
  Board board
);
```

# 带认证的呼叫

为此，我们将使用*登录*端点。它需要用户凭证作为基本身份验证发送，并将返回一个令牌用于进一步的身份验证。

```
@PostMapping("/login")
ResponseEntity<Void> loginUser(
  @RequestHeader("Authorization") String authHeader
);
```

将附加信息作为 header down 传递的第一种方法是添加一个带有 *@RequestHeader* 注释的方法参数。参数的值将被设置为注释中定义的 HTTP 头的值。

在认证的情况下，是*授权*头。作为一个值，我们给它基本的 auth 编码字符串。

在对看板 API 的后续调用中，我们将使用带有令牌的 *X-Auth-Token* 头。

响应头不能直接作为方法返回值返回，但是我们可以使用 Spring 的 *ResponseEntity* ，它是一个响应包装器。

当我们成功调用 */login* 端点时，它将在响应头中返回 auth 令牌。该方法将类似于:

```
@PostMapping("/login")
ResponseEntity<Void> loginUser(
  @RequestHeader("Authorization") String authHeader
);
```

需要将 *Void* 作为参数化类型，因为我们的端点在响应体中不返回任何内容。

我们在看板 API 中使用 Spring Session，所以授权令牌通过 *X-Auth-Token* 头进行交换。

为了检索我们称之为:

```
String token = response.getHeaders().getFirst("X-Auth-Token");
```

*响应*的类型为*响应实体*。

# 认证备选方案

我们总是可以在每个方法上使用 *@RequestHeader* 注释来传递认证头。然而，还有一种全局指定的替代方法。

像 Spring MVC 一样，Feign 也有一个拦截器的概念，可以用来在远程调用之前做一些特定的事情。入口点是 *RequestInterceptor* 接口。

使用 Spring，我们只需要提供一个 Bean 来实现 Spring 上下文的特定接口，它就会被自动拾取。

示例:

```
@Bean 
AuthInterceptor authFeign() { 
  return new AuthInterceptor(); 
}class AuthInterceptor implements RequestInterceptor { 
  @Override 
  public void apply(RequestTemplate template) { 
    template.header("Authorization", "<your token>"); 
  }
}
```

我们的拦截器是一个 Spring Bean，因此我们可以使用 Spring 的强大功能，将 authN 信息外部化到属性中，甚至可以从会话范围的 Bean 中检索它，我们在每个用户的基础上保存信息。

# 使用 FeignClient

现在，我们可以像任何其他 Spring Bean 一样将 *KanbanClient* 注入到我们的代码中。在启动时，Spring Cloud 将为我们设置 Feign 客户端，并给我们一个常规的 Spring 代理，这样我们就可以简单地开始使用远程端了。

# 我们的最终客户

我们使用 Feign 的客户最终看起来是这样的:

```
@FeignClient(name="KanbanClient", 
             url= "https://kanbanbackend.herokuapp.com/")
public interface KanbanClient { @PostMapping(value = "/register") 
  String registerUser(User user);  @DeleteMapping("/unregister")
  ResponseEntity<Void> unregisterUser(
    @RequestHeader("X-Auth-Token") String authToken, 
    Confirmation user
  ); @PostMapping("/login") 
  ResponseEntity<Void> loginUser(
    @RequestHeader("Authorization") String authHeader
  ); @GetMapping("/boards")
  List<Board> listBoards(
    @RequestHeader("X-Auth-Token") String authHeader
  ); @PostMapping("/boards") 
  Board createBoard(
    @RequestHeader("X-Auth-Token") String authHeader,
    Board board
  ); @PutMapping("/board/{id}")
  Board changeBoard(
    @RequestHeader("X-Auth-Token") String authHeader, 
    @PathVariable("id") Long id,
    Board board
  );
}
```

# 示例应用程序

[全工作样本](https://github.com/azarai/spring-boot-tutorials-codeboje/tree/master/feign-introduction)在 GitHub 上。

它将注册一个新用户，登录，创建一个董事会，改变董事会的名称，列出所有董事会，然后在最后注销用户。

*最初发布于*[*codeboje . de*](http://codeboje.de/getting-started-feignclient/)*。*