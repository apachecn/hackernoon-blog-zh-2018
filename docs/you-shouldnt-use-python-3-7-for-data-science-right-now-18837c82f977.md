# 你现在不应该将 Python 3.7 用于数据科学

> 原文：<https://medium.com/hackernoon/you-shouldnt-use-python-3-7-for-data-science-right-now-18837c82f977>

![](img/c48cd932db2e0fc991b8a7a0f66889d1.png)

“selective focus photography of girl crying” by [Arwan Sutanto](https://unsplash.com/@arwanod?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

**更新:Anaconda 发布了一个补丁！如果您重新安装 numpy 1.15.2(至少在 linux 上),您应该注意到您的内部版本号从 1 . 15 . 2-py 37 h1 d 66 E8 a _ 0→1 . 15 . 2-py 37 h1 d 66 E8 a _ 1。这些构建在 PyPi 上不可用。Numpy 1.15.3 也包含一个修复程序。我不建议用 Python 3.7 的旧版本 NumPy】**

**我们运营一个** [**云托管的 Jupyter**](https://www.saturncloud.io) **笔记本服务。** [**今天就开始**](https://www.saturncloud.io) **！**

我认为这是数据科学工作流和 Python 3.7 目前的一个严重缺陷。bug 不在 Python 中，而是在 NumPy 中，在 NumPy 中有时会吞掉造型错误。这意味着您可以将字符串转换为复数，并且 NumPy 可能不会抛出异常。Pandas 依靠 NumPy 的错误处理来确定您的数据帧是否全是数字。所以有时候，取数据帧的平均值可以得到复数形式的结果。在 NumPy 补丁发布之前，每个人都应该继续使用 Python 3.6。相关 github 问题在 NumPy [#11993](https://github.com/numpy/numpy/issues/11993) 、 [#12062](https://github.com/numpy/numpy/pull/12062) 和熊猫 [#22506](https://github.com/pandas-dev/pandas/issues/22506) 、 [#22753](https://github.com/pandas-dev/pandas/issues/22753) 中。您可以通过将`numeric_only=True`传递到对`.mean`的调用中来规避这个问题，但是您不太可能已经这样做了。NumPy 的修复已被合并。一旦发布，你应该升级。

# 数字臭虫

如果我们看看 NumPy 中用于将对象转换成其他类型的[代码:](https://github.com/numpy/numpy/blob/v1.15.2/numpy/core/src/multiarray/arraytypes.c.src#L1481)

```
static void
OBJECT_to_@TOTYPE@(void *input, void *output, npy_intp n,
        void *NPY_UNUSED(aip), void *aop)
{
    PyObject **ip = input;
    @totype@ *op = output;

    npy_intp i;
    int skip = @skip@;

    for (i = 0; i < n; i++, ip++, op += skip) {
        if (*ip == NULL) {
            @TOTYPE@_setitem(Py_False, op, aop);
        }
        else {
            @TOTYPE@_setitem(*ip, op, aop);
        }
    }
}
```

当`@TOTYPE@_setitem`调用出现问题时，该代码不会退出。 [@ahaldane](https://github.com/ahaldane) 发现并修复为

```
static void
OBJECT_to_@TOTYPE@(void *input, void *output, npy_intp n,
        void *NPY_UNUSED(aip), void *aop)
{
    PyObject **ip = input;
    @totype@ *op = output;

    npy_intp i;
    int skip = @skip@;

    for (i = 0; i < n; i++, ip++, op += skip) {
        if (*ip == NULL) {
            if (@TOTYPE@_setitem(Py_False, op, aop) < 0) {
                return;
            }
        }
        else {
            if (@TOTYPE@_setitem(*ip, op, aop) < 0) {
                return;
            }
        }
    }
}
```

在不退出循环的情况下，后续调用可能会调用一些 CPython 代码，这些代码在 3.7 中被更改为调用`PyErr_Clear`。顺便说一下，如果您觉得这段代码很奇怪，那是因为 NumPy 使用了自己的模板引擎。

# 熊猫冲击

这当然会比我在这里描述的影响更大，但是最直接的影响是，有时聚合混合类型的数据帧会导致复杂的结果。为了说明这个问题的不可预测性，请尝试以下示例:

```
df = pd.DataFrame({
    "user":["A", "A", "A", "A", "A"],
    "connections":[3.0, 4970.0, 4749.0, 4719.0, 4704.0],
})
df['connections2'] = df.connections.astype('int64')
print()
print('usually incorrect')
print()
print(df.mean())
print()
print(df.head())
print()
print('usually correct')
print()
print(df.mean())
```

我总是得到类似这样的输出:

```
usually incorrect

user            (1.38443408503753e-310+1.38443408513886e-310j)
connections       (1.3844303826283e-310+1.3844336097506e-310j)
connections2                                         (3829+0j)
dtype: complex128

  user  connections  connections2
0    A          3.0             3
1    A       4970.0          4970
2    A       4749.0          4749
3    A       4719.0          4719
4    A       4704.0          4704

usually correct

connections     3829.0
connections2    3829.0
dtype: float64
```

为了说明不可预测性，如果我取出对`print(df.head())`的调用，那么我几乎每次都会得到复杂的结果。如果我将 connections2 的初始化放入 dataframe 构造函数，我几乎总是得到黑色的浮点结果，但是有时，我会错误地将“用户”的平均值设为 0.0

发生这种情况的原因是因为`_reduce`方法依赖于当归约函数应用于`.values`时发生的异常。如果失败，Pandas 试图[只提取数字列](https://github.com/pandas-dev/pandas/blob/v0.23.4/pandas/core/frame.py#L6901)，然后再次应用该函数。在我们真正得到纯数字数据之前，有两次尝试调用函数[这里](https://github.com/pandas-dev/pandas/blob/v0.23.4/pandas/core/frame.py#L6866)和[这里](https://github.com/pandas-dev/pandas/blob/v0.23.4/pandas/core/frame.py#L6893)。

# 结论

现在继续使用 Python 3.6。当 NumPy 发布修补程序时，请升级到该版本。

# 感谢阅读！

我们是云计算公司，我们提供云计算托管的笔记本电脑。如果您想让 Jupyter 为您的团队服务，或者参加课程，请联系我们！

*原载于*[*www.opensourceanswers.com*](https://www.opensourceanswers.com/blog/you-shouldnt-use-python-37-for-data-science-right-now.html)*。*