# 写代码需要懂计算机科学吗？

> 原文：<https://medium.com/hackernoon/do-you-need-to-know-computer-science-to-write-code-dd155c517716>

![](img/229af932692600f9f3bd1d39e9470285.png)

我将从一个故事开始，这个故事与我写的关于[在晚年学习编码](/@aaronedell/learning-to-code-later-in-life-eb4226e53e69)的文章没有太大的不同。我害怕学习编码，但部分原因是因为不知何故，我认为你需要成为数学和工程的结合体才能做到这一点。

我整个学术生涯都在与数学作斗争，偶尔表现不错，但大多数时候不明白到底发生了什么(我在看你的微积分)。我认为这是学习编写代码的一大障碍，因为我的文化理解中关于编程的一切似乎都表明，要成为一名开发人员，你必须是计算机科学专业的学生。但是我后来了解到这是不正确的，事实上，我要证明相反的观点。

在我对计算机编程的业余的、也许过于简单的观点中，我看到了几种不同类型的代码，以及与技能、兴趣和能力相关的代码应用。我不认为情况总是如此，因为曾经有一段时间计算机科学是数学系的领域。穿孔卡片时代的机器被用来解决复杂的数学问题，随着计算机科学的发展，其背后的工程原理也在发展。但是随之而来的是抽象层。

[抽象对技术进步至关重要](https://www.linkedin.com/pulse/abstracting-abstract-abstractions-aaron-edell/)，部分原因是它使 mindshare 能够追求更复杂、更宏大的社会问题解决方案。想想自动驾驶汽车(当今最先进、最令人印象深刻的技术之一)。为了利用机器学习来检测停车标志，需要建立某些计算机视觉技术。为了构建这些，需要开发高级编程语言。对于高级编程，你首先需要低级编程，先有操作系统，然后是内核，然后是集成电路，等等。

今天，有一些编程语言、库和工具可以让您构建一些强大的问题解决方案，而无需创建或理解底层数学。在这一点上，我们大多数人都熟悉像 Wordpress 和 [Wix](http://www.wix.com) 这样的工具。任何人都可以创建漂亮的网站，这不再仅仅是 HTML 程序员的领域。

但是，如果你正在考虑学习编码，要知道有太多的工具和捷径，而且不需要理解数学、算法或方程。我的第一个代码项目是一个类似脸书的网站，需要登录和密码，可以显示无限量的照片，并允许用户评论帖子。我不需要知道或理解复杂的数学就能构建它，因此(我假设)我不需要知道或理解计算机科学。我所需要的只是一个需要解决的明确问题，一些 PHP、Javascript 和 MySQL 的类，以及谷歌搜索东西的能力。

从那以后，我建立了许多小项目，我曾经不得不实现的最困难的数学是一些简单的代数。你在初级到中级水平上写的大多数函数将只是遍历数组，这意味着你所需要做的一切都是有价值的。考虑这个 PHP 片段，它将扫描一个文件夹中的图像，将每个图像发布到一个[高级对象识别服务(机器学习)](https://goo.gl/SxWKdw)，并将结果推送到一个网站:

```
$trimmedfiles = array();
$result = array();
$files = scandir(‘check’);foreach($files as $file)
{
 switch(ltrim(strstr($file, ‘.’), ‘.’))
 {
 case “jpg”: case”jpeg”: case”png” : case”gif”: 
 $pathtofile = “check/”.$file; 

 $tagbox = json_decode(checkTagbox($pathtofile),true);$results[] = [“detail”=>array(“tagbox”=>$tagbox,”thumbnail”=>$pathtofile)];
 }
}echo json_encode($results);function checkTagbox($a) {if (function_exists(‘curl_file_create’)) { // php 5.5+
 $cFile = curl_file_create($a);
 } else { // 
 $cFile = ‘@’ . realpath($a);
 }$body = [‘file’=>$cFile];
 $ch = curl_init();

 curl_setopt($ch, CURLOPT_URL, “[http://localhost:8080/tagbox/check](http://localhost:8080/tagbox/check)”);
 curl_setopt($ch, CURLOPT_POST, true);
 curl_setopt($ch, CURLOPT_POSTFIELDS, $body);
 curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);

 $result = curl_exec($ch);
 curl_close($ch);
 return $result;
```

考虑一下这带来的强大的高级特性；检测图像中的对象，并将对象的名称返回给可以显示、搜索等的其他系统。你可以基于这样的小项目建立一个完整的人工智能电子商务网站。而且我也绝不是一个高级(甚至优秀)的开发者。想象一下你可以用像[机器盒子](https://goo.gl/SxWKdw)这样的工具和像 [jQuery](https://jquery.com/) 这样的库来构建什么，这些库被设计用来抽象复杂的或者禁止性的代码和数学。

如果你注意到上面的代码块，没有数学，甚至没有一个整数。这是因为像 PHP 这样的语言使得遍历一个目录 X 次变得非常容易，其中 X = *目录中文件的总数*。

如果你想从事编码和开发，不要让对数学的恐惧阻止你！你会惊讶地发现，为了构建强大的东西，你实际上只需要知道很少的数学知识。仍然有大量真正的计算机科学存在，特别是当你开始构建机器学习模型或理解区块链时，但如果你密切关注，你会开始注意到这些技术的普遍民主化是从抽象数学开始的。

现在是投身编码的最佳时机，所以现在就开始吧。