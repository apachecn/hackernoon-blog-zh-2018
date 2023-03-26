# 编程的疯狂

> 原文：<https://medium.com/hackernoon/the-madness-that-is-programming-cba8794f0995>

![](img/daf97407f56f9ee15a902d2b36308f14.png)

Hereee’s a problem!

每个人都喜欢大骂典型工作日的无休止的苦差事。会议，同事打扰，设备不足，Reddit..感觉时间都花在了其他事情上，除了手头的任务。

但是让我们暂时把这些放在一边。你在你的区域。视线中没有分心或干扰。Reddit 也不行。我们花大量的时间不是写代码，而是在浏览器上寻找答案。你不想问的问题的答案。最愚蠢的事情可能需要几天才能解决，让你发疯，让你思考你的职业选择。但是我们在找什么呢？为什么任何时候都有 50 个标签打开，其中一半是堆栈溢出？

我最近从头到尾写了一个 [app](https://github.com/ration/betman) 想回答这个问题。该项目是一个开发者的梦想，因为它完全是绿地，只有一个人(我)负责，我有一个非常具体的功能列表要做，它们不太可能改变或增加。我做了完整的堆栈(Kotlin、Spring、Bootstrap、Angular)——从项目创建到在 Docker 容器中部署到云中的一切。这花了大约两个月的日历时间，在我的新生儿允许的情况下，大多是一两个小时。

栈是典型的，因为它包含了一些我非常熟悉的东西(Spring)，一些知识有限的东西(Kotlin，Angular，Bootstrap)到全新的领域，比如 Google Cloud 和 Spring Webflux。它也有一些我纯粹出于学术兴趣添加的元素(JWT 令牌，暴露)。

那么时间去哪里做这样的东西呢？基本上所有的编程任务都是从分而治之开始的——你设计整体架构，然后将问题分成更易管理的部分，并开始一个接一个地解决它们。数据库。UI 视图。REST 请求处理。认证。CSS。开着开着。对于任何有经验的程序员来说，这种类型的划分就像是第二天性，当你遇到这种情况时，你会觉得生活是值得的。然后是惊喜。“我的应急威士忌在哪里”——那些让你拔光头发的东西(我是秃头)。

不应该是问题的事情需要两天才能解决，这种问题让你抓狂。例子不管用。构建突然失败。配置选项似乎不起作用。对您的依赖关系发布的补丁打破了一切。奇怪的错误代码。

![](img/744524c41e3918db2db321dbecfe7ce9.png)

[xkcd 1024](https://xkcd.com/1024/)

出于兴趣，我写下了路上遇到的最令人惊讶的问题，这些问题花了两个多小时才解决，让我更加接近疯狂的边缘。其中许多可能是他们自己的帖子，并为下一个傻瓜留一些头发，但我将简要介绍其中一些和决议。

## JWT 与 Spring Boot 的代币没有用

有[指令](https://auth0.com/blog/implementing-jwt-authentication-on-spring-boot/)存在，但是会话数据没有保存在 Spring 中。根据 Spring，不管我如何认证自己，我总是匿名用户。我对 Spring 请求处理的内部进行了最长时间的调试，但毫无结果，直到我弄明白为止。事实证明，使用@Autowire 将 Spring [SecurityContext](https://docs.spring.io/spring-security/site/docs/4.2.4.RELEASE/apidocs/org/springframework/security/core/context/SecurityContext.html) 传递给类并不是一个好主意。每次使用*security context holder***。**

**经验教训**:如果你在使用库的时候遇到了麻烦，并且足够幸运地拥有所有的代码，那么看看这些代码。盯着它调试足够长的时间，你可能会发现它。

也许我应该试试汽车修理工的工作？

## 如何从公开的事务中返回数据？

( [Exposed](https://github.com/JetBrains/Exposed) 是 Kotlin 的 ORM)。在该框架中，您将数据库操作封装到如下事务中:

```
transaction {
 val dao = UserDao.find{ name eq someName }
 dao.password = newPass
 return toPojo(dao) // **Error
}**
```

我想从那个事务中返回一些东西(带有更新数据的 POJO)，但是不允许使用 return 关键字。我甚至不知道该用什么谷歌来解决这个问题。

**经验教训** : Kotlin 高阶函数返回最后一条语句，而那个事务只是一个带有 lambda 参数的方法。所以最后一个例子是:

```
transaction {
 val dao = UserDao.find{ name eq someName }
 dao.password = newPass
 toPojo(dao) **}**
```

出于某种无法解释的原因，我的大脑就是无法与科特林 T14 兰姆达斯 T15 联系起来，我以为这是某种别的魔法。我真正需要做的是查看如何定义*事务*功能，并正确地将其内部化。

> 没有魔法这种东西，只有你不懂的东西。如果魔法失效了，你就停下来，在使用它之前想清楚。疯狂地搜索你不理解的问题的解决方案不会让你有任何进展。

也许有机会在酝酿？

## 弹簧布线中的角度布线误差

在 Spring 中，您使用*WebSecurityConfigurerAdapter*类来声明如何解析每个 URI 的安全规则，这可以绕过会话认证等。但是如果我使用角度路由，即不同的控制器具有不同的 URIs。斯普林生气了，吐出了错误。

吸取的教训:事实证明你需要这样的东西:

这确保所有请求(不匹配其他 Spring 路由规则)被转发到*index.html*，并从那里转发到正确的角度控制器。谷歌会给你各种过时的不再工作的版本。

或许是水管工？

## Spring JUnit 测试困境

我同时遇到了两个问题，Spring 上下文加载没有按照我想要的方式运行，自动布线字段也不稳定。例如，上下文没有加载用 YML 编写的配置文件，尤其是在集成测试中。字段注入在测试环境中不太适用。

**经验教训:**结果[你不能在测试属性](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/context/TestPropertySource.html)中使用 YML(查看支持的格式)。另外[如果可以避免的话不要注入磁场](https://www.vojtechruzicka.com/field-dependency-injection-considered-harmful/)。更好的是，如果可以的话，在测试中完全避免 Spring。

垃圾人？

## 通过配置属性进行配置

与前一个部分相关的是，[配置属性](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)，虽然很酷，但是工作起来却非常痛苦。神奇的因素又一次有点太高了——定义的配置 POJO 经常只说 null，我不知道该怎么办。他们不在单元测试中工作也没有帮助。

吸取的教训:有时你只能做无休止的修补。

蒸馏怎么样？

## 公开的多表查询

再次暴露了困境。我不知道如何编写涉及多个表的查询。最后我问了[栈溢出](https://stackoverflow.com/questions/50143775/how-do-i-search-from-multiple-tables-with-dao)得到了答案。

**经验教训:**生成 SQL 查询 DAO 框架魔术在设计上是失败的。在我开始之前我就有这种感觉，现在我又来了。也许这次他们会成功，我对自己说。没有。您头脑中有一个干净快速的 SQL 查询，但是您花了几个小时无休止地修改 DAO 层，希望它能正确地生成查询，并且不会慢得吓人。安卓[房间](https://developer.android.com/topic/libraries/architecture/room)之类的东西简直就是高人一等。

或许是酿酒？

## 因果报应拒绝和幻像一起工作

如果我运行 PhantomJS 浏览器引擎，单元测试会失败。我会得到类似`[[object ErrorEvent]](https://github.com/karma-runner/karma/issues/2852)`的东西，测试会随机失败。

**经验:**JavaScript 生态系统一团糟。但是我已经知道了。

![](img/f2800757d3927c4354b26f55a00b6777.png)

I wonder if you can even throw a newer flat screen through a window or will just bounce

有组织犯罪呢？

## 霍克的安全漏洞

我不知道 Hoek 是什么，但是 Github 抱怨说它有一个漏洞，所以我花了很多时间来解决这个问题。这个问题已经解决了，但我不认为它已经解决了。

**经验教训:** JavaScript 一团糟。您的依赖关系中的安全问题也总是您的问题。

住在桥下靠社保过活？

## 角度 5->6

在我的开发过程中，Angular 6 发布了。在我无限的智慧中，我认为升级应该是轻而易举的，我只有几百行代码，对吗？我也认为我会咬紧牙关，不使用兼容层，因为代码基数很小。我运行了自动升级程序，然后…出了点问题。这里有一个列表:

*   我大量使用 Observable.of 和 Rx 流操作符，两者都没有正确升级。尤其是[流管()变](https://github.com/ReactiveX/rxjs/blob/6.2.0/MIGRATION.md#pipe-syntax)很痛苦。
*   进口货被胡乱摆弄了一番。我觉得他们只是在耍我们。他们知道这打破了一切，对不对？
*   WebStorm 失去了运行测试的能力

**经验教训** : **使用任何最新最棒的东西时都要格外小心。如果我只是等待事情解决，也许所有这些都可以避免。**

种田？

## TypeScript 编译错误

突然，有棱有角的一切都崩溃了。我有:

```
ERROR in src/app/auth.interceptor.ts(6,2): error TS2345: Argument of type 'typeof AuthInterceptor' is not assignable to parameter of type '({ providedIn: Type<any> | "root"; } & ValueSansProvider)| ({ providedIn: Type<any> | "root"; } ...'.Type 'typeof AuthInterceptor' is not assignable to type '{ providedIn: Type<any> | "root"; } & ClassSansProvider'.Type 'typeof AuthInterceptor' is not assignable to type '{ providedIn: Type<any> | "root"; }'.Property 'providedIn' is missing in type 'typeof AuthInterceptor'.
```

谷歌没有提供任何帮助。是的，错误在 *auth.interceptor.ts* 的第 6 行，但是我没有看到。我重写了这个类，并通过比较结果来找出错误。我遗漏了@ Injectable 注释中的括号。

**吸取的教训:**休息一会儿我马上就看出了错误。检查你的基本面当“你好世界！”水平的东西不管用。

![](img/c960981aaa6fede80c90a0b9a43725f5.png)

或许是流浪汉？

## Kotlin 中的 Json 列表类型

我在将 JSON 列表映射到 Kotlin Pojos 时遇到了麻烦。基本上[这个问题](https://stackoverflow.com/questions/39679180/kotlin-call-java-method-with-classt-argument)。虽然我确实弄明白了，但它看起来如此丑陋和古怪，我选择了 B 计划，只是改变了 JSON。

**经验教训:**变通办法永远是一种选择。

公园管理员？

## Gradle 4 的 Spring 集成测试

我没有运气做春季集成测试，我得到的是:

```
java.lang.NoClassDefFoundError: org/hamcrest/SelfDescribing
```

我到处看，所有的东西都静止不动，毫无用处。

**经验:**您可以随时打开更多搜索标签。没完没了的谷歌搜索让我找到了正确的魔法:

```
configurations {
   integrationTestImplementation.extendsFrom testImplementation 
   integrationTestRuntimeOnly.extendsFrom testRuntimeOnly
}
```

这仍然是魔法。我甚至不知道读什么来解除它。

职业高尔夫球手？这在 37 岁时仍然是一个选择，对吗？

## 我的 Rest 控制器返回 HTML

我的一个 rest 端点顽固地返回 HTML 而不是 JSON。我费力的翻了一遍注释，也看不懂是怎么回事。我添加了各种接受和返回类型的注释，但都不起作用。

我没有注意到这个类是用@Controller 注释的，而不是正确的@RestController。叹气。

**吸取的教训:**又有了基本面。

建筑？我听说它们很受欢迎。

## 如果用户没有访问权限，如何返回 403

Spring 有一个名为[*UserDetailsService*](https://docs.spring.io/spring-security/site/docs/4.2.6.RELEASE/apidocs/org/springframework/security/core/userdetails/UserDetailsService.html)的接口，可以用来定义如何从数据库加载用户。但我不知道如何区分认证和授权，即用户是已知的，但不允许在该页面中。我一无所获。我在前端破解了一个解决方案。

**经验教训:**失败永远是一个选项。

保姆？哦，等等，我已经做了。

## 电脑崩溃

我的旧 Surface Pro 3 在压力下开始变形，所以我买了一台新电脑。我的单元测试执行时间从 15 秒变成了 2 秒。不再有完全的 IntelliJ 冻结。我可以在 Docker 容器中同时运行 WebStorm、IDEA 和应用程序，并且仍然有多余的内存。

吸取的教训:假设你有钱，用钱解决问题永远是个好主意。

![](img/d6a6465952547eba150582311330e03d.png)

Urge to kill fading..

## 角度路线参数

我需要在控制器之间发送参数。当然有[向导在那里](https://angular-2-training-book.rangle.io/handout/routing/routeparams.html)，但是魔法因素还是太高了。感觉就像从示例中偏离了一毫米，突然`route.params`是空的或者什么的。这对于因果报应测试来说更是如此。为什么这个[路由测试模块](https://angular.io/api/router/testing/RouterTestingModule)现在是必需的，而以前不是这种东西。尤其是如何设置控制器的参数似乎大相径庭。我最终把它分解成这样的东西，把参数传递给控制器:

```
TestBed.*configureTestingModule*({
  declarations: [GroupComponent],
  imports: [FormsModule, RouterTestingModule.*withRoutes*([]), HttpClientTestingModule],
  providers: [GroupsService, UserService, AlertService, AuthenticationService,
    [GroupComponent, {
      **provide: ActivatedRoute,
      useValue: {snapshot: {params: {'group': '123'}}}**
    }]]
}).*compileComponents*();
```

这是你能想到的最难看的了，因为它通过了所有的测试，但是，嘿，它很有效。

**吸取教训:**再次使用魔法。出血边缘魔术与有限的文件。

还有更多，但我想你明白了。

![](img/663d8b1a24081b2034c8f432e175cfa8.png)

I wish I were Jack