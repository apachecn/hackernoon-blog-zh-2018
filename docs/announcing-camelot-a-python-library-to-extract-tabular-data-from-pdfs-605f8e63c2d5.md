# 宣布推出 Camelot，这是一个从 pdf 中提取表格数据的 Python 库

> 原文：<https://medium.com/hackernoon/announcing-camelot-a-python-library-to-extract-tabular-data-from-pdfs-605f8e63c2d5>

![](img/488027bf15fef46192d68d2bddbae9f9.png)

Photo by [Carles Rabada](https://unsplash.com/photos/05ci_hxKWr4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

PDF([Portable Document Format](https://en.wikipedia.org/wiki/PDF))诞生于[Camelot 项目](http://www.planetpdf.com/planetpdf/pdfs/warnock_camelot.pdf)，旨在创建“一种在各种机器配置、操作系统和通信网络之间交流文档的通用方式”。基本上，目标是使文档可以在任何显示器上查看，并可以在任何现代打印机上打印。PDF 建立在 [PostScript](https://en.wikipedia.org/wiki/PostScript) (一种页面描述语言)之上，它已经解决了这个“随处查看和打印”的问题。PDF 封装了创建“随处查看和打印”文档所需的组件。这些包括字符、字体、图形和图像。

PDF 文件定义了将字符(和其他组件)放置在相对于页面左下角的精确的 *x，y* 坐标上的指令。通过将一些字符放得比其他字符更近来模拟单词。类似地，通过将单词放置在相对较远的位置来模拟空格。那么表格是如何模拟的呢？你猜对了——通过将单词放在电子表格中。

PDF 格式没有表格结构的内部表示，这使得很难提取表格进行分析。可悲的是，许多开放的数据存储在 pdf 中，而 pdf 最初并不是为表格数据设计的！

# 卡米洛特:人类 PDF 表格提取

今天，我们很高兴地宣布 Camelot 的发布，这是一个 Python 库和命令行工具，任何人都可以轻松地提取 PDF 文件中的数据表！你可以在[查看文档，阅读文档](https://camelot-py.readthedocs.io/)并关注 [GitHub](https://github.com/camelot-dev/camelot) 的开发。

# 如何安装卡梅洛特

安装简单！在[安装完依赖项](https://camelot-py.readthedocs.io/en/latest/user/install.html#install)后，您可以使用 pip(安装 Python 包的推荐工具)安装 Camelot:

```
$ pip install camelot-py
```

# 如何使用卡梅洛特

使用 Camelot 从 PDF 中提取表格非常简单。这是你怎么做的。([下面是下例中使用的 PDF](https://camelot-py.readthedocs.io/en/latest/_static/pdf/foo.pdf) 。)

```
>>> import camelot
>>> tables = camelot.read_pdf('foo.pdf')
>>> tables
<TableList n=1>
>>> tables.export('foo.csv', f='csv', compress=True) # json, excel, html
>>> tables[0]
<Table shape=(7, 7)>
>>> tables[0].parsing_report
{
    'accuracy': 99.02,
    'whitespace': 12.24,
    'order': 1,
    'page': 1
}
>>> tables[0].to_csv('foo.csv') # to_json, to_excel, to_html
>>> tables[0].df # get a pandas DataFrame!
```

你也可以查看一下[命令行界面](https://camelot-py.readthedocs.io/en/latest/user/cli.html)。

# 为什么要用卡梅洛特？

*   Camelot 通过调整设置，让您完全控制表提取。
*   可以根据准确性和空白等指标丢弃坏表，而不必手动查看每个表。
*   每个表都是一个[熊猫数据框架](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)，无缝集成到 [ETL 和数据分析工作流](https://gist.github.com/vinayak-mehta/e5949f7c2410a0e12f25d3682dc9e873)。
*   您可以将表格导出为多种格式，包括 CSV、JSON、Excel 和 HTML。

# 好吧，但是为什么要另一个 PDF 表格提取库呢？

# TL；DR:全面控制，实现更好的表格提取

许多人使用开放( [Tabula](http://tabula.technology/) 、 [pdf-table-extract](https://github.com/ashima/pdf-table-extract) )和闭源( [smallpdf](https://smallpdf.com/) 、 [pdftables](https://pdftables.com/) )工具从 pdf 中提取表格。但是他们要么给出一个好的输出，要么悲惨地失败。没有中间地带。这是没有帮助的，因为现实世界中的一切，包括 PDF 表格提取，都是模糊的。这导致为每种类型的 PDF 表格创建专门的表格提取脚本。

我们创建了 Camelot 来为用户提供对表提取的完全控制。如果你不能用默认设置得到你想要的输出，你可以调整它们并完成工作！

您可以查看 Camelot 的输出与其他开源 PDF 表格提取库的比较[。](https://github.com/camelot-dev/camelot/wiki/Comparison-with-other-PDF-Table-Extraction-libraries-and-tools)

# 阅读时间越长

我们经常需要提取 pdf 中的数据。

我们尝试的第一个工具是 [Tabula](http://tabula.technology/) ，它有很好的用户和命令行界面，但是它要么工作得很完美，要么失败得很惨。当它失败时，很难调整设置，如图像阈值参数，这些参数会影响表格检测，并可能导致更好的输出。

我们还尝试了像 [smallpdf](https://smallpdf.com/) 和 [pdftables](https://pdftables.com/) 这样的闭源工具，它们比 Tabula 工作得稍微好一点。但话说回来，他们也不允许调整和成本的钱。(我们在 2015 年写了一篇关于如何从 PDF 中提取表格的博文，标题是[“PDF 是邪恶的”](https://blog.socialcops.com/technology/engineering/pdf-evil-extracting-tabular-data-pdfs/)。)

当这些成熟的 PDF 表格提取工具不起作用时，我们尝试了[PDF text](https://en.wikipedia.org/wiki/Pdftotext)(一个开源命令行实用程序)。pdftotext 使用空格从 PDF 中提取文本，同时保留布局。获得文本后，我们必须用复杂的正则表达式([正则表达式](https://en.wikipedia.org/wiki/Regular_expression))编写 Python 脚本，将文本转换成表格。这是不可伸缩的，因为我们必须为每个新的表格布局更改 regexs。

我们显然需要一个可调整的 PDF 表格提取工具，所以我们在 2015 年 12 月开始开发一个。我们从把工具还给社区的想法开始，社区给了我们这么多开源工具。

我们知道 Tabula 将 PDF 表格分为两类。它有两种方法来提取这些不同的类:Lattice(提取单元格之间有明确定义的线条的表格)和 Stream(提取单元格之间有空格的表格)。我们以 Tabula 的方法命名了卡梅洛特的萃取风味，格子和溪流。

对于 Lattice， [Tabula 使用霍夫变换](https://www.propublica.org/nerds/heart-of-nerd-darkness-why-dollars-for-docs-was-so-difficult)，一种图像处理技术来检测线条。因为我们想使用 Python， [OpenCV](https://en.wikipedia.org/wiki/OpenCV) 是进行图像处理的明显选择。然而，OpenCV 的[霍夫线变换](https://docs.opencv.org/2.4/doc/tutorials/imgproc/imgtrans/hough_lines/hough_lines.html)只返回线方程。经过更多的探索，我们决定采用[形态变换](https://docs.opencv.org/3.4/d9/d61/tutorial_py_morphological_ops.html)，它给出了精确的线段。从这里开始，表示 PDF 中的表格变得简单明了。

要获得更多关于 Lattice 和 Stream 如何在 Camelot 中工作的信息，请查看文档的[“它如何工作”](https://camelot-py.readthedocs.io/en/latest/user/how-it-works.html)部分。

# 我们如何利用卡梅洛特

我们已经通过在各种项目中使用 Camelot 进行了实战测试，包括一次性的和自动化的表提取。

今年早些时候，我们开发了[联合国可持续发展目标解决方案](https://socialcops.com/solutions/sdg-tracking/)，以帮助组织跟踪和衡量他们对【2030 议程】的贡献。对于印度，我们为 17 个可持续发展目标中的每一个确定了开放数据源(主要是 PDF 报告)。例如，目标 3(“人民的健康和福祉”)的来源之一是由 IIPS 发布的全国家庭健康调查(NFHS)报告。为了从这些 PDF 源中获取数据，我们在 Camelot 的基础上创建了一个内部 web 界面，我们的数据分析师可以上传 PDF 报告，并以他们喜欢的格式提取表格。

*注:* [*我们在 2018 年 2 月成为了联合国 SDG 行动奖的入围者*](https://blog.socialcops.com/inside-sc/press/un-sdg-action-awards-finalist/) *。*

我们还建立了一个 [ETL 工作流，使用 Apache Airflow 来跟踪印度的疾病爆发](https://blog.socialcops.com/technology/data-science/apache-airflow-disease-outbreaks-india/)。该工作流程从[综合疾病监测规划(IDSP)](http://www.idsp.nic.in/index4.php?lang=1&level=0&linkid=406&lid=3689) 网站中搜寻疾病爆发数据的每周 pdf，然后使用 Camelot 从 pdf 中提取表格，向我们的团队发送警报，并将数据加载到数据仓库中。

# 到无限和更远的地方！

卡梅洛特有些局限性。(我们正在开发解决方案！)这里有几个例子:

*   使用 Stream 时，不会自动检测表格。Stream 将整个页面视为单个表，当页面上有多个表时，这将产生不好的输出。
*   Camelot 只能处理基于文本的 pdf，不能处理扫描的文档。(正如 Tabula [对](https://github.com/tabulapdf/tabula#why-tabula)的解释，“如果您可以在 PDF 查看器中点击并拖动来选择表格中的文本，那么您的 PDF 就是基于文本的”。)

你可以查看 [GitHub 库](https://github.com/camelot-dev/camelot)以获得更多信息。

你也可以帮忙——每一份贡献都很重要！查看[贡献者指南](https://camelot-py.readthedocs.io/en/latest/dev/contributing.html)，了解关于贡献代码、文档或测试、报告问题和提出改进的指南。你也可以前往[问题追踪](https://github.com/camelot-dev/camelot/issues)，寻找标有“求助”和“好的第一期”的问题。

我们敦促组织以类似于 [CSV](https://en.wikipedia.org/wiki/Comma-separated_values) 的“数据友好”格式发布开放数据。但是当表格被困在 PDF 文件中时，还有卡梅洛特:)