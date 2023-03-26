# 基础 Python 数据科学库:备忘单(第 2/4 部分)

> 原文：<https://medium.com/hackernoon/fundamental-python-data-science-libraries-a-cheatsheet-part-2-4-fcf5fab9cdf1>

![](img/fd47061ca7d323e1c8a623940f27bcb1.png)

如果您是一名开发人员，并且希望将数据操作或科学集成到您的产品中，或者开始您的数据科学之旅，下面是您需要了解的 Python 库。

1.  NumPy
2.  熊猫
3.  Matplotlib
4.  sci kit-学习

本系列的目标是提供关于如何使用必备库的介绍、亮点和演示，以便您可以选择更深入的内容。

# 熊猫

这个库是建立在 [NumPy](http://www.numpy.org/) 之上的，你可能还记得我上一篇[文章](https://hackernoon.com/fundamental-python-data-science-libraries-a-cheatsheet-part-1-4-58884e95c2bd)。Pandas 将 NumPy 强大的数学数组-magic 向前推进了一步。它允许你在一个关系表结构中存储&操作数据。

## 图书馆的焦点

这个库集中于两个对象:系列(1D)和数据框架(2D)。每个都允许您设置:

*   一个*索引*——允许您查找和操作某些行
*   *列名*——让您找到并操作某些列

SQL 似曾相识吗？

## 装置

打开命令行并键入

```
pip install pandas
```

Windows:过去我发现安装 NumPy 和其他科学软件包是一件令人头疼的事情，所以我鼓励所有 Windows 用户下载 Anaconda 的 Python 发行版，它已经安装了所有的数学和科学库。

## 细节

pandas 数据结构在几个方面不同于 NumPy 数组:

1.  NumPy 数组中的所有数据必须是相同的数据类型，pandas 数据结构可以保存多种数据类型
2.  pandas 数据结构允许您命名行和列
3.  NumPy 数组可以达到多维度，pandas 数据结构将你限制在 1 & 2D。*

*有一个名为[面板](https://pandas.pydata.org/pandas-docs/stable/dsintro.html#panel)的 3D 熊猫数据结构，但它已被贬值

让我们开始吧！

```
import pandas as pd
import numpy as np
```

## 创造

很简单！

您可以从列表、元组、NumPy 数组甚至字典中创建一个系列或数据帧！哦，当然还有 CSV 和数据库。

**来自一个数组**

```
# Series
future_array1 = [1,2,3,4,5,6]
array1 = np.array(future_array1)
s = pd.Series(array1)>>> s
0    1
1    2
2    3
3    4
4    5
5    6
dtype: int64
```

您在上面看到的打印输出有两列。左边的是索引，右边的是你的数据。这个索引看起来像我们在使用列表、元组、数组或任何其他可迭代对象时所习惯的索引。我们很快就会在熊猫身上看到，我们可以把它改成我们喜欢的任何东西！

```
# DataFrame
future_array2 = [2,4,6,8,10,12]
array2 = np.array(future_array2)
df = pd.DataFrame([future_array1, future_array2])>>> df
   0  1  2  3   4   5
0  1  2  3  4   5   6
1  2  4  6  8  10  12
```

你在上面看到的打印结果有一大堆数字。左边的第一列是索引。第一行是列名(现在是 0…5)。同样，我们将很快在熊猫身上看到我们可以把它改成我们喜欢的任何东西！

**来自字典**

字典键将成为一系列中的索引

```
# Series
future_series = {0: 'A', 1: 'B', 2: 'C'}
s = pd.Series(future_series)>>> s
0    A
1    B
2    C
dtype: object
```

它在数据帧中的工作方式有点不同——键变成了列名

```
# DataFrame
dict = {'Normal': ['A', 'B', 'C'], 'Reverse': ['Z', 'Y', 'X']}
df = pd.DataFrame(dict)>>> df
  Normal Reverse
0      A       Z
1      B       Y
2      C       X
```

**上传数据**

Pandas 有很多方法[上传数据](https://pandas.pydata.org/pandas-docs/stable/cookbook.html#data-in-out)，但是让我们把重点放在标准的 csv 格式上。

```
uploaded_data = pd.read_csv("filename.csv", index_col=0)
```

关键字参数 **index_col** 可以指定 CSV 中的哪一列应该是 DataFrame 中的索引。有关 read_csv 函数的更多详细信息，请访问[此处](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_csv.html)。

我喜欢熊猫图书馆只需要一行代码就可以从 CSV 导入数据。还有谁从 csv 库中复制并粘贴了相同的代码行？；)

## 使用索引

你的文字争吵的日子结束了！不再有奇怪的列表理解或带有注释的 for 循环，如“#在给定期间提取此列”或“#很抱歉造成混乱”。

以下是一个示例数据帧:

```
dates = pd.date_range("20160101", periods=6)
data = np.random.random((6,3))
column_names = ['Column1', 'Column2', 'Column3']
df = pd.DataFrame(data, index=dates, columns=column_names)>>> df
             Column1   Column2   Column32016-01-01  0.704351  0.151919  0.5058812016-01-02  0.242099  0.887256  0.0695122016-01-03  0.683565  0.305862  0.2780662016-01-04  0.943801  0.388292  0.2213182016-01-05  0.353116  0.418686  0.0540112016-01-06  0.802379  0.720102  0.043310
```

**索引一列**

```
>>> df['Column2'] # use the column name's string2016-01-01    0.1519192016-01-02    0.8872562016-01-03    0.3058622016-01-04    0.3882922016-01-05    0.4186862016-01-06    0.720102Freq: D, Name: Column2, dtype: float64
```

**索引一行**

```
>>> df[0:2] # use the standard indexing technique Column1   Column2   Column32016-01-01  0.704351  0.151919  0.5058812016-01-02  0.242099  0.887256  0.069512 >>> df['20160101':'20160102'] # use the index's strings Column1   Column2   Column32016-01-01  0.704351  0.151919  0.5058812016-01-02  0.242099  0.887256  0.069512
```

**分度多轴-名称**

```
>>> df.loc['20160101':'20160102',['Column1','Column3']] Column1   Column32016-01-01  0.704351  0.5058812016-01-02  0.242099  0.069512
```

**多轴分度—编号**

```
>>> df.iloc[3:5, 0:2] Column1   Column22016-01-04  0.943801  0.3882922016-01-05  0.353116  0.418686
```

## 查看您的数据

快速检查顶行和底行:

```
>>> df.head(2) # first 2 rows Column1   Column2   Column32016-01-01  0.704351  0.151919  0.5058812016-01-02  0.242099  0.887256  0.069512 >>> df.tail(2) # last 2 rows Column1   Column2   Column32016-01-05  0.353116  0.418686  0.0540112016-01-06  0.802379  0.720102  0.043310
```

赶去开会前查看汇总统计数据:

```
>>> df.describe() Column1   Column2   Column3count  6.000000  6.000000  6.000000mean   0.621552  0.478686  0.195350std    0.269550  0.273359  0.180485min    0.242099  0.151919  0.04331025%    0.435728  0.326470  0.05788750%    0.693958  0.403489  0.14541575%    0.777872  0.644748  0.263879max    0.943801  0.887256  0.505881
```

## 控制您的数据

Pandas 为 Python 带来了 SQL 的灵活性。

**排序**

```
>>> df.sort_index(axis=0, ascending=False) # sort using the index Column1   Column2   Column32016-01-06  0.802379  0.720102  0.0433102016-01-05  0.353116  0.418686  0.0540112016-01-04  0.943801  0.388292  0.2213182016-01-03  0.683565  0.305862  0.2780662016-01-02  0.242099  0.887256  0.0695122016-01-01  0.704351  0.151919  0.505881 >>> df.sort_values(by='Column2') # sort using a column Column1   Column2   Column32016-01-01  0.704351  0.151919  0.5058812016-01-03  0.683565  0.305862  0.2780662016-01-04  0.943801  0.388292  0.2213182016-01-05  0.353116  0.418686  0.0540112016-01-06  0.802379  0.720102  0.0433102016-01-02  0.242099  0.887256  0.069512
```

**加入**

以下是新的示例数据框架:

```
dates1 = pd.date_range("20160101", periods=6)
data1 = np.random.random((6,2))
column_names1 = ['ColumnA', 'ColumnB']dates2 = pd.date_range("20160101", periods=7)
data2 = np.random.random((7,2))
column_names2 = ['ColumnC', 'ColumnD']df1 = pd.DataFrame(data1, index=dates1, columns=column_names1)
df2 = pd.DataFrame(data2, index=dates2, columns=column_names2)>>> df1.join(df2) # joins on the index ColumnA   ColumnB   ColumnC   ColumnD2016-01-01  0.128655  0.181495  0.574188  0.6285842016-01-02  0.278669  0.810805  0.634820  0.5455312016-01-03  0.489763  0.397794  0.169862  0.3006662016-01-04  0.911465  0.903353  0.058488  0.9111652016-01-05  0.094284  0.890642  0.282264  0.5680992016-01-06  0.512656  0.735082  0.141056  0.698386
```

如果您想连接索引以外的列，请检查[合并方法](https://pandas.pydata.org/pandas-docs/stable/merging.html#database-style-dataframe-joining-merging)。

**分组依据**

```
df3 = df1.join(df2)# add a column to df to group on
df3['ProfitLoss'] = pd.Series(['Profit', 'Loss', 'Profit', 'Profit', 'Profit', 'Loss'], index=dates)>>> df3.groupby('ProfitLoss').mean() ColumnA   ColumnB   ColumnC   ColumnDProfitLossLoss        0.403947  0.759588  0.272969  0.305868Profit      0.576668  0.477050  0.359661  0.406070
```

## 访问属性

注意我是如何在上面的代码中使用键/值符号来添加列的？Pandas 允许您轻松添加新数据。但它也允许您访问数据结构的核心属性。

**访问索引**

```
>>> df3.indexDatetimeIndex(['2016-01-01', '2016-01-02', '2016-01-03', '2016-01-04',
               '2016-01-05', '2016-01-06'],
              dtype='datetime64[ns]', freq='D')
```

**访问数值**

```
>>> df3.valuesarray([[0.441513594483238, 0.974419927787583, 0.20896018007846018,0.45913058454344435, 'Profit'], ...[0.6980963896232228, 0.7005669323477245, 0.09231336594380268,0.13264595083739117, 'Loss']], dtype=object)
```

**访问列**

```
>>> df3.columnsIndex([u'ColumnA', u'ColumnB', u'ColumnC', u'ColumnD', u'ProfitLoss'], dtype='object')
```

我在这里提供了一个[链接，用 Jupyter 笔记本](https://github.com/ljglass/datascience_notebooks)下载我的熊猫漫游！

以前没用过 Jupyter 笔记本？访问他们的网站[这里](http://jupyter.org)。

总的来说，如果你有一个数据集，你想操作，但不想麻烦地把它全部拖进 SQL，我建议先搜索熊猫解决方案！

## 应用程序

让我们看一个场景。假设你想关注比特币，但不想在基础设施建设上投入太多时间。你可以用熊猫来保持简单。

你需要一个 [Quandl](https://www.quandl.com) 账号和 [python Quandl 库](https://docs.quandl.com/docs/python-installation)。

```
pip install quandl
```

让我们编码:

```
import quandl# set up the Quandl connection
api_key = 'GETYOURAPIKEY'
quandl.ApiConfig.api_key = api_key
quandl_code = "BITSTAMP/USD"# get the data from the API
bitcoin_data = quandl.get(quandl_code, start_date="2017-01-01", end_date="2018-01-17", returns="numpy")# set up the data in pandas
df = pd.DataFrame(data=bitcoin_data, columns=['Date', 'High', 'Low', 'Last', 'Bid', 'Ask', 'Volume', 'VWAP'])# make the 'Date' column the index
df.set_index('Date', inplace=True) # find a rolling 30 day average
df['RollingMean'] = df['Last'].rolling(window=30).mean().shift(1)# label when the last price is less than L30D average
df['Buy'] = df['Last'] < df['RollingMean']# create a strategic trading DataFrame
trading_info = df.loc[:,['Last', 'RollingMean', 'Buy']]>>> trading_info.tail(10) # lets look at last 10 days Last   RollingMean    BuyDate2018-01-08  16173.98  15693.421333  False2018-01-09  15000.00  15704.147667   True2018-01-10  14397.30  15716.680333   True2018-01-11  14900.00  15706.590333   True2018-01-12  13220.00  15655.209333   True2018-01-13  13829.29  15539.209333   True2018-01-14  14189.66  15458.548000   True2018-01-15  13648.00  15384.760000   True2018-01-16  13581.66  15258.109000   True2018-01-17  11378.66  15070.668667   True
```

这就是熊猫用真实生活数据的力量！然而，如果我们想以图表的形式查看上面显示的数据呢？这是可能的，查看我在 Matplotlib 上的 n [ext 文章。](https://hackernoon.com/fundamental-python-data-science-libraries-a-cheatsheet-part-3-4-6c2aecc697a4)

感谢阅读！如果你有问题，请随意评论&我会尽量回复你。

![](img/a2693ae6881f4d0191d145a363aa8b5f.png)

在 Instagram 上与我联系[@ Lauren _ _ glass](https://www.instagram.com/lauren__glass/)&[LinkedIn](https://www.linkedin.com/in/laurenjglass/)

在亚马逊上查看我的[必需品清单](http://bit.ly/my_essentials)

[访问我的网站！](https://www.laurenglass.me)

![](img/704cab53566f8ef978de5e98726ddb63.png)

Search for me using my nametag on Instagram!