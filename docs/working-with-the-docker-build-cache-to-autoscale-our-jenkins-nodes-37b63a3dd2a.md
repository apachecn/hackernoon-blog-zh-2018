# 使用 Docker 构建缓存自动扩展我们的 Jenkins 节点

> 原文：<https://medium.com/hackernoon/working-with-the-docker-build-cache-to-autoscale-our-jenkins-nodes-37b63a3dd2a>

我们有一个相当标准的 [Jenkins](https://hackernoon.com/tagged/jenkins) 设置，一个主节点和一大堆辅助节点，大部分工作步骤都外包出去了。

这些辅助节点整晚无所事事，因此我们认为最好在晚上缩减它们，然后在早上备份，这样我们就可以用节省下来的资金在白天需要时运行更多辅助节点。

我们在我们的詹金斯基础设施上运行各种工作，但最常见的工作是在每次推进 PR 和合并到 master 时构建和测试我们的 monolith。

这基本上包括对分支进行 git 签出(签入主节点上的现有工作区，这样我们就不必进行完整的初始签出)，构建一个 [Docker](https://hackernoon.com/tagged/docker) 映像，其中包含所有的依赖项和运行作业时的代码库版本，然后在结果容器中运行测试(在任何一个辅助节点上，这样我们就可以并行运行大量作业，文件使用 Jenkins stashes 在节点之间传输)。

从头构建这个 Docker 映像大约需要 10 分钟，我们依靠这些节点上的 Docker 映像缓存来减少构建时间，方法是将通常使缓存无效的步骤(复制更新的代码库)放在 Docker 文件的末尾。

docker 文件看起来像这样:

```
FROM ubuntu:14.04# Things like apt-get installs, these take a decent amount of time
<some bash commands># These live in our main codebase along side the code and basically never change
<copy in some config files># There's a fair few of these, they take a while but rarely change
<copy some requirements.txt files>
<some pip installs># This is where we expect the cache to be invalidated and copy'ing in the files doesn't take long
<copy in the version of the codebase that's being built>
```

因此，如果我们希望自动扩展辅助节点，我们需要确保它们在作业开始运行之前拥有映像缓存。

第一种尝试是在节点启动时，从我们的注册表中的 Cloudformation userdata 中下载一份映像副本。

这失败了，因为 Docker 不会使用从注册表中提取的图像作为构建缓存的一部分。

这被认为是 https://github.com/moby/moby/issues/20316 的一个特点

第二次尝试是用将我们的存储库克隆到`/tmp`中来代替提取 Docker 映像，然后在节点第一次出现时从该版本的代码库构建 Docker 映像。这将为我们提供来自代码库最新版本的 Docker 映像副本，以及我们加速后续构建所需的构建缓存。

这失败了，在 Jenkins 作业的第一次运行期间，当我们复制配置文件时，缓存在第一个`COPY`步骤中无效，即使它们的内容没有改变。

这有点令人困惑，因为我认为 Docker 只在文件已经更改的情况下才在第`COPY`行使缓存无效，而实际上并没有。一些调查显示，Docker 在考虑文件是否被更改时也使用文件的`mtime`值。

[https://github . com/Moby/Moby/issues/4351 # issue comment-76222745](https://github.com/moby/moby/issues/4351#issuecomment-76222745)

因此，发生的情况是，在 Jenkins 作业中，git 签出代码库创建的文件与我们在节点创建时签出代码库到`/tmp`中的文件具有不同的`mtimes`，因此 Docker 认为我们在早期复制的配置文件被更改，并在我们希望的时间之前使缓存失效。

我们以前没有看到这个问题，因为我们在主节点上将作业的 git 签出部分签入到从不删除的工作区中，所以在初始签出后，这些文件从未被触及，Docker 也从未认为早期的`COPY`步骤已经失效。

我们可以用一个简单的例子来复制这种行为:

我们有一个包含两个文件的项目，一个 Dockerfile 文件和一个文本文件

```
vagrant@vagrant-ubuntu-trusty-64:~$ ls
Dockerfile  test
```

docker 文件只是复制到我们的文件中

```
vagrant@vagrant-ubuntu-trusty-64:~$ cat Dockerfile
from ubuntu:16.04
COPY test /srv/test
```

让我们记下测试文件的`mtime`，以便稍后使用

```
vagrant@vagrant-ubuntu-trusty-64:~$ stat test
  File: ‘test’
  Size: 0          Blocks: 0          IO Block: 4096   regular empty file
Device: 801h/2049d Inode: 140134      Links: 1
Access: (0664/-rw-rw-r--)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)
Access: 2017-12-31 18:03:33.995881878 +0000
Modify: 2017-12-31 18:03:33.995881878 +0000
Change: 2017-12-31 18:03:33.995881878 +0000
 Birth: -
```

最后，如果我们建造它

```
vagrant@vagrant-ubuntu-trusty-64:~$ sudo docker build .
Sending build context to Docker daemon 13.82 kB
Sending build context to Docker daemon
Step 0 : FROM ubuntu:16.04
16.04: Pulling from ubuntu
Step 1 : COPY test /srv/test
 ---> 3be8d8c094bf
```

我们没有缓存这个步骤，所以 Docker 只是运行它。

如果我们修改测试

```
vagrant@vagrant-ubuntu-trusty-64:~$ echo 'hello' > test
vagrant@vagrant-ubuntu-trusty-64:~$ sudo docker build .
Sending build context to Docker daemon 14.34 kB
Sending build context to Docker daemon
Step 0 : FROM ubuntu:16.04
 ---> c1ea3b5d13dd
Step 1 : COPY test /srv/test
 ---> 75bb69e528c3
Removing intermediate container 6771d1e1f191
Successfully built 75bb69e528c3
```

Docker 获得更改并使缓存层失效，这并不奇怪。

现在，如果`test`来自 git 回购呢？

我将这两个文件捆绑到一个 git repo 中，将它们向上推，然后将 repo 下拉到一个单独的文件夹中

```
vagrant@vagrant-ubuntu-trusty-64:~$ mkdir git
vagrant@vagrant-ubuntu-trusty-64:~$ cd git/
vagrant@vagrant-ubuntu-trusty-64:~/git$ git pull [git@github.com](mailto:git@github.com):AaronKalair/test.git
```

Docker 使用我们用早期的`docker build`为文件创建的缓存吗？

```
vagrant@vagrant-ubuntu-trusty-64:~/git/test$ sudo docker build .
Sending build context to Docker daemon 47.62 kB
Sending build context to Docker daemon
Step 0 : FROM ubuntu:16.04
 ---> c1ea3b5d13dd
Step 1 : COPY test /srv/test
 ---> f4fe0a287838
Removing intermediate container 20c77a3dee81
Successfully built f4fe0a287838
```

没有。

文件是一样的吗？

git 克隆的那个

```
vagrant@vagrant-ubuntu-trusty-64:~/git/test$ md5sum test
a10edbbb8f28f8e98ee6b649ea2556f4  test
```

原文

```
vagrant@vagrant-ubuntu-trusty-64:~$ md5sum test
a10edbbb8f28f8e98ee6b649ea2556f4  test
```

是的，一模一样。

那`mtime`呢？

```
vagrant@vagrant-ubuntu-trusty-64:~/git/test$ stat test
  File: ‘test’
  Size: 7          Blocks: 8          IO Block: 4096   regular file
Device: 801h/2049d Inode: 262341      Links: 1
Access: (0775/-rwxrwxr-x)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)
Access: 2017-12-31 19:37:05.348272796 +0000
Modify: 2017-12-31 19:37:05.348272796 +0000
Change: 2017-12-31 19:37:05.348272796 +0000
 Birth: -
```

不，它改变了(最初是`18:03`)，git pull 将修改、访问和更改时间设置为克隆的时间。

git 拉取会影响现有的文件吗？

如果我们将另一个文件添加到项目中，将它向上推…

```
vagrant@vagrant-ubuntu-trusty-64:~$ touch test_test
vagrant@vagrant-ubuntu-trusty-64:~$ git add test_test
vagrant@vagrant-ubuntu-trusty-64:~$ git commit
vagrant@vagrant-ubuntu-trusty-64:~$ git push origin master
```

然后把它拉回到另一个 git 收银台…

```
vagrant@vagrant-ubuntu-trusty-64:~$ cd git/test/
vagrant@vagrant-ubuntu-trusty-64:~/git/test$ git pullvagrant@vagrant-ubuntu-trusty-64:~/git/test$ stat test
  File: ‘test’
  Size: 7          Blocks: 8          IO Block: 4096   regular file
Device: 801h/2049d Inode: 262341      Links: 1
Access: (0775/-rwxrwxr-x)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)
Access: 2017-12-31 19:40:05.032278718 +0000
Modify: 2017-12-31 19:37:05.348272796 +0000
Change: 2017-12-31 19:37:05.348272796 +0000
 Birth: -vagrant@vagrant-ubuntu-trusty-64:~/git/test$ stat test_test
  File: ‘test_test’
  Size: 0          Blocks: 0          IO Block: 4096   regular empty file
Device: 801h/2049d Inode: 262359      Links: 1
Access: (0664/-rw-rw-r--)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)
Access: 2017-12-31 20:36:22.916378925 +0000
Modify: 2017-12-31 20:36:22.916378925 +0000
Change: 2017-12-31 20:36:22.916378925 +0000
 Birth: -
```

现有的测试文件保持不变，新文件出现，所有时间戳都设置为`git pull`的时间

好了，这解释了我们看到的行为，以及为什么我们以前从未遇到过这个问题，但如果不解决缓存问题，我们仍然无法自动扩展我们的辅助 Jenkins 节点。

因此，成功的第三次尝试是:

在主 docker 文件的开头创建一个我们在`FROM`中使用的基本映像，该文件已经运行了昂贵的步骤，并在节点启动时将其下载到节点。

这将有效地复制构建缓存中的昂贵步骤。

因此，我们有一个从 docker 文件开始的每夜构建，如下所示:

```
# NIGHTLY IMAGEFROM ubuntu:14.04<some bash commands>
<copy in some config files>
<copy some requirements.txt files>
<some pip installs>
```

我们的 Jenkins 作业现在使用一个 docker 文件，看起来像:

```
FROM NIGHTLY_IMAGE<copy in some config files>
<copy some requirements.txt files>
<some pip installs>
<copy in the version of the codebase that's being built>
```

现在，我们想要缓存的所有昂贵的命令(初始 bash 命令和 pip 安装)都在夜间映像中，当它被创建时，我们将它拉至一个实例，Jenkins 在`FROM`行中使用它。

Jenkins 在工作中使用的 Dockerfile 中重复的`pip installs`确保了如果在下一个每夜构建之前需求发生了变化，这些变化会反映在构建的映像中。

如果每夜映像构建使用不同`mtimes`的文件版本，那么作为已经有需求的`pip install`也没关系，只需要几秒钟。

每夜映像是在每天凌晨 2 点作为 Jenkins 作业构建的，cron 作业是助手，自动缩放操作确保他们每天都有最新的每夜映像。

现在我们有了它，我们现在花更少的钱，在需要的时候有更多的 Jenkins 容量！

在推特上关注我 [@AaronKalair](http://twitter.com/AaronKalair)