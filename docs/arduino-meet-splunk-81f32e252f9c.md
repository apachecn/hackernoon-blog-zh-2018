# Arduino，认识一下 Splunk

> 原文：<https://medium.com/hackernoon/arduino-meet-splunk-81f32e252f9c>

![](img/f6256fb38b1994f9524d98d7af37408e.png)

Diy 物联网非常有趣，而且准入门槛相当低。市面上有连接 wifi 的 arduino 板，价格为 10-20 美元。很多时候，这些电路板会被传感器塞满，能够轻松监控周围环境。例子包括温度、湿度、气压、光线和运动。[https://www.adafruit.com/category/35](https://www.adafruit.com/category/35)构建更多的传感器网关并从“dunios”中收集数据会迅速提升学习曲线，并让试图从电路板收集并处理数据的开发人员很快受挫。使用两个库，可以快速简单地将这些数据推送到个人 splunk 实例中。这是通过对 Splunk Http 事件收集器或“hec”的简单网络发布来完成的从那里天空是极限。

首先什么是 arduino？https://www.arduino.cc/[Arduinos 是对微控制器板的全面阐述。一些是配置的裸机，其他是具有额外内存空间和突破的 esp8266 芯片。本说明中使用的电路板是 Adafruit“Huzzah”8266 分线板。https://www.adafruit.com/product/2471](https://www.arduino.cc/)T4【使用这些是因为材料成本，一个 arduino Uno 板是入门级板，售价 20 美元。这是一个裸露的微控制器，然后需要第二个“屏蔽”，以便将 wifi 控制器捆绑到其上。得到一个简单便宜的 huzzah 要容易得多。Adafruit 有一个很棒的教程和入门指南社区，可以下载和配置 Arduino IDE，然后安装适当的库来支持 huzzah 板。然后这些库被用于 http 和 wifi 客户端。看一看，在这里配置 IDE 和添加板。[https://learn.adafruit.com/add-boards-arduino-v164?view=all](https://learn.adafruit.com/add-boards-arduino-v164?view=all)

在深入研究 Arduino“草图代码”之前，应该理解我们将要看到的那种代码。Arduinos 是一个非常友好的“c”，有很多强类型语言，但是 String 是可用的，整个编译和上传过程是通过一个漂亮的 gui 完成的。它也可以通过 makefiles 之类的工具来完成，但是在 Arduino IDE 中调试器至少要好一点。建议从那里入手。C 根的一个结果是，处理 http 请求会有点不太用户友好，但并不可怕。在提交 http post 请求之前，需要添加 http 头，然后对内容长度求和，最后将整个消息打包。虽然这不是世界末日，但如果有人不熟悉 http 的工作方式，这可能会令人困惑。不要担心，花些时间阅读代码，它会变得有意义。

![](img/7340d21445564afaf7bf1dcce8b63a41.png)

只有 100 多行代码，希望不算太糟糕。大部分工作被分成几个职能部门。Loop()函数一直在运行，因为这就是 arduino 的工作方式。Setup()在引导时运行一次。然后，其他所有东西只有在被调用时才运行。Loop()收集一些虚拟数据，并将其传递给 splunkpost()。如果需要更精细的数据收集，可以很容易地从循环中调用它，然后返回并发送给 splunkpost()。Splunk post 接收开始时为 Splunk 服务器配置的所有参数、HEC 和一些元数据。有一个问题，http 收集器是硬编码在固件中的，有更优雅和可配置的方式来做到这一点，但是对于这个目的来说，这是很好的。应该注意的是，如果港灯要改变，将需要重建和刷新。

这就是所有的人！arduino 连接到 wifi 网络，然后通过 HEC 将数据直接发送到 Splunk。所有这些都不需要任何其他东西，没有数据网关，没有屏蔽层之间的串行位碰撞，以将传感器推至 wifi 板。只是 DiY arduino 的传感器数据进入 Splunk 的强大功能。通过 Splunk，所有的分析数据处理和警报都可以完成。希望这足以描述轮子的转动和数据流。

**链接:**

[](https://www.arduino.cc/) [## Arduino - Home

### 开源电子原型平台，使用户能够创建交互式电子对象。

www.arduino.cc](https://www.arduino.cc/) [](https://www.adafruit.com/product/2471) [## Adafruit HUZZAH ESP8266 分线点

### 阿达果产业，独特的&有趣的 DIY 电子和工具包阿达果 HUZZAH ESP8266 突破 ID: 2471 -添加互联网…

www.adafruit.com](https://www.adafruit.com/product/2471) [](https://learn.adafruit.com/add-boards-arduino-v164?view=all) [## 概述|向 Arduino v1.6.4+ IDE 添加第三方板| Adafruit 学习系统

### Arduino IDE 的 1.6.4 版本引入了对添加第三方电路板的官方支持(如 Adafruit Flora 和…

learn.adafruit.com](https://learn.adafruit.com/add-boards-arduino-v164?view=all) [](https://learn.adafruit.com/adafruit-huzzah-esp8266-breakout) [## 概述| Adafruit HUZZAH ESP8266 分组讨论| Adafruit 学习系统

### HUZZAH ESP8266 breakout 旨在让使用这款芯片变得超级简单和充满乐趣。我们采取了…

learn.adafruit.com](https://learn.adafruit.com/adafruit-huzzah-esp8266-breakout) [](https://www.adafruit.com/category/35) [## 传感器:Adafruit Industries，独特而有趣的 DIY 电子产品和套件

### Adafruit Industries，独特的&有趣的 DIY 电子产品和套件:传感器-工具礼券 Arduino 电缆…

www.adafruit.com](https://www.adafruit.com/category/35)  [## 关于 Splunk HTTP 事件收集器- Splunk 文档

### 令牌管理员可以是 Splunk 企业或自助 Splunk 云管理员，也可以是不具备以下条件的其他人

docs.splunk.com](http://docs.splunk.com/Documentation/Splunk/7.1.3/Data/AboutHEC)