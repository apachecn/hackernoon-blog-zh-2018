# 2 分钟食谱:如何回滚您的 conda 环境

> 原文：<https://medium.com/hackernoon/2-minute-recipe-how-to-rollback-your-conda-environment-8521208f9a6c>

![](img/9ec5efa2d525c3ed1b1ed9e255006671.png)

我在做一个个人机器学习项目，因为一些依赖，我在 python 2.7 中使用了 [Anaconda](https://anaconda.org/) 环境(是的，我知道)。

我在尝试升级 sklearn 库的时候，不小心把 Python 更新到了 3.6。我正要删除我的整个环境，并从 YAML 文件中重新创建它(感谢上帝我有那个)，但后来我参考了 conda 文档，它就在那里，这个神奇的命令把我从所有这些麻烦中拯救了出来。

```
# List the history of each change to the current environment 
conda list --revisions# Restore environment to a previous revision 
conda install --revision 2
```

最好的解释方式是举一个简单的例子。如果您运行`*conda list –revision*`，您将得到如下输出:

```
2017-04-18 23:29:36  **(**rev 1**)**
     requests  **{**2.12.3 -> 2.13.0**}**

2018-05-30 19:41:47  **(**rev 2**)**
     mkl  **{**11.3.3 -> 2018.0.2**}**
     numpy  **{**1.11.2 -> 1.14.3**}**
     pip  **{**9.0.1 -> 10.0.1**}**
     python  **{**2.7.12 -> 3.6.5**}**
     scikit-learn  **{**0.17.1 -> 0.19.1**}**
     scipy  **{**0.18.1 -> 1.1.0**}**
     setuptools  **{**27.2.0 -> 39.1.0**}**
     wheel  **{**0.29.0 -> 0.31.1**}**
    +blas-1.0
    +certifi-2018.4.16
    +icc_rt-2017.0.4
    +intel-openmp-2018.0.0
    +mkl_fft-1.0.1
    +mkl_random-1.0.1
    +numpy-base-1.14.3
    +vc-14
    +vs2015_runtime-14.0.25123
    +wincertstore-0.2
```

正如您在输出中看到的，它列出了每个修订以及更新的包(旧版本->新版本)和新添加的包(带有+符号的包)。所以现在您知道了在每个修订中做了什么更改，您可以通过使用`conda install –revision *revision number*`安全地回滚到您的环境的先前版本。

在我的例子中，我恢复到 rev 1，所以在回滚后，当我再次运行`*conda list –revisions*`时，我可以看到在回滚中所做的更改。

```
2018-05-30 20:08:46  **(**rev 3**)**
     mkl  **{**2018.0.2 -> 11.3.3**}**
     numpy  **{**1.14.3 -> 1.11.2**}**
     pip  **{**10.0.1 -> 9.0.1**}**
     python  **{**3.6.5 -> 2.7.12**}**
     scikit-learn  **{**0.19.1 -> 0.17.1**}**
     scipy  **{**1.1.0 -> 0.18.1**}**
     setuptools  **{**39.1.0 -> 27.2.0**}**
     wheel  **{**0.31.1 -> 0.29.0**}**
    -blas-1.0
    -certifi-2018.4.16
    -icc_rt-2017.0.4
    -intel-openmp-2018.0.0
    -mkl_fft-1.0.1
    -mkl_random-1.0.1
    -numpy-base-1.14.3
    -vc-14
    -vs2015_runtime-14.0.25123
    -wincertstore-0.2
```

您可以看到修订版 3 的变化正好与修订版 2 相反。

如果你不小心搞砸了你的环境，这就方便了。

更多信息:

Conda 用户指南:[https://conda . io/docs/User-Guide/tasks/manage-environments . html](https://conda.io/docs/user-guide/tasks/manage-environments.html)

康达小抄:[https://conda.io/docs/_downloads/conda-cheatsheet.pdf](https://conda.io/docs/_downloads/conda-cheatsheet.pdf)

如果你有任何建议，请在推特上告诉我: [@Sriramjaju](https://twitter.com/Sriramjaju)

*合十礼！*