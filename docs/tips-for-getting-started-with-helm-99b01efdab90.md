# Helm 入门提示

> 原文：<https://medium.com/hackernoon/tips-for-getting-started-with-helm-99b01efdab90>

您有一个想要用 Helm 部署的应用程序。大概你已经看到了[官方文件](https://docs.helm.sh/)和一些例子。但是你不确定如何将它们应用到你的应用程序中。这里有一些提示。

# 1 了解你的码头工人形象

首先为您的应用程序准备一个 docker 图像，并确保您可以使用它。首先使用普通的 Kubernetes 描述符进行部署，或者从 docker-compose 开始。

当您使用普通 k8s 描述符完成所有工作后，您将需要考虑如何使部署更加灵活。您可能希望为不同的配置或不同的环境传递部署开关。这是介绍赫尔姆的好时机。

# 2 了解价值观文件的作用

为了确保这一点是清楚的，让我们提醒自己舵轮图的[结构](https://www.nclouds.com/blog/simplify-kubernetes-deployments-helm-part-2/):

```
├── Chart.yaml
├── README.md
├── templates
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── **deployment.yaml**
│   ├── **secrets.yaml**
│   └── **...more yaml...**
└── **values.yaml**
```

当用户运行`helm install`时，图表中 values.yaml 的条目和 helm 发布信息(比如惟一的发布名称)被注入到模板化的 yaml 资源描述符中，因为模板被评估并呈现为纯 Kubernetes 部署描述符。当用户使用参数或值文件运行`helm install`时，参数或值文件将覆盖在图表中的文件上。实际上，图表的值文件设置了可以被覆盖的默认值。

如果这还不清楚，试试看。创建一个名为 mysql-values.yaml 的文件:

```
imageTag: “5.7.10”
```

然后跑`helm install stable/mysql --values=mysqlvalues.yaml`。Helm 为我们生成了一个唯一的版本名(` ignore-camel ` ), MySQL 部署在集群中。`kubectl describe pod ignorant-camel-mysql-5dc6b947b-lf6p8` 的输出告诉我们，我们选择的`imageTag`已经被应用。

# 尝试一些东西

当您完成安装和覆盖值后，尝试创建一个基本图表。您可以使用`helm create`来获得一个您可以修改的起始图表——在官方文档中有一个关于如何做到这一点的示例[。试试看。](https://docs.helm.sh/chart_template_guide/)

或者在网上找到一个部署应用程序的指南，该应用程序使用的语言与您正在使用的语言相同。如果你能找到一个与你的应用足够相似的图表示例，那么你可以复制粘贴并以此为起点。甚至有一些工具可以给你入门的例子——比如 Jenkins-X 就有这些，它称之为[草稿包](https://github.com/jenkins-x/draft-packs)。

# 4 从简单开始

大多数图表是为单个 docker 图像设计的。如果你有一个微服务应用程序，它有几个相互作用的部分，你可以选择你认为可以独立部署的应用程序的一小部分，并尝试为此编写一个掌舵图。

# 5 从环境变量和``if`` 条件开始

大多数图表都包括将环境变量注入 Pod 中的 docker 容器。所以你会在一个`deployment.yaml`中看到这样的东西:

```
- name**:** ENV_VAR1
  value: {{ .Values.var1 }}
- name**:** ENV_VAR2
  value: {{ .Values.var2 }
```

这样，图表的用户可以在他们的 values.yaml 中或者用`—set var1=foo` 覆盖这些值。

通常还有控制是否启用配置的选项。因此，您可能会发现类似这样的内容:

```
- name**:** ENV_VAR1
  value: {{ .Values.var1 }}
{{- if .Values.var2 }}
- name**:** ENV_VAR2
  value: {{ .Values.var2 }
{{- end }}
```

那么只有在为`var2`设置了值的情况下，才会设置`ENV_VAR2` 。或者你可以

```
- name**:** ENV_VAR1
  value: {{ .Values.var1 }}
{{- if .Values.var2enabled }}
- name**:** ENV_VAR2
  value: {{ .Values.var2 }
{{- end }}
```

那么只有当`var2enabled` 为真时`ENV_VAR2` 才会被设置。

`if` 指令是一个容易理解的舵[控制结构](https://github.com/helm/helm/blob/master/docs/chart_template_guide/control_structures.md#ifelse)，允许你做很多事情。

# 6 你可以使用不同的值文件

我们已经看到，我们可以创建一个定制的`mysqlvalues.yaml`文件，并将其传递到官方的 mysql 图表中。我们也可以用自己的图表做到这一点。如果我们有不同的配置要应用于不同的环境，例如转移和生产，这将非常方便。

# 7 期望调试

带`--dry-run --debug`选项的舵安装非常有用。它不会真正部署您的图表，但它会运行部署中涉及的检查，因此您会知道自己是否做了一些根本错误的事情。它还显示了生成的 Kubernetes 描述符。这可以让你检查你的图表是否如你所愿。

# 8 先不要担心发布

最终，您可能会想要[打包](https://github.com/helm/helm/blob/master/docs/helm/helm_package.md)您的图表，并在[图表存储库](https://github.com/helm/helm/blob/master/docs/chart_repository.md)中发布它们。但是，只有当你希望别人能够消费你的图表并在它们的基础上构建时，才需要这样做。首先，你可以只从源代码开始工作，因为这会使事情变得更容易。如果它们在源代码控制中，那么其他人可以像您一样将它们从源代码控制中签出，直到您建立了图表存储库。

# 9 了解版本名称

我们之前看到过，当我们安装 mysql chart Helm 时，它为我们创建的安装生成了发布名称“ignored-camel”。helm 将这个发布名称注入到 helm 从我们的图表中生成的 kubernetes 部署描述符中。这确保了部署的每个资源都有一个惟一的名称([和 kubernetes 需要它来](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/))，即使我们多次安装图表。发生这种情况是因为模板包含如下内容:

```
apiVersion: v1
kind: Service
metadata:
  name:  {{ .Release.Name }}-myservice
```

所以`{{ .Release.Name }}`是一个占位符，Helm 将在其中注入发布名称。对于许多图表，您更可能会看到这种情况:

```
apiVersion: v1
kind: Service
metadata:
  name: {{ template "fullname" . }}
```

这相当于在图表中的`_helpers.tpl`中定义了模板全名函数，并使用发布名称和图表名称。

当您开始在图表中部署需要相互引用多个资源时，您需要意识到这一点。如果资源是在您的图表中定义的，那么您需要注意通过使用相同的函数来使用定义中使用的相同名称。因此，如果您想将一个环境变量注入到包含上述服务名称的 Pod 中，那么您可以:

```
- name: SERVICE_NAME
  value: {{ template "fullname" . }}
```

# 10 互动很棘手

假设你有一堆微服务，它们作为一个应用程序协同工作。如果服务只是一起使用，不需要单独部署，这可能是一个大图表。但最有可能的是你需要能够分开。因此，棘手的是如何为每个部分建立单独的图表，并让它们相互交流。

所以你会想要使用[子图](https://github.com/helm/helm/blob/master/docs/chart_template_guide/subcharts_and_globals.md)。这使我们可以在图表中包含其他图表，以创建复合/伞状图表。我们通过引用想要包含在`requirements.yaml`中的图表并将其包含在图表的`charts` 目录中来实现这一点。如果我们包含一个我们自己定义的(还没有发布)叫做`mysubchart`的图表，我们可以把它放在需求中。

```
- name: mysubchart
  version: 0.1.0
```

并且把`mysubchart` (它的整个目录)的整个源代码放到我们伞图的`charts` 目录里。如果我们包括一个官方/出版的图表，那么我们会在需求中包括它的出版位置。yaml:

```
- name: postgresql                         
  repository: [https://kubernetes-charts.storage.googleapis.com](https://kubernetes-charts.storage.googleapis.com)
  version: 0.11.0
```

然后，我们将在部署图表之前执行`helm dep build`。

当图表拥有需要引用其他子图表的资源时，事情就变得有点棘手了。您需要知道您正在寻找的资源的名称。这些名称通常是动态生成的，包括一个版本名称。

希望图表能在其文档中清楚地说明这一点，但您可能需要查看其源代码。例如，[官方 postgresql 图表](https://github.com/helm/charts/tree/master/stable/postgresql)可以创建一个包含我们为 postgresql 实例设置的密码的秘密。如果我们在应用程序中使用 postgresql(将其添加为子图表),那么我们需要引用它并将其注入到环境变量中。

我们可以看看这个[秘密是如何定义的](https://github.com/helm/charts/blob/master/stable/postgresql/templates/secrets.yaml)或者参考[例子](https://github.com/helm/charts/blob/f7cfc026e69a5b5109a5ba97caf9b4115659d57b/stable/xray/templates/xray-persist-deployment.yaml#L54)它是如何被使用的，我们需要使用:

```
— name: POSTGRESS_PASSWORD
  valueFrom:
    secretKeyRef:
      name: {{ .Release.Name }}-postgresql
      key: postgres-password
```

这是可行的，因为 postgres 图表包含在我们的图表中。它的资源是同一个版本的一部分，所以使用同一个版本名。

这些引用的复杂性可能会增加，这取决于图表需要如何交互以及它们将如何被使用。例如，如果您需要支持随图表一起创建 postgres 或者指向一个现有的 postgres，该怎么办呢？或者提供切换到 mysql 的选项？这种事情会变得复杂。官方图表[显示了处理这种情况的模式](https://hackernoon.com/the-art-of-the-helm-chart-patterns-from-the-official-kubernetes-charts-8a7cafa86d12)，但是你最好从限制选项开始，这样一开始你只需要处理简单的情况。

# 11 跨图表复制可以接受

每个图表用于部署一个或多个特定的 docker 映像。(同一图像的不同标签或版本通常由同一图表处理。)但是如果你有几个非常相似的图像呢？我们可以在不同的图表中重用部分吗？

官方 helm 文档给了[一个例子](https://github.com/helm/helm/blob/master/docs/chart_template_guide/subcharts_and_globals.md#overriding-values-from-a-parent-chart)，告诉你如何从父图表“继承”特性。它定义了一个名为`mychart`的图表，在其`charts`目录中包含一个名为`mysubchart`的子图表。`mysubchart`的 values.yaml 包含:

```
dessert: cake
```

该值进入由`mysubchart`创建的配置映射资源。然后，mychart 的 values.yaml 可以用以下内容覆盖它:

```
mysubchart:
  dessert: ice cream
```

所以`mychart`可以定义由`mysubchart`定义的相同的配置图，因为它包括`mysubchart`，但是它可以通过覆盖这些值给它不同的内容。

因此，您可以使用一个基础图表来定义公共资源，并让其他图表包含该图表，并根据需要覆盖它们。例如，您可以想象有一个 java 应用程序的基础图表。然而，您目前并没有看到这种模式以那种方式被大量使用。实际上，许多官方的掌舵图都包含了许多图表中几乎相同的部分。批次以同样的方式处理入口。他们复制，而不是试图重用一个共同的父节点。(实际上，他们试图广泛地遵循一个共同的指导方针。)为什么？

一个原因是 helm 没有抽象父代的概念。每个图表，包括父图表，都可以单独部署。如果不是，那么测试起来就有点棘手了。

在面向对象语言中，子图表更像是图表中可重写的成员，而不是父图表。每个都必须有一个前缀(在我们的例子中是`mysubchart`)。如果你有多个层，这些名字会变得很难用。您不能修改子图表中的资源，除非它们通过值公开。这意味着必须去改变子图表，以便改变消耗它的图表，这在图表被发布时变得更加笨拙，因为每个改变都是版本改变，并且需要重建和发布。

此外，如果有一个定义入口的公共父类，那么它将需要引用被公开的服务。这意味着必须将服务名注入入口资源。由于父图表实际上是一个包含的第三方图表，这将带来我们在上一节中看到的所有复杂的交互。

这并不是说我们不应该试图避免重复。如果我们有几个一起部署的图表，并且都需要相同的环境变量集，那么使用 [globals](https://github.com/helm/helm/blob/master/docs/chart_template_guide/subcharts_and_globals.md#global-chart-values) 或指向 [common ConfigMap](https://dzone.com/articles/configuring-java-apps-with-kubernetes-configmaps-a) 的选项会很有意义。用名为模板的[来减少模板内容的重复也是可能的，但是这有点复杂(尽管这里的例子](https://github.com/helm/helm/blob/master/docs/chart_template_guide/named_templates.md)[是](https://stackoverflow.com/questions/43923749/kubernetes-how-to-avoid-duplicating-env-in-multiple-deployments))。

关键是，如果你发现重复是一个问题，你可以先复制内容，然后再减少重复，这样你就可以省去一些麻烦。(希望在 Helm3 中这部分会[变得更流畅，因为该区域正在](https://github.com/helm/helm/issues/3920)[讨论中](https://github.com/helm/helm/issues/4535)。)

# 12 你不必马上自动测试你的舵

无论如何，你可能是在部署之后测试你的应用程序。如果是这样，不要觉得你必须完全 TDD 你的图表部署过程。Helm 确实有这方面的选项，使用它们会很棒，但还需要学习。

# 13 把赫尔姆当成一盒戏法没关系

许多程序员习惯于遵循既定的设计模式。从这个角度来看，你很快就会看到掌舵[“提示和技巧”页面](https://github.com/helm/helm/blob/master/docs/charts_tips_and_tricks.md)或[模板函数的文档](https://github.com/helm/helm/blob/master/docs/chart_template_guide/functions_and_pipelines.md#template-functions-and-pipelines)。接受这一点。官方的 Kubernetes 图表中有很多变化。以适合你的方式做事是可以的。

# **14 请随意使用官方图表**

官方图表处理的是你不会想到的事情，它们被维持着。因此，如果你需要一个 mysql 数据库或者任何你能在官方图表中找到的东西，去使用它，而不是为此创建你自己的图表。

请注意，[官方图表](https://hub.kubeapps.com/)相当复杂，因为它们必须在许多方面可用。不要觉得你需要钻研他们的代码，马上效仿他们，[，尽管你以后可能会从中受益。最初，你可以根据自己的情况使用它们。每个都应该有如何使用它的解释。](https://hackernoon.com/the-art-of-the-helm-chart-patterns-from-the-official-kubernetes-charts-8a7cafa86d12)

# 15 使用 Helm 社区

如果你加入了[官方的 kubernetes slack](http://slack.k8s.io/) ，那里有舵和舵图的频道。你可以在那里得到很好的建议。也不要害怕在 [stackoverflow](https://stackoverflow.com/) 上提问。你并不孤单。

# 外卖食品

![](img/18ff27695732d184da352f3e824bfd0e.png)

[Getting the hang of Helm now](https://vimeo.com/127298945)

Helm 有很多东西需要学习，它与许多程序员习惯的方法有很大不同。分解一下，一步一步来。你兴风作浪的速度会比你想象的要快。