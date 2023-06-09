# 数据清理

> 原文：<https://medium.com/hackernoon/data-cleaning-3c3e37f358dc>

![](img/ff28d32fb906e4b0c7ef0f7608061db7.png)

***你知道吗，数据科学家花 80%的时间清理数据，另外 20%的时间抱怨数据？***

不要与[净化(机密信息)](https://en.wikipedia.org/wiki/Sanitization_(classified_information))或[数据清理](https://en.wikipedia.org/wiki/Data_scrubbing)相混淆

本博客将对当今数据科学中最热门的一些话题进行简单的解释，包括:

*   **什么是数据清洗**
*   **为什么需要数据清理**
*   **如何清理数据？**
*   **处理缺失值**

> **先看看数据**
> 
> **查看我们有多少缺失的数据点**
> 
> 找出数据丢失的原因
> 
> **删除缺失值**
> 
> **填写缺失值**

*   **缩放和归一化**

> **设置我们的环境**
> 
> **缩放与规范化:有什么区别？**
> 
> **练习缩放比例**
> 
> **练习正常化**

*   **解析日期**

> **设置我们的环境**
> 
> **检查日期列的数据类型**
> 
> **将我们的日期列转换为日期时间**
> 
> **从我们的列**中选择一个月中的某一天
> 
> **绘制一个月中的某一天来检查日期解析**

*   **字符编码**

> **设置我们的环境**
> 
> **有哪些编码？**
> 
> **读入有编码问题的文件**
> 
> **用 UTF 8 编码保存文件**

*   **数据输入不一致**

> **设置我们的环境**
> 
> **做一些初步的文本预处理**
> 
> **使用模糊匹配纠正不一致的数据输入**

# **什么是数据清洗？**

**数据清理**或**数据清理**是从记录集、[表](https://en.wikipedia.org/wiki/Table_(database))或[数据库](https://en.wikipedia.org/wiki/Database)中检测并纠正(或删除)损坏或不准确的[记录](https://en.wikipedia.org/wiki/Storage_record)的过程，是指识别数据中不完整、不正确、不准确或不相关的部分，然后替换、修改或删除[脏数据](https://en.wikipedia.org/wiki/Dirty_data)或粗糙数据。[【1】](https://en.wikipedia.org/wiki/Data_cleansing#cite_note-1)数据清洗可以通过[数据角力](https://en.wikipedia.org/wiki/Data_wrangling)工具[交互](https://en.wikipedia.org/wiki/Interactively)进行，也可以通过[脚本](https://en.wikipedia.org/wiki/Script_(computing))作为[批处理](https://en.wikipedia.org/wiki/Batch_processing)进行。参考了解更多[维基百科](https://en.wikipedia.org/wiki/Data_cleansing)

> **为什么需要数据清理？**

数据清理是一个有价值的过程，可以帮助公司节省时间和提高效率。[数据清理软件](http://www.winpure.com/)各种组织使用工具从营销列表、数据库和 CRM 中删除重复数据、修复和修改格式错误、不正确和不完整的数据。

一些优点是:-

*   **提高客户获取活动的效率**
*   **简化业务实践**
*   **增加收入**
*   **提高生产率**
*   **改进决策过程** ( [参见本文](https://www.invensis.net/blog/data-processing/5-advantages-of-data-cleansing/))

> **如何清理数据？**

如你所知，数据清理是一个重要的阶段，需要一段时间来清理数据。但是，如何清理数据呢？的数据怎么看起来像**？**如果我们的数据集中有缺失值，有哪些方法？如果我们的数据中有分类缺失值**会怎样？**我们的数据不一致怎么办**？**如果我们的数据集中缺少日期怎么办**？**

在这篇博客中，我将尝试详细回答和解释每一个案例，如何处理不干净的数据以及如何解决它们。

> **我们开始吧！！**
> 
> **处理缺失值**

我们需要做的第一件事是加载我们将要使用的库。我将使用美式足球比赛中发生的事件数据集进行演示。

当我得到一个新的数据集时，我做的第一件事就是看一看其中的一些。这让我看到所有数据都被正确读入，并了解数据的情况。在这种情况下，我想看看是否有任何丢失的值，这些值将用`NaN`或`None`表示。

> **第一步:导入库和数据集(** [**NFL 数据集**](https://www.kaggle.com/maxhorowitz/nflplaybyplay2009to2016) **)**

![](img/ee8a86e88f9880e03299a2433513a19c.png)![](img/f09f7285e565e7c5f24bebe10b122a18.png)

> **第二步:检查缺失的数据点**

![](img/beae85866bfb7d66cea2810043d75750.png)![](img/831c2d2c0f484b79eeba8354c232e33e.png)

> **第三步:从不推荐(删除缺失值)**

![](img/5ba8ff05c2878f839b424421e39ba8b1.png)![](img/f02eb947b0eb5b6443e34b3014ce4bb0.png)

> **第四步:自动填充缺失值**

![](img/f6ee5825b6f9988ed3f29ba071f7a92b.png)![](img/a39665c066423b5eb0e9d169ebb41a33.png)![](img/05e133405e3e73966b1a532dadc61fe7.png)![](img/cd8ebbb14bd12186969c37c834d53da2.png)

> **缩放和归一化**
> 
> **第一步:导入库和数据集(**[**Kickstarter**](https://www.kaggle.com/kemical/kickstarter-projects)**)**

![](img/404289383eada31f970cebc96c949105.png)

# 缩放与正常化:有什么区别？

**缩放和规范化之间容易混淆的原因之一是，这两个术语有时会互换使用，更令人困惑的是，它们非常相似！在这两种情况下，您都在转换数值变量的值，以便转换后的数据点具有特定的有用属性。不同之处在于，在缩放时，你改变了数据的*范围*，而在归一化时，你改变了数据的分布的*形状。让我们更深入地讨论一下这些选项。***

# 缩放比例

这意味着您正在转换您的数据，使其符合特定的范围，如 0-100 或 0-1。当您使用基于数据点相距多远的测量方法时，您想要缩放数据，如 [**支持向量机，或【SVM】**](https://en.wikipedia.org/wiki/Support_vector_machine)**或**[**k-最近邻，或【KNN】**](https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm)**。利用这些算法，任何数字特征中的“1”的变化被赋予相同的重要性。**

**例如，您可能会同时查看某些产品的日元和美元价格。一美元大约相当于 100 日元，但是如果你不称一下你的价格，像 SVM 或 KNN 这样的方法会认为 1 日元的差价和 1 美元的差价一样重要！这显然不符合我们对世界的直觉。使用货币，您可以在货币之间进行转换。但是如果你关注的是身高和体重呢？还不完全清楚多少磅应该等于一英寸(或者多少千克应该等于一米)。**

通过调整你的变量，你可以平等地比较不同的变量。为了帮助巩固扩展的样子，让我们看一个虚构的例子。(不要担心，我们马上会使用真实数据，这只是为了帮助说明我的观点。)

> **步骤 2:使用最小-最大缩放来缩放数据**

![](img/5f1c5aae82c1a3f6abdaed239fd3b132.png)![](img/46170262557982c326fea4efcea65006.png)

# 正常化

**缩放只是改变了数据的范围。正常化是一种更激进的转变。标准化的要点是改变你的观察值，使它们可以被描述为正态分布。**

> [**正态分布:**](https://en.wikipedia.org/wiki/Normal_distribution) **也称为“钟形曲线”，这是一种特定的统计分布，其中一个大致相等的观察值落在均值之上和之下，均值和中位数相同，并且有更多的观察值更接近均值。正态分布也称为高斯分布。**

一般来说，如果你要使用机器学习或统计技术，假设你的数据是正态分布的，你只需要将你的数据标准化。其中的一些例子包括 t 检验、ANOVAs、线性回归、线性判别分析(LDA)和高斯朴素贝叶斯。(专业提示:任何名称中带有“高斯”的方法都可能假设正态性。)

**我们这里用来归一化的方法叫做** [**Box-Cox 变换**](https://en.wikipedia.org/wiki/Power_transform#Box%E2%80%93Cox_transformation) **。让我们快速地看一下规范化一些数据是什么样子的:**

> **步骤 3:使用 Box-Cox 变换进行归一化**

![](img/bf4caf83f66cd43d97a927d29fb609bf.png)![](img/4ed993bd425bb1298b549a9c36861ec9.png)![](img/9132b2870be4e3d507103cdb495b5545.png)![](img/964a37bac311ce1e068ab8251e0bf6c4.png)![](img/3a952d94f1b935c83925f3ccbc175b04.png)![](img/23a05adc95cf4b6a80a56f444f92cee2.png)![](img/790c35da7e8b4e4d9450cf4cfe4e2c6b.png)![](img/750f16acded4df6662280a5fb8b575b6.png)

> **这并不完美(看起来很多人都获得了很少的承诺)，但已经非常接近正常水平了！**

![](img/4efb7182d545eede39c657df41246ec0.png)![](img/ad1e6b14b5f3c49375652c9ea0947a48.png)

> **解析日期**
> 
> **第一步:导入库和数据集(** [**滑坡**](https://www.kaggle.com/nasa/landslide-events) **)**

我们需要做的第一件事是加载我们将要使用的库和数据集。今天，我们将使用数据集:包含 2007 年至 2016 年间发生的山体滑坡的信息。

![](img/fd008d1ac86bf6edee298316dfcdf05f.png)

> **第二步:检查我们的日期列的数据类型**

在这部分挑战中，我将使用滑坡数据框中的日期列。我要做的第一件事是看一眼前几行，确保它看起来确实包含日期。

![](img/61dfb73b0fdc6e32922459363cef6c76.png)

是的，那些是日期！但是仅仅因为我，一个人类，能够分辨出这些是日期，并不意味着 Python 知道它们是日期。注意 head()输出底部的，可以看到它说这个列的数据类型是“object”。

Pandas 使用“object”dtype 来存储各种类型的数据类型，但最常见的情况是，当您看到 dtype 为“object”的列时，其中会有字符串。

如果您查看这里的 pandas dtype 文档，您会注意到还有一个特定的 datetime64 dtypes。因为我们的列的 dtype 是 object 而不是 datetime64，所以我们可以看出 Python 不知道这个列包含日期。

如果愿意，我们也可以只查看列的数据类型，而不打印前几行:

![](img/8f9ba5264453256d9f01ec01b24fa909.png)

您可能需要查看 numpy 文档，以将字母代码与对象的数据类型相匹配。“O”是“object”的代码，所以我们可以看到这两个方法给我们的信息是一样的。

> **步骤 3:将我们的日期列转换成日期时间**

现在我们知道我们的日期列没有被识别为日期，是时候转换它，使它被识别为日期了。这被称为“解析日期”，因为我们接受一个字符串并识别它的组成部分。

我们可以用一个叫做“strftime 指令”的指南来告诉你我们约会的格式，你可以在这个链接找到更多的信息。基本的想法是，你需要指出日期的哪些部分在哪里，它们之间有什么标点符号。日期有许多可能的组成部分，但最常见的是%d 代表日，%m 代表月，%y 代表两位数的年，%Y 代表四位数的年。

一些例子:

2007 年 1 月 17 日的格式为“%m/%d/%y”

回头看看滑坡数据集中日期列的开头，我们可以看到它的格式是“月/日/两位数的年”，因此我们可以使用与第一个示例相同的语法来解析我们的日期:

![](img/4934bc9a4afe14b94b7a4488fd617a64.png)

既然我们的日期被正确地解析了，我们可以用有用的方式与它们交互。

如果我遇到多种日期格式的错误怎么办？当我们在这里指定日期格式时，当一列中有多种日期格式时，有时会遇到错误。如果发生这种情况，你让熊猫试着推断正确的日期格式应该是什么。您可以这样做:slides[' Date _ parsed ']= PD . to _ datetime(slides[' Date ']，infer_datetime_format=True)

为什么不总是用 infer_datetime_format = True？有两个主要原因让熊猫不要总是猜测时间格式。首先，熊猫并不总是能够找出正确的日期格式，尤其是如果有人在数据输入方面很有创意的话。第二，它比指定日期的精确格式要慢得多。

# 从我们的列中只选择一个月中的某一天

"我想，这种对数据类型的摆弄是好的，但是有什么意义呢？"为了回答您的问题，让我们尝试从原始的“日期”列中获取滑坡发生的日期信息，该列具有“对象”数据类型:

![](img/39d3cb4d12674ca07d6c40e5e125ff6e.png)

我们出错了！这里要看的重要部分是最末尾的部分，写着 AttributeError:只能使用。具有 datetimelike 值的 dt 访问器。我们得到这个错误是因为 dt.day()函数不知道如何处理 dtype 为“object”的列。即使我们的数据框中有日期，因为它们还没有被解析，所以我们不能以一种有用的方式与它们交互。

幸运的是，我们有一个之前解析过的列，这让我们可以毫无问题地得到一个月中的某一天:

![](img/c724b1174427c3af245774541f4bc9e4.png)

> **步骤 4:画出一个月中的某一天来检查日期解析**

解析日期的最大危险之一是混淆月和日。to_datetime()函数确实有非常有用的错误消息，但是仔细检查一下我们提取的一个月中的日期是否有意义也无妨。

为了做到这一点，让我们绘制一个月中各天的直方图。我们预计它的值在 1 到 31 之间，因为没有理由假设滑坡在一个月的某些日子比其他日子更常见，所以这是一个相对均匀的分布。(31 日有所下降，因为不是所有月份都有 31 天。)我们来看看是不是这样:

![](img/0dbcd7c28d1c93ef9a18cb6a6d6ed66c.png)

> **字符编码**
> 
> **第一步:导入库和数据集(**[**Kickstarter**](https://www.kaggle.com/kemical/kickstarter-projects)**)**

![](img/4164421ae31b5f8fa2a25faf3307f953.png)

# 什么是编码？

字符编码是从原始二进制字节字符串(看起来像这样:0110100001101001)映射到组成人类可读文本的字符(像“hi”)的特定规则集。有许多不同的编码，如果你试图用一种不同于最初编写的编码来读取文本，你最终会得到一个被称为“mojibake”的加扰文本(就像 mo-gee-bah-kay 一样)。这里有一个 mojibake 的例子:

æ–‡å — åŒ–ã??

你也可能以一个“未知”字符结束。当某个特定的字节和您用来读取字节字符串的编码中的某个字符之间没有映射时，就会打印出来，如下所示:

����������

字符编码不匹配现在不像以前那么普遍了，但它肯定仍然是一个问题。有许多不同的字符编码，但你需要知道的主要一种是 UTF-8。

> UTF-8 是**的**标准文本编码。所有的 Python 代码都是 UTF 8 版本，理想情况下，你所有的数据也应该是这样。当事情不在 UTF-8 时，你就会遇到麻烦。

在 Python 2 中处理编码相当困难，但幸运的是在 Python 3 中要简单得多。(Kaggle 内核只用 Python 3。)在 Python 3 中处理文本时，您会遇到两种主要的数据类型。一个是字符串，默认情况下是文本。

![](img/857ce1c2dadb4d15604addbca22ae6f3.png)

如果你看一个 bytes 对象，你会看到它前面有一个 b，后面可能还有一些文本。这是因为字节被打印出来，就好像它们是用 ASCII 编码的字符一样。(ASCII 是一种较老的字符编码，它实际上不适合写除英语之外的任何语言。)在这里，您可以看到我们的欧元符号被替换为一些 mojibake，当它被打印出来时，看起来像是“\xe2\x82\xac ”,就像它是一个 ASCII 字符串一样。

![](img/586ece4f9ad83af395f1ae50b3757fe5.png)

但是，当我们尝试使用不同的编码将字节映射到字符串时，我们会得到一个错误。这是因为我们试图使用的编码不知道如何处理我们试图传递给它的字节。您需要告诉 Python 字节串实际应该采用的编码。

您可以将不同的编码视为录制音乐的不同方式。你可以将同一首音乐录制在 CD、盒式磁带或 8 轨唱片上。虽然音乐听起来可能大同小异，但您需要使用正确的设备来播放每种录音格式的音乐。正确的解码器就像卡带播放器或 cd 播放器。如果你试着用激光唱机播放磁带，它就不会工作。

![](img/d6324cea5f5451eb4034752da2c769ca.png)

如果我们试图使用错误的编码从字符串映射到字节，我们也会遇到麻烦。就像我前面说的，在 Python 3 中，字符串默认是 UTF-8，所以如果我们试图把它们当作另一种编码，我们就会产生问题。

例如，如果我们试图使用 encode()将一个字符串转换为 ascii 的字节，我们可以要求字节是 ASCII 文本的字节。因为我们的文本不是 ASCII 格式的，所以会有一些它不能处理的字符。我们可以自动替换 ASCII 不能处理的字符。但是，如果我们这样做，任何不在 ASCII 中的字符都将被替换为未知字符。然后，当我们将字节转换回字符串时，该字符将被未知字符替换。危险的是，没有办法判断它应该是哪个角色。这意味着我们可能已经使我们的数据不可用了！

![](img/459bdebadbe2bfb8ec4efd009c4a6a65.png)

这是不好的，我们想避免这样做！最好是尽快将我们所有的文本转换成 UTF-8 格式，并保持这种编码。将非 UTF-8 输入转换为 UTF-8 输入的最佳时间是当你读入文件时，我们将在下面讨论。

阅读有编码问题的文件大多数你会遇到的文件可能是用 UTF-8 编码的。这是 Python 默认期望的，所以大多数时候你不会遇到问题。然而，有时您会得到这样的错误:

![](img/1d0acee0176c68d534dba38be95ac7e6.png)

> 请注意，当我们试图将 UTF 8 字节解码为 ASCII 码时，我们得到了相同的 unicode decode 错误！这告诉我们这个文件实际上不是 UTF-8。我们不知道它实际上是什么编码。解决这个问题的一种方法是尝试和测试一系列不同的字符编码，看看它们中是否有任何一种有效。不过，更好的方法是使用 chardet 模块尝试并自动猜测正确的编码。虽然不能保证 100%正确，但通常比仅仅猜测要快。

我只看这个文件的前一万个字节。这通常足以猜测编码是什么，并且比试图查看整个文件要快得多。(特别是对于大文件，这可能会非常慢。)只查看文件第一部分的另一个原因是，通过查看错误消息，我们可以看到第一个问题是第 11 个字符。因此，我们可能只需要查看文件的第一小段就能弄清楚发生了什么。

![](img/94ef60312102a7b919a3dcd7fe28abb3.png)![](img/428695ef956ed13f6a87a07b0bbd9329.png)

是啊，看来查德是对的！文件读入没有问题(尽管我们确实得到了一个关于数据类型的警告),当我们查看前几行时，它似乎是好的。

> **数据输入不一致**
> 
> **第一步:加载库和数据集(** [**【巴基斯坦自杀式袭击】**](https://www.kaggle.com/zusmani/pakistansuicideattacks) **)**

![](img/fd80a7ebf254e0c180a7b0ace27f7fcc.png)

当我第一次尝试读取`PakistanSuicideAttacks Ver 11 (30-November-2017).csv`文件时，我得到了一个字符编码错误，所以我将快速检查编码应该是什么。

![](img/99546aac2340d6b85cefd59e870cf5c4.png)![](img/1ce5de91297eadebf699220b6a8e54d2.png)

现在我们准备好开始了！您可以一如既往地在这里花一点时间查看数据并熟悉它。

# 做一些初步的文本预处理

对于这个练习，我感兴趣的是清理“City”列，以确保其中没有数据输入不一致。当然，我们可以手工检查每一行，并在发现不一致时手工纠正。不过，有一种更有效的方法可以做到这一点！

![](img/9b62251c8081d99cfd33c2f5f9e3292f.png)

**仅仅看这个，我就能看出一些由于数据输入不一致而产生的问题:例如:“拉合尔”和“拉合尔”，或者“拉基马尔瓦特”和“拉基马尔瓦特”。**

**我要做的第一件事是将所有内容都变成小写(如果我愿意，我可以在末尾将其改回小写),并删除单元格开头和结尾的任何空格。在文本数据中，大写和尾随空格的不一致是非常常见的，通过这样做，您可以修复 80%的文本数据输入不一致。**

![](img/dfe4d0ca6de8390ad971413914d23da6.png)

# 使用模糊匹配纠正不一致的数据输入

好的，让我们再看一下 city 列，看看我们是否还需要清理数据。

![](img/8d633aaac28d644494ab89562dfaafe3.png)

看起来确实还有一些不一致的地方:“d. i khan”和“d.i khan”应该是一样的。(我[查了一下](https://en.wikipedia.org/wiki/List_of_most_populous_cities_in_Pakistan)和‘d . g Khan’是一个单独的城市，所以我不应该把这两个结合起来。)

我将使用 [fuzzywuzzy](https://github.com/seatgeek/fuzzywuzzy) 包来帮助识别哪些字符串彼此最接近。这个数据集足够小，我们也许可以手动纠正错误，但这种方法不能很好地扩展。(你会想要手动纠正一千个错误吗？一万呢？尽早实现自动化通常是个好主意。另外，这很有趣！

> **模糊匹配:**自动寻找与目标字符串非常相似的文本字符串的过程。一般来说，如果将一个字符串转换为另一个字符串，需要更改的字符越少，就认为该字符串与另一个字符串越“接近”。所以“apple”和“snapple”相距两个变化(加上“s”和“n”)，而“in”和“on”相距一个变化(r 用“o”代替“I”)。你不可能总是 100%地依赖模糊匹配，但它通常会为你节省一点时间。

给定两个字符串，Fuzzywuzzy 返回一个比率。比率越接近 100，两个字符串之间的编辑距离就越小。这里，我们将从距离“d.i khan”最近的城市列表中获取十个字符串。

![](img/a2cd5170835ddcec1afe5ee1a27bf3aa.png)

**我们可以看到，城市中有两项与“d. i khan”非常接近:“d. i khan”和“d. i khan”。我们还可以看到“d.g khan ”,这是一个独立的城市，比例为 88。既然我们不想将“d.g khan”替换为“d.i khan”，那么就将我们的 City 列中所有比率为> 90 的行替换为“d. i khan”。**

**为此，我要写一个函数。(如果您认为您可能必须不止一次或两次地执行某个特定任务，那么编写一个您可以重用的通用函数是一个好主意。这使您不必过于频繁地复制和粘贴代码，从而节省时间并有助于防止错误。)**

![](img/aaedc964ef804adaedc509b8335256ec.png)

> 现在我们有了一个函数，我们可以测试它了！

![](img/6a4d6a6b26e3806285c1426004a6529c.png)

> 现在，让我们再次检查 City 列中的唯一值，确保我们已经正确整理了 d.i khan。

![](img/7d5f49c3d0481036e3a4e4f59c462cef.png)

> **太棒了！现在，我们的数据框中只有“d.i khan ”,我们无需手动更改任何内容。**

# 如果你喜欢这个教程，请分享、评论并订阅我的博客

# [你想要的男人](https://themenyouwanttobe.wordpress.com/)

## 分享一些爱❤