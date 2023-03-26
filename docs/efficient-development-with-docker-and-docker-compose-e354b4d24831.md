# 使用 Docker 和 docker-compose 进行高效开发

> 原文：<https://medium.com/hackernoon/efficient-development-with-docker-and-docker-compose-e354b4d24831>

![](img/d817aa09787ba817d44f7dfd3cf062b4.png)

我们将为一个包含各种服务的项目建立一个开发环境。所有这些服务都将被 Docker 容器化，它们将在开发期间使用 docker-compose 同时运行。

我们的环境将具有即时代码重载、测试驱动开发、数据库连接、依赖性管理等特性。使用 docker-compose 或 Rancher 可以很容易地将它部署到产品中。作为奖励，我们将在 Gitlab 上建立持续集成。

文章讲的是效率，我就开门见山了。

## 目标

我们希望:

*   在我们的 Docker 容器中尽快键入代码并观察变化，最好没有手动操作；
*   拥有代表实际部署环境的本地环境；
*   支持多种工作流。

让我们实现它。

## 先决条件

您将需要安装以下工具:

*   [码头工人](https://docs.docker.com/engine/installation/linux/) (CE 没事)
*   [docker-compose](https://docs.docker.com/compose/install/)

## 前提

我们将建立一个由 Python 和 Java 服务以及 Postgres 数据库组成的项目。Postgres 数据库将在开发环境中运行在我们自己的机器上，但是在生产过程中被认为是外部的(例如，它可能使用 Amazon RDS)。

Python 服务包含 Pytest 支持的单元测试，我们将为此设置测试驱动开发。Java 服务在构建过程中使用 Maven。

最后，我们将使用 Gitlab 的容器注册表和 Gitlab 的 CI 服务。下面描述的代码也可以在 [Github](https://github.com/larsderidder/docker-compose-development-env) 或 [Gitlab](https://gitlab.com/lars_xithing/docker-compose-development-env) 库中获得。

这个设置应该演示最基本的概念。然而，下面描述的方法应该不管技术如何都可以工作。

## 设置

文件结构:

```
|/myproject
| /python
|  /mypackage
|   run.py
|  /tests
|   my test.py
|  Dockerfile
|  setup.py
|  requirements.txt
|
| /java
|  Dockerfile
|  pom.xml
|  /src
|   /main
|    /java
|     /com
|      /example
|       /Main.java
|
| docker-compose.common.yml
| docker-compose.dev.yml
| docker-compose.prod.yml
| Makefile
| python-tests.sh
| .gitlab-ci.yml
```

Python 服务的 docker 文件如下:

```
FROM python:3.6-slimCOPY . /code
WORKDIR /codeRUN pip install --no-cache-dir -r requirements.txt
RUN pip install -e .ENTRYPOINT python ./mypackage/run.py
```

这将服务代码添加到容器中，安装它的依赖项(包含在 requirements.txt 中，在本例中它将包含`pytest`和`watchdog`)，并安装 Python 服务本身。它还定义了容器启动时要执行的命令。

Java 服务的 docker 文件可以在下面找到:

```
FROM maven:3.5-jdk-8COPY . /usr/src/app
WORKDIR /usr/src/appRUN apt-get update && apt-get install entr -yRUN mvn clean package --batch-mode
ENTRYPOINT java -jar target/docker-compose-java-example-1.0-SNAPSHOT.jar
```

像 Python Dockerfile 一样，这也首先将代码添加到容器中。然后继续安装 Unix 实用程序`entr`,我们稍后会用到它。Maven 用于创建 JAR 文件，之后我们定义容器命令来执行 JAR 文件。

最后，docker-compose.common.yml 文件构成了我们的环境的基础，它包含了对应用程序很重要的所有配置，而不管它是在什么环境中执行的。这相当简单:

```
version: '2'services:
  python:
    build: ./python
    environment:
      - POSTGRES_USER
      - POSTGRES_PASSWORD
      - POSTGRES_DB
      - POSTGRES_HOST java:
    build: ./java
    environment:
      - POSTGRES_USER
      - POSTGRES_PASSWORD
      - POSTGRES_DB
      - POSTGRES_HOST
```

这为我们提供了创建开发配置的所有要素。

## 开发配置

我们来看看 docker-compose.dev.yml 文件。这可能看起来令人生畏，但是不要担心，我们将在下面一步一步地完成它。

```
version: '2'services:
  python:
    image: registry.gitlab.com/mycompany/myproject/python:dev
    volumes:
      - ./python/:/code
    entrypoint: watchmedo auto-restart --recursive --pattern="*.py" --directory="." python mypackage/run.py
    depends_on:
      - postgres
    links:
      - postgres
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=myproject
      - POSTGRES_HOST=postgrespython-tests:
    image: registry.gitlab.com/mycompany/myproject/python:dev
    volumes:
      - ./python/:/code
    entrypoint: watchmedo auto-restart --recursive --pattern="*.py" --directory="." pytest
    depends_on:
      - python

  java:
    image: registry.gitlab.com/mycompany/myproject/java:dev
    volumes:
      - ./java/:/usr/src/app
    entrypoint: sh -c 'find src/ | entr mvn clean compile exec:java --batch-mode --quiet'
    depends_on:
      - postgres
    links:
      - postgres
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=myproject
      - POSTGRES_HOST=postgres postgres:
    image: postgres:9.6
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=myproject
    volumes:
      - /data/aedspy/postgres:/var/lib/postgresql/data pgadminer:
    image: clue/adminer
    ports: 
      - "99:80"
```

## 开发配置— Python

让我们从 Python 服务开始。我会指出有趣的部分。

```
volumes:
  - ./python/:/code
```

这里实际发生的是我们主机上的`python`子目录，包含我们 Python 服务的代码，现在被映射到我们容器中的`/code`目录。要回答为什么这是相关的问题，让我们快速地再看一下 Python Dockerfile 文件中的相关行:

```
COPY . /code
WORKDIR /code
```

如果 docker-compose.dev.yml 文件中没有`volumes`语句，`python`子目录的内容将被简单地添加到容器中。如果在主机上进行了更改，那么在我们可以在容器中看到这些更改之前，必须重新构建容器。

但是，使用 docker-compose.dev.yml 文件中包含的`volumes`语句，您所做的任何更改都会立即反映在容器内部。这是因为两个目录现在都指向*完全相同的文件*。

docker-compose.dev.yml 文件中的下几行利用了这一点:

```
entrypoint: watchmedo auto-restart --recursive --pattern="*.py" --directory="." python mypackage/run.py
```

这一行覆盖了容器的`entrypoint`(这是启动容器时执行的命令)。默认的`entrypoint`在 Dockerfile 文件中定义，如下所示:

```
ENTRYPOINT python ./mypackage/run.py
```

然而，由于 docker compose 文件中的`entrypoint`语句，这个入口点现在将被以`watchmedo`开头的命令替换。`watchmedo`命令是我们包含在`requirements.txt`文件中的[看门狗](https://pypi.python.org/pypi/watchdog)包的一部分。它监控给定目录中具有所提供模式的文件(在本例中是所有的`*.py`文件)，如果其中任何一个文件被修改，`watchmedo`将重启运行进程并执行所提供的命令(在本例中是`python ./mypackage/run.py`)。

这一行，结合我们前面提到的卷映射，意味着对我们主机上的`./python`目录中的任何 Python 文件的每次修改都将重启我们的应用程序。如果你打开任何 Python 文件并修改它，你会看到你所做的每一个改变都会立即反映在运行的容器中。

可能只有我，但这是我见过的最酷的事情之一。

注意:请记住，如果添加新的依赖项，您确实需要重新构建映像。

## 开发配置——Python 单元测试和测试驱动开发

让我们来看看 Python 单元测试服务的 docker-compose.dev.yml 文件，名为`python-tests`:

```
python-tests:
  image: registry.gitlab.com/mycompany/myproject/python:dev
  entrypoint: watchmedo auto-restart --recursive --pattern="*.py" --directory="." pytest
  depends_on:
   - python
```

有趣的是，该图像与 Python 服务的图像相同。这意味着它将使用与 Python 服务完全相同的环境；该映像将只构建一次。

`depends_on`语句建议 docker-compose 在运行`python-tests`服务之前构建`python`服务。

但是最重要的一行还是`entrypoint`。我们在这里做的事情与我们在常规 Python 服务中做的事情非常相似，但是我们现在让`watchemedo`在每次修改时执行`pytest`(如果您还记得，这也包含在`requirements.txt`文件中)。

这样做的结果是，每次代码变更*都会自动执行*py test 可以找到的所有测试*，给你*关于测试状态的即时反馈*。*

这使得用 Docker 进行测试驱动开发变得微不足道。

## 开发配置— Java

Java 是一种编译语言，使用起来稍微复杂一点。幸运的是，Maven 在很大程度上帮助了我们。

首先要注意的是 docker 文件中的下面一行:

```
RUN apt-get update && apt-get install entr -y
```

这里发生的是安装了命令行工具`[entr](http://entrproject.org/)`。它的功能与我们在 Python 中使用的 watchdog 的`watchmedo`命令非常相似，但优点是它不需要 Python 就可以工作；这只是一个通用的 Unix 实用程序。事实上，我们也可以在 Python 服务中使用它，但是，我们没有这样做。

我们可以在`docker-compose.dev.yml`文件和`java`服务的入口点中看到它的作用:

```
entrypoint: sh -c 'find src/ | entr mvn clean compile exec:java --batch-mode --quiet'
```

这表示‘无论何时目录`src/`中的任何文件发生变化，请让 maven 清理、编译然后执行 Java 项目’。

这些都不是现成的；Maven 首先需要在 `pom.xml`文件中进行一些相当广泛的配置。更具体地说，我们需要几个插件，即 [Maven 编译器插件](https://maven.apache.org/plugins/maven-compiler-plugin/)、执行容器默认入口点的 [Maven JAR 插件](https://maven.apache.org/plugins/maven-jar-plugin/)和在开发期间运行的 [Exec Maven 插件](http://www.mojohaus.org/exec-maven-plugin/usage.html)(使用 Java 目标)。

一旦这些都配置好了，再加上构成有效的`pom.xml`文件的其他内容，结果就和 Python 服务一样了(实际上还稍微好一点):对 Java 源文件的每一次更改都会重启应用程序，编译 Java 文件，安装新的依赖项(谢谢 Maven！)并重新启动应用程序。

## 开发配置—PostgresDB

让我们再次看看 docker-compose.dev.yml 文件中的相关行:

```
postgres:
  image: postgres:9.6
  environment:
    - POSTGRES_USER=user
    - POSTGRES_PASSWORD=password
    - POSTGRES_DB=myproject
  volumes:
    - /data/myproject/postgres:/var/lib/postgresql/datapgadminer:
  image: clue/adminer
  ports: 
    - "99:80"
```

`postgres`服务使用标准的 Postgres 映像，这是默认配置。环境变量通过定义一个名为“myproject”的数据库来配置 Postgres，用户名为“user”，密码为“password”。

Python 和 Java 服务也定义了这些环境变量。他们应该在应用程序代码中使用这些来连接数据库。在`docker-compose.dev.yml`文件中，这些值都是硬编码的。然而，在构建生产容器时，生产值应该作为环境变量从外部源传入。这允许与适当的秘密管理工具链集成。

作为`postgres`服务的最后指令，定义了一个`volume`。这会将 Postgres 数据库数据映射到主机上的某个位置。虽然不是绝对必要的，但是如果容器由于某种原因被删除，这对于持久化 Postgres 的数据是有用的。

最后，我们还定义了`pgadminer`服务，它启动 [adminer](https://www.adminer.org/) ，这是一个通过 web 界面进行数据库管理的有用工具。有了这个配置，就可以通过主机上的端口 99 访问它(所以 [http://127.0.0.1:99)。](http://127.0.0.1:99).)作为主机名，您应该使用 Postgres 服务的名称，在本例中为`postgres`，因为它们共享同一个 docker 网络，因为它们是在同一个 Docker 合成文件中定义的，因此 DNS 会自动为您执行。

## 开发配置—构建和执行

现在让我们兜一圈。

首先，我们必须为开发构建所有的容器。从命令行:

```
docker-compose -f docker-compose.common.yml -f docker-compose.dev.yml build
```

并启动所有服务:

```
docker-compose -f docker-compose.common.yml -f docker-compose.dev.yml up
```

由于需要键入的内容很多，而且我喜欢自文档化的入口点，所以我倾向于在 Makefile 中定义这些和其他基本的项目范围的命令:

```
dev-build:
  docker-compose -f docker-compose.common.yml -f docker-compose.dev.yml build --no-cachedev:
  docker-compose -f docker-compose.common.yml -f docker-compose.dev.yml up
```

在某些时候，您可能想要运行 Python 单元测试，而不需要启动所有的服务。因此，我们将 python-tests.sh 定义如下:

```
#!/bin/bash
docker-compose -f docker-compose.common.yml -f docker-compose.dev.yml run --rm --entrypoint pytest python $*
```

这将执行`python`容器中的`pytest`，执行所有的测试。提供给脚本的任何参数都将直接传递给容器中的 pytest 命令(多亏了`$*`)，允许您像正常运行 pytest 一样运行它。最后，我们用以下代码扩展 Makefile:

```
test:
 ./python-tests.sh
```

# 生产配置

快到了。我们来看看 docker-compose.prod.yml:

```
version: '2'services:
  python:
    image: $IMAGE/python:$TAG
    restart: always java:
    image: $IMAGE/java:$TAG
    restart: always
```

这就是事情的全部。大部分配置应该在 docker-compose.common.yml 中，命令和入口点都在 docker 文件中。您确实需要传入还没有值的环境变量(在 docker-compose.common.yml 和这个文件中定义),但是这应该由您的构建脚本来处理。

这样，我们就可以为生产构建服务了。让我们就这么做吧，用 Gitlab 的 CI 服务来构建它。让我们来看看。gitlab-ci.yml 文件。它非常简单，允许优化，但是它完成了工作。

```
stages:
  - build
  - testvariables:
  TAG: $CI_BUILD_REF
  IMAGE: $CI_REGISTRY_IMAGEservices:
  - docker:dindimage: dockerbefore_script:
  - apk add --update py-pip
  - pip install docker-compose
  - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN $CI_REGISTRYbuild:
  stage: build
  script:
    - docker-compose -f docker-compose.common.yml -f docker-compose.prod.yml build
    - docker-compose -f docker-compose.common.yml -f docker-compose.prod.yml pushtest-python:
  stage: test
  script:
    - docker-compose -f docker-compose.common.yml -f docker-compose.prod.yml pull python
    - docker-compose -f docker-compose.common.yml -f docker-compose.prod.yml run --rm --entrypoint pytest python
```

这里有一些 Gitlab CI 特有的东西，比如`docker:dind`服务的定义和运行构建的映像，这两者都需要 docker 可用。此外，`before_script`部分很重要，因为它安装了`docker-compose`(因为我找不到最新版本的好图片)。您还会注意到，默认情况下，`$TAG`和`$IMAGE`变量是使用 Gitlab 的 CI runner 在[中传递的环境变量来设置的。](https://docs.gitlab.com/ee/ci/variables/)

此外，Gitlab 有一个概念，即[秘密变量](https://docs.gitlab.com/ee/ci/variables/#secret-variables)，它们作为环境变量在您的构建中传递。只需设置正确的值，然后`docker-compose`就会选择它们。如果您使用另一个 CI 环境，我敢肯定它也有这方面的一些机制。如果您喜欢稍微低一点的技术，您当然也可以编写自己的部署脚本。

# 包扎

所以你有它；一个高效但并不复杂的设置来编排和开发 Docker 中的大多数项目。

如果你感兴趣，我还写了一篇关于在 Gitlab 上用 docker-compose 设置简单部署的[文章。这将把您从基于 Gitlab CI 的开发环境直接带到 docker-compose 的持续部署。](/@Empanado/simple-continuous-deployment-with-docker-compose-docker-machine-and-gitlab-ci-9047765322e1)