# 使用 PHP 实现 Unity3D 游戏的后端

> 原文：<https://medium.com/hackernoon/implementing-backend-for-unity3d-games-using-php-6f043b5b7db7>

![](img/1a4bcd1544878168af878a4c4cdd3d0a.png)

Photo by [Sean Do](https://unsplash.com/@everywheresean?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 介绍

**Unity3D** 已经成为当今流行的游戏引擎之一。只需对代码做一些修改，就可以开发跨平台的游戏。如果你非常习惯使用 *JavaScript* 或 *C#，*那么 Unity3D 应该是你的选择。

# 游戏化

业内许多游戏背后的成功肯定是游戏化的最佳实践。游戏化包含了让游戏更具竞争性的一切，并促使更多的肾上腺素冲击排行榜的榜首位置。为一个游戏制作一个排行榜并不容易，因为有很多组件，比如数据库安全，用户认证等等…

# 履行

这里我们将使用 *PHP* 和 *MySQL* 为 unity3D 上的一个简单游戏实现一个排行榜。计划是这样的:

*   编写 PHP 脚本对我们的数据库进行 SQL 查询
*   编写 *C#* 脚本，在我们的应用程序和后端服务器之间发送和访问数据。这里我们的应用程序将是 Unity3D 游戏，所以我们将使用他们自己的文档来实现它。
*   最终将我们的数据库转储到云上，尽管我们在开发期间在本地主机上测试它😃。

# 我们将实施什么

我们将尽可能简化这一过程，以便我们能够获得更好的学习体验。我们有一个游戏，我们必须在****数据库中存储**用户名**和**分数**。因此，每当游戏结束时，我们会将这些数据存储在数据库中，然后检索整个数据，这些数据将被排序并显示在排行榜上。没那么简单。让我们现在就实施吧😃。****

## ******编写 PHP 脚本******

****您需要有一些后端脚本的想法😉。****

****我们将编写两个 PHP 文件。****

*   ****`*data.php*` 用于**发送**和**检索**数据****
*   ****`update.php`用于**更新**每当玩家取得局部高分时的分数。****

****`data.php`:****

****我们将声明一些在代码中使用的基本变量。****

```
**$servername = "host_name";
$dbname = "dabase_name";
$username = "username";
$password = "database_password";
$playername = $_POST["playernamePost"];
$score = $_POST["scorePost"];**
```

****`$playername`该变量用于存储游戏应用程序发送的用户名。`$_POST["playernamePost"]`这是一个特定的动作，它表示将获取存储在变量`playernamePost`中的数据，该变量是由某个未知来源发送的。虽然这并不安全，但我们将只研究基本的实现。这同样适用于`$score`变量。****

****接下来，连接数据库****

```
**$db = mysqli_connect($servername, $username,$password, $dbname);**
```

****一旦连接成功，我们可以进一步使用 *SQL* 查询我们的数据。****

```
**$sql = " SELECT * FROM data ORDER BY score";
$push = "INSERT INTO data ( name, score) VALUES ('".$playername."','".$score."')";**
```

****`$sql`该变量以字符串的形式存储 *SQL* 查询(数据检索)。`$push`该变量再次以字符串的形式存储 *SQL* 查询 *(* 数据推送到数据库)。您需要对 SQL 查询语句有所了解，比如`SELECT`、`INSERT`等等……对数据做了什么。尽管它们很容易理解😜。`name`和`score`是我们数据库表的列。`data`是表名。`ORDER BY`这是用来对我们的数据进行排序的，不需要为我们的数据编写单独的排序算法。我建议你避免使用它，因为它效率不高。****

****让这些查询起作用****

```
**$retrieval = mysqli_query($db, $sql);
$pushdata = mysqli_query($db,$push);**
```

****将变量作为参数发送给`mysqli_query`函数将完成我们的工作。****

****您可能会想我们的游戏应用程序将如何获取数据。这很简单，我们将所有数据作为一个字符串打印到我们的 PHP 文件中。稍后，我们将检索数据作为一个单一的字符串，并做一些字符串处理的东西，把它放在我们的排行榜。****

```
**while ($row = mysqli_fetch_assoc($retrieval))
{
echo";".$row['name'].":".$row['score']."";
}**
```

****我们将使用`mysqli_fetch_assoc($retrieval)`将数据提取到单个变量`$row`中，然后使用 while 循环打印完整的数据。我们完成了`data.php`****

****现在我们可以使用`data.php`文件检索和推送数据。现在当你要在玩家打出高分的时候更新他的分数，那么你就要用这个`update.php`脚本了。这与您简单地使用更新查询非常相似。****

****`update.php`****

```
**$servername = "host_name";
$dbname = "database_name";
$username = "username";
$password = "password";$playername = $_POST["playernamePos"];
$score =$_POST["scorePos"];
$db = mysqli_connect($servername, $username,$password, $dbname);
$update = "UPDATE data SET score='$score' WHERE name='$playername'";
$result = mysqli_query($db,$update);**
```

****您可以在用户名的帮助下找到特定的列，然后更新相应的高分。****

****我们已经完成了 PHP 文件的编写。现在让我们编写 *C#* 脚本来连接应用程序和后端。****

## ****在 Unity3D 中编写 C#脚本****

****您需要熟悉 *unity3D* 文档😜。****

****`retrieval.cs`****

```
**public string url = "www.somecloudstorage.com/data.php";
IEnumerator Start () 
{       
WWW retrieve = new WWW(url); 
yield return retrieve;  
string fulldata = retrieve.text;  
users = fulldata.Split(';');  
int n =0;  
list.text = "";  
for (int i = users.Length-1; i>=0; i--)
{ 
n++;
if (users[i] != "->0" && !(users[i].StartsWith("<")))
{   
items = users[i].Split(':');
list.text = list.text+ "\n"+n+".  "+ items[0];   
list2.text = list2.text + "\n" + items[1];  
}}}**
```

****`IEnumarator`在这里非常有用，因为我们希望程序暂停执行，直到我们的全部数据从 *PHP* 文件中加载完毕。我们对那个特定的 *PHP 文件发出一个 *HTTP* 请求。我们通过创建一个`WWW`对象并发送所需的 *URL 来做到这一点。*然后我们会一直等到数据加载完毕。我们可以使用`retrieve.text` *将数据作为单个字符串访问(需要时查看 Unity3D 文档)。* Rest 很简单，我们做一些字符串处理工作，以期望的形式检索它。*****

****我们能够检索数据，这是小菜一碟，没错！！现在，让我们看看如何将数据从游戏应用程序推送到数据库。****

****`push_highscore.cs`****

```
**public string url = "www.somecloudstorage.com/data.php";
public void pushData(string username, int highscore) 
{
WWWForm form = new WWWForm ();
form.AddField("playernamePos",username);  form.AddField("scorePos",highscore);
WWW push_Data = new WWW(url,form);
}**
```

****这几行 *PHP* 代码将完成我们的工作。⭐️，记住我们使用 Unit3D 官方文档来实现这个，所以你必须导入必要的包。****

****几乎什么都做好了！！但我不会谈论你将如何更新分数。享受自己实现它的乐趣，我相信你也能做到😜。****

****将您的数据转储到云空间显然是必要的。你可能会在市场上找到很多云空间提供商，转储你的后端代码( *PHP* 文件)，数据库文件(。sql 文件)并正确配置您的 PHPMyAdmin。就这样，你完了！！🎉****

# ****结论****

****就是这样，这是实现一个*排行榜*和使用 *PHP* 和 *MySQL* 为你的游戏应用编写你自己的后端的基本方法👋。****

****🌟请记住，这根本不是一种为游戏应用程序(产品)实现排行榜的安全方式。这只是为了让你站起来开始。🌟****

****你可以在 [GitHub](https://github.com/kmehant) 、 [Twitter](https://twitter.com/mehant1) 、 [LinkedIn](https://www.linkedin.com/in/mehant-kammakomati-1a0b41170/) ✨✨找到 Mehant Kammakomati****