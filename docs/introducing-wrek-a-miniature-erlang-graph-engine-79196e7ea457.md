# 介绍 Wrek——一个微型 Erlang 图形引擎

> 原文：<https://medium.com/hackernoon/introducing-wrek-a-miniature-erlang-graph-engine-79196e7ea457>

由 [**理查德·卡洛**](https://github.com/rkallos)

[Wrek](https://github.com/rkallos/wrek) 是我为并发执行任务依赖图编写的 Erlang 库。它的预期目的是运行一组预定义的任务，这些任务之间有一个偏序。在这篇文章中，我解释了我为什么写 wrek，它可以用来做什么，以及如何使用它。

# 动机

雷克的出现是两种截然不同的力量的结果。首先，我对图论的业余爱好使我试图在任何可能的地方看到图形，为这个库奠定了概念基础。其次，我意识到我在 Adgear 工作的一个项目将受益于这样一个库，这促使我最终开始编写 Wrek。

# 概念的

图是计算中普遍存在的数据结构。当我在学校了解到图算法时，我惊讶于它的广泛应用。图在编译器和构建系统中扮演着重要的角色。各种图形算法构成了我们在互联网上相互交流的基础。

我们的日常生活往往充满了清单。我们有任务清单、购物清单、食谱、清单、说明书等等。有一天，我意识到这些名单有些是骗人的。其中一些列表实际上是隐藏在列表外衣下的图表；这些列表是[有向无环图](https://en.wikipedia.org/wiki/Directed_acyclic_graph)的[拓扑排序](https://en.wikipedia.org/wiki/Directed_acyclic_graph)。两个更明显的例子是待办事项清单和食谱。你不能寄一封还没写的信，也不能煮一壶水就做意大利面。

一旦这些鬼鬼祟祟的图表被发现，我就花了一些时间思考如何从对待各种列表中获益，就像它们实际上是 Dag 一样。这些清单和 Dag 之间最明显的相似之处是待办事项清单和食谱。这些非常相似的[依赖图](https://en.wikipedia.org/wiki/Dependency_graph)。与列表表示不同，这些依赖图显示了哪些顶点(单个任务)可以并发执行。有时这是显而易见的(例如:我可以在等水烧开的时候切菜！当第一批饼干在烤箱里的时候，我可以开始准备第二份烤饼干了！)，但是我希望将列表重新表示为依赖图的行为可以暴露更多并发的机会。

```
 Boil water -- Add pasta -- Cook pasta --.
                                                         \
  Purée tomatoes --.                                      \
                    \                                      \
  Chop vegetables -- Combine in saucepan -- Simmer sauce -- Combine pasta and sauce -- Serve
                    /
      Add spices --'
```

上图中顶点之间的边表示偏序。之间没有路径的顶点可以同时执行。图中顶点之间的关系反映了厨房里的真实情况。我们可以边煨调味汁边煮意大利面。我们是先切蔬菜还是先切西红柿泥并不重要；如果我们要做调味酱的话，它们都需要做。

尽管我尽了最大努力，我在厨房里仍然是一个灾难。作为改变这一不方便的事实的一种方法，我认为尝试用有向图来表示食谱会很有趣，并带有额外的数据，如每一步预计需要多长时间。这件事在我的“将来某一天的项目”清单上放了很长时间，只有当我开始思考我在$JOB 工作的一个项目时，我才会想起它。

# 实际的

我去年在 Adgear 完成的一个项目是，移除我们每台已经达到极限的边缘服务器上正在进行的昂贵计算，并用一个在单台机器上执行昂贵计算的系统取而代之，然后分发结果。这个系统在没人碰它的时候工作，但它的程序相当于树枝和胶带；cron 作业和 shell 脚本。这个系统使用起来仍然很痛苦，在 CPU 资源匮乏的机器上进行昂贵计算的例子越来越多。此时，开始编写一个更健壮的系统是有意义的。

这些昂贵的计算被很好地分解成要执行的步骤列表。获取这些数据，进行转换，再进行一些转换，将一些数据发送到这组服务器，将另一些数据发送到另一组服务器。Shell 脚本非常擅长编码这些管道，所以在实现时这是一个可以接受的选择。过了一会儿，我明白了，这些步骤列表并不是真正的列表；它们是依赖图。我试图通过添加一些后台作业和 waitpid 来揭示 shell 脚本中潜在的并发性，但是最终决定改用 Erlang 并从 OTP 提供的所有功能中获益会更有意义。

# 让我们开始吧

(不好意思。我无法抗拒。)

Wrek 接受像上面的意大利面条食谱这样的依赖图作为输入，并执行每个顶点。只要能够执行，并发执行动作的本质对于任何可以由依赖图表示的问题都是通用的。依赖图的结构，以及图的每个顶点所涉及的任务都是根据用户的愿望而特定的。遵循与检察官办公室相同的通用/专用划分；这类问题的一般部分由`wrek`和`wrek_vert`模块解决。特定部分由用户以描述图中每个顶点的 Erlang 图的形式提供，以及一组实现`wrek_vert` 行为的回调模块。

`wrek_vert` 行为由一个回调`run/2`组成，其中第一个参数是要发送给回调函数的参数列表，第二个参数是可以提供其他顶点生成的信息的进程的 ID。这个回调函数的预期结果是

`{ok, Any :: any()}` 或`{error, Reason :: any()}`。如果回调函数成功，`Any`将被 wrek 获取，并可供其他`wrek_vert`进程使用。如果回调函数崩溃或返回一个错误，整个图形将被关闭。

# 做二郎面

按照上面这个虚构的例子，让我们开始使用 wrek 制作意大利面。当然，我们的程序不会真的做出意大利面，但是它的输出应该会愚弄一些人。

看上面的图表，每一步似乎做三件事之一:

1.  添加配料
2.  在容器中混合配料
3.  对容器中的配料做些什么

让我们继续为每个动作写一些`wrek_vert`。如果你不喜欢使用文本编辑器，完整的代码可以在这里找到。

```
-module(cook_add).-behaviour(wrek_vert).
-export([run/2]).run([Ingredient, Quantity], _Pid) ->
 io:format(“adding ~s. amount: ~s.~n”, [Ingredient, Quantity]),
 {ok, #{added => [{Ingredient, Quantity}]}}.
```

`cook_add`到此为止。它打印一条消息，然后生成一个添加了一个键的 map，该键的值是一个只有一对的 proplist。

```
-module(cook_heat).-behaviour(wrek_vert).
-export([run/2]).run([Verb, Noun], _Pid) ->
    io:format("~ping ~p.~n", [Verb, Noun]),
    {ok, #{}}.
```

`cook_heat`也挺短的。也很抽象。它可以用来打印任何关于`Verb`的信息，而不仅仅是烹饪原料！

我们的最后一个回调模块稍微长一点，因为它做的比打印消息多一点。

```
-module(cook_combine).-behaviour(wrek_vert).
-export([run/2]).run([Ingredients, Vessel], Pid) ->
    Fun = fun(Step, Acc) ->
              Stuff = wrek_vert:get(Pid, Step, added),
              io:format("combining ~p with ~p in ~p.~n", [Stuff, Acc, Vessel]),
              Stuff ++ Acc
          end,
    Stuff = lists:foldl(Fun, [], Ingredients),
    io:format("~p now contains: ~p.~n", [Vessel, Stuff]),
    {ok, #{added => Stuff}}.
```

`Ingredients`应该是顶点名称的列表。我们最后使用父进程的 Pid 作为`wrek_vert:get/3`的参数。这让我们消费由`cook_add` 回调模块产生的数据。在组合所有东西之后，我们返回一个新的成分集合。

好吧！我们差不多已经描述完了问题的具体部分！最后一步是根据这些回调模块和我们要传递给它们的参数来表示我们的依赖图。

```
-module(wrek_example).-export([make_pasta/0]).make_pasta() ->
    application:ensure_all_started(wrek),
    Graph = #{
      tomatoes => #{
        module => cook_add,
        args => ["pureed tomatoes", "1 can"],
        deps => []
       },
      vegetables => #{
        module => cook_add,
        args => ["chopped vegetables", "lots"],
        deps => []
       },
      spices => #{
        module => cook_add,
        args => ["spices", "to taste"],
        deps => []
       },
      saucepan => #{
        module => cook_combine,
        args => [[tomatoes, vegetables, spices], saucepan],
        deps => [tomatoes, vegetables, spices]
       },
      simmer_sauce => #{
        module => cook_heat,
        args => [simmer, sauce],
        deps => [saucepan]
       },
      boil_water => #{
        module => cook_heat,
        args => [boil, water],
        deps => []
       },
      add_pasta => #{
        module => cook_add,
        args => ["pasta", "1 handful"],
        deps => [boil_water]
       },
      cook_pasta => #{
        module => cook_heat,
        args => [cook, pasta],
        deps => [add_pasta]
       },
      mix_pasta_with_sauce => #{
        module => cook_combine,
        args => [[saucepan, add_pasta], saucepan],
        deps => [simmer_sauce, cook_pasta]
       }
     },
    wrek:start(Graph).
```

真是满眼都是！我们在这里所做的是创建一个 Erlang 映射，它的键表示我们原始依赖图中顶点的名称，它的值是指定回调模块、传递给回调模块的参数以及顶点可能具有的任何依赖关系的映射。我祝贺那些注意到我们从不过滤意大利面的人；我承认在厨房里是个灾难。我保证我吸取了教训。

好了，我们完成编码了！让我们做一个贝壳，做一些意大利面吧！

# 在 [codesync.global](http://www2.erlang-solutions.com/l/23452/2018-02-26/5bm6dr) 上阅读这篇博文的其余部分

![](img/0540daea6bee9bbed40a59748d6510e7.png)

Code BEAM SF 2018: 15–16 March 2018

加入 CodeBEAM SF 的 AdGear 开发人员 Richard Kallos，他将在这里发表关于 **wrek** 的演讲。

*最初发布于*[*codesync . global*](http://www2.erlang-solutions.com/l/23452/2018-02-26/5bm6dr)*。*