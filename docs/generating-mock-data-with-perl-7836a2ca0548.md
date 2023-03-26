# 用 Perl 生成模拟数据

> 原文：<https://medium.com/hackernoon/generating-mock-data-with-perl-7836a2ca0548>

![](img/5c10656b8e7fb635db3bb69252b741fa.png)

最近在工作中，为了测试代码，我们的团队创建了模拟数据的测试，或者在某些情况下只是模拟正回报，这允许我们更容易地隔离我们正在工作的特定代码块。我确信这在大多数测试驱动设计(TDD)场景中是一种相当常见的实践。然而，这让我想到了我开始学习 JavaScript 时使用的一个 API，它会生成随机用户。[https://randomuser.me/](https://randomuser.me/)

这是模仿用户数据的绝佳资源。您可能会认为这是随机生成的用户的 Lorem Ipsum。当我思考这个简洁的工具，以及我们的团队如何模仿其他类型的数据时，我开始思考如何着手制作自己的用户数据生成器，类似于上面的 API。

如果你想看这个项目的源代码，这里是我目前所拥有的:

【https://github.com/ethanjarrell/randomUsers 号

下面的例子将展示我是如何考虑这个过程的，以及我是如何做的一些代码片段。首先，我需要知道我想要生成的关于每个用户的信息的类型。以下是我想出的清单:

*   名字
*   年龄
*   性别
*   种族划分
*   生日
*   电子邮件
*   电话号码
*   地址
*   职业
*   业余爱好
*   眼睛的颜色
*   毛色
*   肤色
*   高度
*   重量
*   体型
*   婚姻状况
*   配偶(如果已婚)
*   儿童
*   最高教育水平
*   学校
*   程度
*   学生贷款债务
*   年薪
*   宗教

事后看来，我很可能过分使用了这个列表。但这是一个有趣的编程挑战。我的想法是，你可以用两种方法之一来做这件事。首先，您可以创建数千个概要文件，当运行代码时，只从数千个概要文件中随机返回一个。

第二个选择，我认为会花费更少的时间，是随机产生每一个新的人……以某种程度的随机性产生每一个特征，而不依赖于任何包含完整随机特征的数据库。例如，每次运行代码时，它会从一个列表或通过其他方式生成每个特征，每次都将它们组合在一起创建一个新的用户配置文件。

在这样做的过程中，我意识到有些特征在某种程度上依赖于其他特征。而另一些可以完全独立于其他任何东西。比如不能随便选名字和性别。否则你可能会和一个叫伊莎贝拉的男人，或者一个叫巴奇的女人在一起。这并没有什么不对，但是为了一致性，我想在一定程度上保持在社会规范之内。

其他微妙的从属特征是爱好、体重/体型等。例如，如果我们随机选择这些特征，我们可能最终会得到一个爱好包括铁人三项，但体型超重的人。同样，这没有错，但关键是要生成一致的数据。

另一个例子可能是教育/工作类型和年薪。同样，随机选择，我们可以产生一个拥有电气工程硕士学位的人，年薪为 12，000 美元。

为了以一种有意义的方式解决这个问题，我首先生成所有不需要相互依赖的数据。然后，根据我最初的几个特征产生的结果，我可以从一个更孤立的数据子集产生剩余的特征。

为了开始我的 Perl 文件，我将使用几个本地 Perl 包:

```
#!/usr/bin/perl
use DBI;
use strict;
use warnings;
use lib qw(..);
use JSON qw(  );
use Data::Dumper;
```

在这个例子中，我实际上没有使用 DBI，但是如果您想将每个新生成的用户写入数据库，DBI 可能会派上用场。然而，我将使用 lib 和 JSON。

首先，我上网找到了一个特定条目的随机生成特征列表。这些都包含在上面的 github 链接中。在清理数据、删除空格和添加逗号之后，我遍历列表，将每个条目放入一个数组中。然后，当我想从数组中进行随机选择时，我可以使用 Perl“rand”方法获得一个随机条目。

我从很多列表中提取数据，但是我基本上使用相同的格式从列表中提取数据。下面是一个例子，说明如何为男性和女性的名字创建列表:

```
# declare a variable for the test txt file
my $maleNames = 'maleNames.txt';
# connect to and open the json file
open (FH, "< $maleNames") or die "Can't open $maleNames for read: $!";
my [@maleLines](http://twitter.com/maleLines);
while (<FH>) {
    push ([@maleLines](http://twitter.com/maleLines), $_);
}
close FH or die "Cannot close $maleNames: $!";my [@maleList](http://twitter.com/maleList) = ();
foreach my $x ([@maleLines](http://twitter.com/maleLines)) {
  my $str = $x;
[@maleList](http://twitter.com/maleList) = split(',', $str);
}# declare a variable for the test txt file
my $femaleNames = 'femaleNames.txt';
# connect to and open the json file
open (FH, "< $femaleNames") or die "Can't open $femaleNames for read: $!";
my [@femaleLines](http://twitter.com/femaleLines);
while (<FH>) {
    push ([@femaleLines](http://twitter.com/femaleLines), $_);
}
close FH or die "Cannot close $femaleNames: $!";my [@femaleList](http://twitter.com/femaleList) = ();
foreach my $x ([@femaleLines](http://twitter.com/femaleLines)) {
  my $str = $x;
[@femaleList](http://twitter.com/femaleList) = split(',', $str);
}
```

现在，我可以模拟一次抛硬币，如下所示:

```
my $gender = int(rand(2));
```

这将为性别生成一个 0 或 1 的数字。

然后我可以使用条件句:

```
if ( $gender == 0 ) {
$gender = "female";
} else {
$gender = "male";
}
```

性别是随机决定的，我们可以根据随机选择性别的结果，从男性或女性名单中随机抽取一个新记录。

**生成随机电话号码:**

现在，我可以做一个 API 调用来确定一个州的区号，一旦随机选择了一个州，但此时我并不真正了解这一点。我真正关心的是号码是电话号码格式((xxx) xxx — xxxx)。

为此，我随机生成了 10 个数字，并将其连接成正确的格式:

```
my $n1 = int(rand(8)+1);
my $n2 = int(rand(9));
my $n3 = int(rand(9));
my $n4 = int(rand(9));
my $n5 = int(rand(9));
my $n6 = int(rand(9));
my $n7 = int(rand(9));
my $n8 = int(rand(9));
my $n9 = int(rand(9));
my $n10 = int(rand(9));my $phoneNumber = "($n1$n2$n3) $n4$n5$n6-$n7$n8$n9$n10";
```

**生成随机邮编:**

邮政编码也是如此:

```
my $z1 = int(rand(8)+1);
my $z2 = int(rand(9));
my $z3 = int(rand(9));
my $z4 = int(rand(9));
my $z5 = int(rand(9));my $zipcode = $z1 . $z2 . $z3 . $z4 . $z5;
```

**生成随机邮件:**

我认为可能会有点不同的邮件。例如，如果我已经生成了一个随机的名字，我可以简单地这样做:

```
firstName.lastName @ emailServer . com
```

虽然，这看起来不太可信，所以我想让电子邮件变得多种多样。

```
my [@emails](http://twitter.com/emails) = ("gmail", "hotmail", "protonmail", "yahoo", "aol");my $index = rand [@emails](http://twitter.com/emails);
my $email = $emails[$index];my $index2 = rand [@adjectiveList](http://twitter.com/adjectiveList);
my $adjective = $adjectiveList[$index2];my $index3 = rand [@animalsList](http://twitter.com/animalsList);
my $myAnimal = $animalsList[$index3];
```

每次运行代码时，将从 5 个列表中随机选择一个电子邮件服务。然后一个形容词和一种动物将从几百个列表中随机挑选出来。这将允许我创建电子邮件格式如下:

```
fuzzy . kitten . 01 @ gmail .com
```

或者

```
firstName . the . terrible @ yahoo .com
```

有了这些变量，我创建了 5 种格式类型，并在 1 到 5 之间随机选择了一个数字。根据结果，新电子邮件将获得该格式。这是它的样子:

```
my $emailAddress = "";
my $girlGuy  ="";
if ($gender eq "male"){
  $girlGuy = "guy";
}else {
  $girlGuy = "girl";
}
my $emailFormat = int(rand(5));
if ($emailFormat == 0){
$emailAddress = $lastName[0] . "." . $lastName[1] . $sN3 . $z2 . "@" . $email . ".com";
} elsif ($emailFormat == 1) {
  $emailAddress = $adjective . "_" . $myAnimal . $sN1 . "@" . $email . ".com";
} elsif ($emailFormat == 2) {
  $emailAddress = $adjective . "_" . $lastName[0] . $sN1 . "@" . $email . ".com";
} elsif ($emailFormat == 3) {
  $emailAddress = $newState . "_" . $girlGuy . $sN1 . $sN2 . $sN3 . "@" . $email . ".com";
} elsif ($emailFormat == 4) {
  $emailAddress = $adjective . "_" . $newCity . "_" . $girlGuy . $sN1 . $sN2 . $sN3 . "@" . $email . ".com";
}
$emailAddress =~ s/\s+//g;
```

**生成随机作业和程度:**

对我来说，另一个有趣的事情是创造一份工作和一个学位。我使用了一个随机的职位列表，并随机选择一个，类似于我创建和随机选择一个男性或女性名字的方式。然后，对于我新的随机作业，我使用 Perl 中的 substr 方法来比较该作业。我基本上做的是将职位名称的一个子串与一大串潜在学位项目进行比较。例如，如果职务是:

```
Electrical Engineer
```

然后，我可能会抓住职位的前 5 个字母:

```
elect
```

遍历列表中的所有学位项目，看是否有匹配的。如果有匹配，我会将匹配添加到这个新的随机用户拥有的学位列表中。我有学士、助理和硕士项目的列表，所以我检查了所有项目，并将匹配项放入学位数组:

```
my $substring = substr $job, 0, 5;my [@degrees](http://twitter.com/degrees) = ();
foreach my $x ([@bachelorList](http://twitter.com/bachelorList)) {
  if (index($x, $substring) != -1) {
      push [@degrees](http://twitter.com/degrees), $x;
  }
}foreach my $x ([@associateList](http://twitter.com/associateList)) {
  if (index($x, $substring) != -1) {
      push [@degrees](http://twitter.com/degrees), $x;
  }
}foreach my $x ([@masterList](http://twitter.com/masterList)) {
  if (index($x, $substring) != -1) {
      push [@degrees](http://twitter.com/degrees), $x;
  }
}my $degree = "";foreach my $x ([@degrees](http://twitter.com/degrees)) {
  $degree = $degree . ", " . $x;
}
```

最后，我简单地将所有结果连接成一个字符串。

没有仔细检查我所做的每一个决定，我最终得到了一个结果的散列，我用 Dumper 打印了它。我的散列如下所示，变量作为散列值:

```
my $json = {NAME=>$name, WEIGHT=>$finalWeight, HEIGHT=>$finalHeight, EYE_COLOR=>$eyeColor, BODY_TYPE=>$bodyType, HAIR_COLOR=>$hairColor, SKIN_COLOR=>$skinColor, RELIGION=>$myReligion, SCHOOL=>$school, HIGHEST_LEVEL_OF_EDUCATION=>$highestLevelOfEducation, AGE=>$age, STUDENT_LOAN_DEBT=>'$' . $studentLoanDebt . '.00', ANNUAL_SALARY=>'$' . $annualSalary . '.00', CHILDREN=>$kids, BIRTHDAY=>$birthDate, GENDER=>$gender, MARITAL_STATUS=>$married, SPOUSE=>$spouse, ADDRESS=>$streetNumber . " " . $newStreet . ", " . $newCity . ", " . $newState . " " . $zipcode, PHONE=>$phoneNumber, EMAIL=>$emailAddress, OCCUPATION=>$job, HOBBIES=>$hobby1 . ", " . $hobby2 . ", " . $hobby3, DEGREE=>$degree, ETHNICITY=>$ethnicBackground};print Dumper $json;
```

我的输出通常如下所示:

```
{
          'ADDRESS' => '7841 3rd Street West, Duarte, Minnesota 87604',
          'GENDER' => 'female',
          'SPOUSE' => 'Raleigh Teer',
          'STUDENT_LOAN_DEBT' => '$6336.00',
          'HAIR_COLOR' => 'Dark Brown',
          'HEIGHT' => '6 feet, 10 inches',
          'EMAIL' => '[creepy_Duarte_girl784@aol.com](mailto:creepy_Duarte_girl784@aol.com)',
          'SKIN_COLOR' => 'Light Brown',
          'RELIGION' => 'Stoicism',
          'HOBBIES' => 'Golf, Tutoring Children, Photography',
          'BIRTHDAY' => 'March 7, 1983',
          'PHONE' => '(361) 734-5735',
          'WEIGHT' => '327 lbs',
          'AGE' => 35,
          'MARITAL_STATUS' => 'married',
          'DEGREE' => ', Bachelor of Accountancy, Master of Accountancy, Master of Accounting and Information Systems',
          'ETHNICITY' => 'Chinese Australian',
          'SCHOOL' => 'University of Minnesota-Duluth',
          'NAME' => 'Maggie Davila',
          'HIGHEST_LEVEL_OF_EDUCATION' => 'Masters Degree',
          'BODY_TYPE' => 'Thick',
          'EYE_COLOR' => 'Black',
          'OCCUPATION' => 'Accountant',
          'CHILDREN' => 5,
          'ANNUAL_SALARY' => '$68077.00'
        };
```

这是一个非常有趣的项目，即使它不是非常有用，我仍然在这个过程中学到了很多关于 Perl 的知识。希望你喜欢。