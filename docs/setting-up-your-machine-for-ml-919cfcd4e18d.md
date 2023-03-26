# 为 ML 设置您的机器

> 原文：<https://medium.com/hackernoon/setting-up-your-machine-for-ml-919cfcd4e18d>

作为一名软件工程出身的人，我认为在进入*机器学习*之前，我应该做的第一件事就是安装我的电脑。我天真地以为可能只需要几个小时。我完全错了。

我想用 anaconda 和 CUDA sdk 的正确版本设置 pytorch。这应该是一个相当简单的练习。幸运的是，我所有的机器都有 NVIDIA GPUs。所以我认为这只是一个安装正确软件的问题，我应该很快就能完成。

哦..哦..没那么快。

## **MacBook Pro**

首先，我试用了装有 NVIDIA GeForce 750m 的 mac book pro。安装完所有东西后，conda env 创建失败

```
conda env create
Solving environment: failedResolvePackageNotFound:
  - cuda90
```

几次尝试之后，我意识到 Mac 可能是最难设置的环境。

## Ubuntu 16.04

接下来，我转移到运行 Ubuntu 16.04 的 Linux 桌面上。这台机器甚至无法安装 NVIDIA 驱动程序。这是一个装有很多软件的旧装置。我安装的任何东西都可能会弄坏别的东西，所以随它去吧。

## Windows 10

然后我搬到了我的 Windows 10 桌面。这台机器很新，只有 6 个多月，我很少使用它。于是，我装了 NVIDIA 驱动，SDK，Anaconda，git。然后我运行了下面的代码来测试 pytorch 和 CUDA 是否能工作。

```
>>> import torch
>>> torch.cuda.is_available()
True
>>> x = torch.randn( 4, 5 )
>>> print( x )
tensor([[-0.1285, -0.2369, -0.3363,  0.1386,  0.1244],
        [ 0.1818, -2.0207,  1.9165, -1.4153,  0.3645],
        [-0.5384,  0.4833, -1.0172, -0.2509, -1.3831],
        [ 1.9053, -1.7572, -0.0098, -0.0333, -1.8762]])
>>> device = torch.device("cuda")
>>> y = torch.ones_like( x , device=device )
C:\Users\nsankaran\AppData\Local\conda\conda\envs\torch2\lib\site-packages\torch\cuda\__init__.py:116: UserWarning:
    Found GPU0 NVS 510 which is of cuda capability 3.0.
    PyTorch no longer supports this GPU because it is too old.warnings.warn(old_gpu_warn % (d, name, major, capability[1]))
THCudaCheck FAIL file=c:\programdata\miniconda3\conda-bld\pytorch_1524546371102\work\aten\src\thc\generic/THCTensorMath.cu line=15 error=48 : no kernel image is available for execution on the device
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
RuntimeError: cuda runtime error (48) : no kernel image is available for execution on the device at c:\programdata\miniconda3\conda-bld\pytorch_1524546371102\work\aten\src\thc\generic/THCTensorMath.cu:15
```

某些张量运算无法在 GPU 上执行。看起来这是一个问题，直到最后一步，Windows 10 看起来非常有希望。

在这一点上，我已经花了 15 个多小时，没有明确和简单的方法来安装我需要的软件。我认为安装我的机器很难。

## 图纸空间(云 GPU)

与此同时，我用[paperspace.com](https://www.paperspace.com/)创建了一个账户，并试图创建一个支持 GPU 的虚拟机。但是，paperspace 不起作用。我想使用`fastai`模板或`linux`模板。两者都没有。

![](img/821a1a140ed0985833329070a550b51c.png)

Paperspace GPU request dialog

我请求了几次 GPU，但是没有任何反应。所以我认为 paperspace 还没有准备好。在这一点上，我是完全绝望的。

## Ubuntu 16.04(再次)

我还有一台运行 Ubuntu 16.04 的 NVIDIA GPU 笔记本电脑。我想为什么不再试一次。

到这个时候，我确切地知道需要做什么。所以我开始系统地安装软件，

*   下载了 NVIDIA GPU [驱动](https://www.geforce.com/drivers/results/134859)
*   用于 Ubundu 16.04 的 CUDA SDK[指令](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1604&target_type=deblocal)
*   安装 CUDNN [指令](https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html)
*   为 Python 3.6 安装 Anaconda】指令
*   为 Linux，Conda，Python 3.6 和 Cuda 9.1 安装 pytorch。Pytorch 生成了简单的一行脚本来安装

```
conda create --name torch
source activate torch
conda install pytorch torchvision cuda91 -c pytorch
```

所有这些步骤都进展顺利。现在是测试时间。为了测试，我使用了下面的代码。

```
import torchif torch.cuda.is_available():
    print("cuda is available")x = torch.randn( 4, 5 )
    print( "x=\n", x )device = torch.device("cuda")
    y = torch.ones_like( x, device=device )
    z = y.to( device )a = y +z 
    print( "a=\n", a )
    print( "a.to(\"cpu\")\n", a.to("cpu", torch.double) )else:
    print( "cuda is not available")
```

这是结果，

```
cuda is available
x=
 tensor([[-1.1355, -1.0563,  1.1290,  0.5870, -1.4088],
        [ 0.0311,  1.4285, -0.7489, -0.3020, -1.1053],
        [-0.6777, -0.3300, -0.1201, -0.0400, -0.7543],
        [-0.3388,  0.8496, -0.1594, -0.3230,  1.0891]])
a=
 tensor([[ 2.,  2.,  2.,  2.,  2.],
        [ 2.,  2.,  2.,  2.,  2.],
        [ 2.,  2.,  2.,  2.,  2.],
        [ 2.,  2.,  2.,  2.,  2.]], device='cuda:0')
a.to("cpu")
 tensor([[ 2.,  2.,  2.,  2.,  2.],
        [ 2.,  2.,  2.,  2.,  2.],
        [ 2.,  2.,  2.,  2.,  2.],
        [ 2.,  2.,  2.,  2.,  2.]], dtype=torch.float64)
```

终于在第四次尝试中成功了。

## 我的建议

*   使用更新的 Windows 10 或 Ubuntu 16.04 安装
*   按照提供的顺序安装软件
*   希望一切正常

这是真棒有 Linux 笔记本电脑安装与 ML 的东西。