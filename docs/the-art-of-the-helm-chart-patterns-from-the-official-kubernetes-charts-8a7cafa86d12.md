# 舵图的艺术:来自官方 Kubernetes 图表的模式

> 原文：<https://medium.com/hackernoon/the-art-of-the-helm-chart-patterns-from-the-official-kubernetes-charts-8a7cafa86d12>

Helm charts 打包应用程序以安装在 Kubernetes 集群上。安装舵图有点像运行安装向导。因此，helm chart 开发人员面临着开发安装程序的开发人员所面临的一些同样的挑战:

*   对于安装运行的环境，可以做出哪些假设？
*   该应用程序是否意味着能够与其他应用程序进行交互？
*   需要向用户提供哪些配置，应该如何向用户提供这些配置？

但这些问题伴随着特定的舵扭曲。要了解原因，让我们从用户运行“helm install”时会发生什么开始。然后我们可以继续看一些官方的 Kubernetes 图表如何处理这些问题。

# 一个头盔安装的图片

我想在我的集群中安装 mysql。但我不希望 stable/mysql chart 在官方 charts repo 的 values.yaml 文件中设置[的 mysql 版本。所以我创建了自己的 values.yaml 文件，名为 mysql-values.yaml，只有一行代码:](https://github.com/helm/charts/blob/fce283ced152e4eed93da8574022a5bcaff9b98d/stable/mysql/values.yaml#L5)

```
imageTag: “5.7.10”
```

然后我跑`helm install stable/mysql --values=mysqlvalues.yaml`。Helm 为我生成了一个唯一的版本名(` ignore-camel ` ), MySQL 部署在我的集群中。`kubectl describe pod ignorant-camel-mysql-5dc6b947b-lf6p8` 的输出告诉我，我选择的`imageTag`已经被应用。

实际上，我不需要在集群中安装任何东西来查看我对`imageTag`的选择是否会被应用。我可以运行`helm install stabe/mysql --values=mysqlvalues.yaml --dry-run --debug`，helm 会简单地向我展示它生成的 kubernetes 部署描述符 yaml 的内容，而不需要安装任何东西。

通过思考 helm 图表的[结构](https://www.nclouds.com/blog/simplify-kubernetes-deployments-helm-part-2/)，可以更好地理解获得生成的 kubernetes 部署描述符的过程:

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

当用户运行`helm install stable/mysql`时，图表中 values.yaml 的条目和 helm 发布信息(比如惟一的发布名称)被注入到模板化的 yaml 资源描述符中，因为模板被评估并呈现为纯 Kubernetes 部署描述符。当用户使用参数或值文件运行`helm install stable/mysql`时，参数或值文件将覆盖在图表中的文件上。实际上，图表中的值文件设置了可以被覆盖的默认值。

所以 values.yaml 是我们作为图表开发人员和用户之间的主要接口。我们在 values.yaml 中公开的内容决定了用户可以对我们的图表做什么，不可以做什么。

我们的图表和 values.yaml 文件也需要满足另一种场景。另一个图表开发者可能会喜欢我们的应用程序，并决定将它包含在他们想要发布的包中。因此，他们将我们的图表添加到他们创建的新图表的 requirements.yaml 中，还有一堆其他的图表。他们通过设置*他们的*值. yaml 来覆盖我们的一些缺省值，然后他们把它分发给用那个图表做`helm install`的用户。

因此，我们的图表进入其他图表，它们反馈给图表食物链中的用户。如何在这个食物链中满足我们的消费者，是编写掌舵图的关键挑战之一。

# 写舵图的挑战

应用程序通常有许多配置选项，Kubernetes 集群可以用许多不同的方式进行配置。因此，在绘制掌舵图时，我们自然会面临这样的问题:

1.  如果我忘记在 values.yaml 中公开一个配置参数怎么办？如果我的应用程序动态地解析配置，以至于我不能提前说出所有可能的参数名称，该怎么办？
2.  我如何允许用户使用不是直接在我的图表中定义的资源，而是在我的图表被用作子图表的图表中定义的资源？
3.  如果另一个图表开发人员在他们自己的图表中使用我的图表，并且他们需要在我定义的资源中添加重要的部分(例如，向 pod 或自定义初始化脚本添加额外的容器)，该怎么办？
4.  我如何让我的用户以他们可能想要的各种方式对外公开应用程序？

我们可以从[官方舵手图表库](https://github.com/helm/charts)的图表中学到很多关于如何处理这类问题的知识。让我们来看看那些图表使用的一些模式，这样我们就可以了解如何在我们自己的图表中使用相同的模式。

在开始之前，值得记住的是，公开图表是相当先进的。如果你在制作你的第一张舵轮图时担心上面的一些问题，试着从更困难的担忧开始。从熟悉[舵文档](https://docs.helm.sh/chart_template_guide/#getting-started-with-a-chart-template)开始，试着先建立一个只适用于最简单情况的图表。您可以稍后添加[高级选项](https://github.com/helm/helm/blob/master/docs/charts_tips_and_tricks.md)。

因此，让我们试着从官方图表中获得一个模式的快照。这不会是全面的，我希望当 [helm 3](https://sweetcode.io/a-first-look-at-the-helm-3-plan/) 用 Lua 添加脚本时，新的选项会变得可用，新的模式会出现。但它会让我们看到当前的艺术水平。

# 1 公开配置参数的模式

假设我们已经在模板中定义了一个部署资源，并且我们已经配置了`env` 部分，允许从 values.yaml:

```
- name**:** ENV_VAR1
  value: {{ .Values.var1 }}
- name**:** ENV_VAR2
  value: {{ .Values.var2 }}
```

现在我们图表的用户可以在他们的 values.yaml 中或者用`—set var1=foo` 覆盖这些值。但是如果我们忽略了一个，我们的用户能做什么呢？或者更糟的是，如果我们的应用程序可以动态解析配置选项(例如，它可能读取`ENV_VAR1`并创建一个名为`var1`的内部变量)会怎么样？那么甚至没有一组有限的配置选项名称可以公开。那么，我们如何让用户设置变量名和值呢？

正如 [helm charts 开发者指南](https://docs.helm.sh/chart_template_guide/#variables)所说，我们可以创建一个带有范围函数的配置图。一个很好的例子是稳定/未绑定图表。它包含一个定义其 unbound.conf 文件的 [configmap](https://github.com/helm/charts/blob/cbd5e811a44c7bac6226b019f1d1810ef5ee45fa/stable/unbound/templates/configmap.yaml) 。它[将这个文件挂载](https://github.com/helm/charts/blob/cbd5e811a44c7bac6226b019f1d1810ef5ee45fa/stable/unbound/templates/deployment.yaml#L71)到由它的部署创建的 pod 中。在配置图中，它有条目[，如](https://github.com/helm/charts/blob/cbd5e811a44c7bac6226b019f1d1810ef5ee45fa/stable/unbound/templates/configmap.yaml#L35):

```
{{- range .Values.localRecords }}
local-data: "{{ .name }} A {{ .ip }}"
local-data-ptr: "{{ .ip }} {{ .name }}"
{{- end }}
```

和它的[值。yaml](https://github.com/helm/charts/blob/cbd5e811a44c7bac6226b019f1d1810ef5ee45fa/stable/unbound/values.yaml#L54) 让`localRecords`中的条目被设置为列表，例如:

```
localRecords:
- name: "fake3.host.net"
  ip: "10.12.10.10"
- name: "fake4.host.net"
  ip: "10.13.10.10"
```

[sonar cube 图表](https://github.com/helm/charts/blob/0445fad8d8308f089f5fb6756a5570bc6d6f0bf5/stable/sonarqube/templates/deployment.yaml#L80)将类似的方法直接应用于环境变量，明确定义一些变量，并允许使用`extraEnv`集合设置更多变量:

```
{{- range $key, $value := .Values.extraEnv }}
 — name: {{ $key }}
   value: {{ $value }}
{{- end }}
```

因此变量将被设置在 values.yaml 中，如:

```
extraEnv:
  ENV_VAR1: var1
  ENV_VAR2: var2
```

许多官方图表对 T2 的定义略有不同。 [buildkite 图表](https://github.com/helm/charts/blob/cbd5e811a44c7bac6226b019f1d1810ef5ee45fa/stable/buildkite/templates/deployment.yaml#L43)以不同的方式定义它，没有使用 range。相反，它获取 values.yaml 部分，并简单地将其放入模板中:

```
{{- if .Values.extraEnv }}
{{ toYaml .Values.extraEnv | indent 12 }}
{{- end }}
```

因此，这意味着不是将 values.yaml 中的 extraEnv 条目设置为简单的对，我们还需要将对中的每个键(`name`)和值(`value`)命名为:

```
extraEnv:
 — name: ENV_VAR1
   value: "var1"
 — name: ENV_VAR2
   value: "var2"
```

[键盘锁图表](https://github.com/helm/charts/blob/0445fad8d8308f089f5fb6756a5570bc6d6f0bf5/stable/keycloak/templates/statefulset.yaml#L67)再次以不同的方式做到了这一点:

```
{{- with .Values.keycloak.extraEnv }}
{{ tpl . $ | indent 12 }}
{{- end }}
```

因此它将 extraEnv 视为一个字符串，通过`tpl`函数发送它，以便它作为模板的一部分被评估，并确保应用正确的缩进。这很有趣，因为它允许像这样设置值:

```
extraEnv: |
 — name: KEYCLOAK_LOGLEVEL
   value: DEBUG
 — name: HOSTNAME
   value: {{ .Release.Name }}-keycloak
```

通常不可能在 values.yaml 中使用类似于`{{ .Release.Name }}`的模板指令，但是这里我们可以，因为内容将通过 `tpl`。在我们将图表包含在正在开发的父图表中，并且我们需要引用作为同一父图表一部分的服务的情况下，这可能是一个很大的优势。(下一节将详细介绍这一点。)缺点是它有点抽象，而且 values.yaml 中的内容被视为字符串，因此编辑器不一定会发现格式问题。

# 2 引用相互部署的资源

通常，通过 helm 部署的资源都带有一个版本名前缀，这样就可以从同一个图表(和同一个名称空间)安装多个版本，而不会在安装的资源之间产生命名冲突。这意味着，当图表中的一个资源需要引用另一个资源或同一父图表中的资源时，需要使用模板指令作为资源名称的前缀。

需要引用相互部署的资源的常见情况是数据库机密。例如，[x 射线图表](https://github.com/helm/charts/blob/cbd5e811a44c7bac6226b019f1d1810ef5ee45fa/incubator/xray/templates/xray-indexer-deployment.yaml#L56)中包含了部署 postgres 数据库的选项。它通过指向 postgres secret(它本身作为子图表是图表的一部分，因此也以相同的发布名称为前缀)来设置其索引器组件的部署和凭证:

```
{{- if .Values.postgresql.enabled }}
 — name: POSTGRES_USER
   value: {{ .Values.postgresql.postgresUser }}
 — name: POSTGRESS_PASSWORD
   valueFrom:
     secretKeyRef:
       name: {{ .Release.Name }}-postgresql
       key: postgres-password
 — name: POSTGRESS_DB
   value: {{ .Values.postgresql.postgresDatabase }}
 {{- else }}
...
```

这里，x 射线图表知道它需要连接到哪个数据库，因为它本身包括 postgres 图表。但是如果我们为一个用户可以选择提供数据库的应用程序开发一个图表会怎么样呢？然后，可能有必要允许用户将我们的图表包含在他们选择的数据库旁边的父图表中。那么我们如何允许用户设置数据库配置呢？

一种选择是我们已经在钥匙孔图表中看到的`extraEnv`方法。然后用户可以设置图表的`extraEnv`指向 postgres，即使我们在原始图表中没有明确定义它。values.yaml 将会有一个类似如下的条目:

```
extraEnv: |
- name: POSTGRES_USER
  value: {{ .Values.postgresql.postgresUser }}
— name: POSTGRESS_PASSWORD
  valueFrom:
    secretKeyRef:
      name: {{ .Release.Name }}-postgresql
      key: postgres-password
— name: POSTGRESS_DB
  value: {{ .Values.postgresql.postgresDatabase }}
```

这里有`|`,因为该部分被视为要显式传递给`tpl`函数的字符串。

如果需要在加载到配置映射中的文件的条目中使用发布名称前缀，也会出现类似的问题。将整个文件加载到配置图的典型方式是使用`.Files.Get`。但是，像 values.yaml 一样，以这种方式加载的文件内容不是模板的一部分，因此不能在其中使用模板指令。允许在文件中使用模板指令的是用. Files.Get 加载[文件内容，并将其传递给](https://stackoverflow.com/questions/47595295/how-do-i-load-multiple-templated-config-files-into-a-helm-chart/52009992#52009992) `[tpl](https://stackoverflow.com/questions/47595295/how-do-i-load-multiple-templated-config-files-into-a-helm-chart/52009992#52009992)`。那么配置图的数据部分将包含如下条目:

```
conf_file1: {{ tpl (.Files.Get "files/conf_file1") . | quote }}
```

或者为了载入一个秘密，我们需要用 base64 编码内容:

```
conf_file1: {{ tpl (.Files.Get "files/conf_file1") . | b64enc | quote }}
```

不是只有一个文件可以使用 `.Files.Glob`将一组*文件加载到配置图[中:](https://www.nclouds.com/blog/simplify-kubernetes-deployments-helm-part-3-creating-configmaps-secrets/)*

```
{{ (tpl (.Files.Glob "files/*").AsConfig . ) | indent 2 }}
```

尽管我们不能对`AsSecret`应用同样的方法，因为内容会在通过`tpl` 之前被编码。要将一组文件加载到一个秘密中，我们可以使用`[Glob](https://github.com/helm/helm/blob/master/docs/chart_template_guide/accessing_files.md#glob-patterns)` [来查找文件，使用](https://github.com/helm/helm/blob/master/docs/chart_template_guide/accessing_files.md#glob-patterns) `[Get](https://github.com/helm/helm/blob/master/docs/chart_template_guide/accessing_files.md#glob-patterns)` [来加载它们](https://github.com/helm/helm/blob/master/docs/chart_template_guide/accessing_files.md#glob-patterns):

```
{{ range $path, $bytes := .Files.Glob "files/*" }}
{{ base $path }}: '{{ tpl ($root.Files.Get $path) . | b64enc }}'
{{ end }}
```

# 3 授权给我们的图表开发者伙伴

在 keycloak 图表中使用`extraEnv`提出了一种可能的模式，用于处理可能需要注入到图表中的其他类型的定义。例如，keycloak 图表需要允许用户将 keycloak 图表打包到父图表中，父图表还提供用户定义的 json 文件，用户需要能够将该文件挂载到 keycloak 窗格中。图表通过[暴露](https://github.com/helm/charts/blob/0445fad8d8308f089f5fb6756a5570bc6d6f0bf5/stable/keycloak/templates/statefulset.yaml#L115)和`extraVolumes`来支持这一点:

```
{{- with .Values.keycloak.extraVolumes }}
{{ tpl . $ | indent 8 }}
{{- end }}
```

[和](https://github.com/helm/charts/blob/0445fad8d8308f089f5fb6756a5570bc6d6f0bf5/stable/keycloak/templates/statefulset.yaml#L73) `extraVoumeMounts`:

```
 volumeMounts:
            - name: scripts
              mountPath: /scripts
{{- with .Values.keycloak.extraVolumeMounts }}
{{ tpl . $ | indent 12 }}
{{- end }}
```

然后用户[可以](https://github.com/helm/charts/tree/cbd5e811a44c7bac6226b019f1d1810ef5ee45fa/stable/keycloak#setting-a-custom-realm)指向他们的秘密(包含 json 文件)并通过 values.yaml:

```
extraVolumes: |
 — name: custom-secret
   secret:
     secretName: custom-secret
extraVolumeMounts: |
 - name: custom-secret
   mountPath: "/realm/"
   readOnly: true
```

实际上，卷和卷装载的配置被外化为 values.yaml。该图表还应用了这种模式，以允许用户向模板中注入更多的资源，如 initContainers，甚至添加整个附加容器(或“sidecars”)。这种模式为图表用户提供了很大的能力和灵活性，特别是对于使用我们图表的其他图表开发人员。

当与 keycloak 图表显示的其他参数结合使用时，该模式会特别强大，例如图表初始化脚本中使用的 [preStartScript](https://github.com/helm/charts/blob/0445fad8d8308f089f5fb6756a5570bc6d6f0bf5/stable/keycloak/templates/configmap.yaml#L19) 变量:

```
{{- with .Values.keycloak.preStartScript }}                           echo 'Running custom pre-start script...'                       
{{ . | indent 4 }}                         
{{- end }}
```

用户可以将他们喜欢的任何 shell 脚本内容注入到 values.yaml 中的`.Values.keycloak.preStartScript` 中。因此，这种方法使用户不仅可以设置自己的配置参数，还可以加载自己的文件，并使用这些文件运行自己的定制脚本。

# 4 以不同的方式对外公开

由`helm create`生成的启动器舵图包括服务规范，但不包括入口。许多公共图表确实定义了入口资源。这需要可配置，因为用户可能不想使用入口。所以 [rabbitmq 图表](https://github.com/helm/charts/blob/cbd5e811a44c7bac6226b019f1d1810ef5ee45fa/stable/rabbitmq/templates/ingress.yaml#L1)和其他许多图表一样，用以下内容包装了它的整个入口资源定义:

```
{{- if .Values.ingress.enabled }}
...
{{-end}
```

这符合官方图表回购的[审查指南](https://github.com/helm/charts/blob/master/REVIEW_GUIDELINES.md#ingress)，该指南建议默认情况下应禁用入口。

例如，rabbitmq 图表需要提供是否通过设置基于[主机的](https://github.com/helm/charts/blob/cbd5e811a44c7bac6226b019f1d1810ef5ee45fa/stable/rabbitmq/templates/ingress.yaml#L19)入口规则来公开它的选项:

```
rules:
  {{- if .Values.ingress.hostName }}
  - host: {{ .Values.ingress.hostName }}
    http:
  {{- else }}
  - http:
  {{- end }}
```

(实际上，用户可能希望多个主机路由到服务，因此[审查指南](https://github.com/helm/charts/blob/master/REVIEW_GUIDELINES.md#ingress)建议对主机使用范围函数。rabbitmq 图表只提供了一个主机。)

rabbitmq 图表还允许不设置主机(上面的 else 条件)。在这种情况下，用户很可能会覆盖路径(这样 rabbitmq 就在一个唯一的路径上公开，与其他公开的服务不同):

```
- path: {{ default "/" .path }}
  backend:
    serviceName: {{ template "rabbitmq.fullname" . }}
    servicePort: {{ .Values.rabbitmq.managerPort }}
```

同样特别重要的是，用户可以灵活地在入口资源上设置注释，因为这些注释用于控制路由配置选项。[审核指南](https://github.com/helm/charts/blob/master/REVIEW_GUIDELINES.md#ingress)建议通过`toYaml`对此予以支持:

```
{{- with .Values.ingress.annotations }}
 annotations:
{{ toYaml . | indent 4 }}
{{- end }}
```

这允许图表的用户在 values.yaml 中设置注释，例如:

```
annotations:
  kubernetes.io/ingress.class: nginx
  nginx.ingress.kubernetes.io/rewrite-target: /
```

因此，用户可以通过 values.yaml 在入口设置他们需要的任何注释，而图表不需要知道这些注释可能是什么。仅仅公开这些注释就相当于在入口路由上应用类似脚本的配置。例如，使用 nginx 入口，用户可以通过配置片段以自定义方式应用规则来设置标头:

```
annotations:
  kubernetes.io/ingress.class: nginx
  nginx.ingress.kubernetes.io/rewrite-target: /
  nginx.ingress.kubernetes.io/configuration-snippet: |
     more_set_headers 'Access-Control-Allow-Origin: $http_origin'; 
```

# 我们从舵图艺术中学到了什么

![](img/ff8d6072a9c0d962fb1e578e14e4574f.png)

This [guy](https://www.pexels.com/photo/grayscale-photo-of-man-wearing-black-dress-shirt-with-hat-sitting-on-folding-armchair-beside-family-sketch-1046123/) is just chillin because his charts are so good

一个好的掌舵图需要预测用户需要什么样的灵活性。更多的灵活性往往意味着更多的复杂性或更高的抽象性，或者两者兼而有之。这会妨碍图表的可读性，并给图表用户带来更多负担。挑战在于选择最适合用户对特定图表的需求的工具。

还有其他我们没有涉及到的问题需要平衡，比如测试和安全性。这只是官方图表的一个特定部分。我试图把重点放在那些对我特别有帮助的模式上，以确保用户可以用你的图表做他们需要做的事情。在我为 [Activiti](https://github.com/Activiti) 项目制作[舵轮图](https://github.com/Activiti/activiti-cloud-charts/)的经历中，Kubernetes 官方海图对我帮助极大。希望这篇文章中的解释可以帮助鼓励其他人深入官方回购，并从其图表中获得灵感。