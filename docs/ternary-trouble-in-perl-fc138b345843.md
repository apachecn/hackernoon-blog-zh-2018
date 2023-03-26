# Perl 中的三元问题

> 原文：<https://medium.com/hackernoon/ternary-trouble-in-perl-fc138b345843>

![](img/baa0054b82bc256579c2be970ad5f886.png)

有一天，我在 Perl 中遇到了一个问题，这个问题一直困扰着我，原因很简单，我还没有找到它的答案。我的问题是。举以下例子:

## **多变量赋值:**

Perl 允许使用如下语法同时声明多个变量:

```
my ($var1, $var2) = (100, 200);
print "variable 1 is: $var1, variable 2 is: $var2 \n\n";
```

这将返回以下结果:

```
variable 1 is: 100, variable 2 is: 200
```

## 字符串拆分:

够简单了吧？Perl 和大多数语言一样，也允许拆分字符串。拆分字符串的语法如下所示:

```
my $str1 = 'a sentence has many words';
my [@arr1](http://twitter.com/arr1) = split / /, $str1;foreach my $x ([@arr1](http://twitter.com/arr1)) {
  print "word: $x \n";
}
```

该示例的打印结果将为我们提供以下内容:

```
word: a 
word: sentence 
word: has 
word: many 
word: words
```

## 三元运算符:

最后，Perl 还允许三元运算符。这是一种很好的合并代码的方式，并且将 if else 块都放在一行中，尽管对于哪种风格更好、可读性更强还有一些争论。在任何情况下，请使用以下字符串:

```
my $str2 = 'This string has two sentences -  A dash separates them';
```

现在，如果我们想根据一个条件语句来拆分它，我们可以这样做:

```
my [@arr7](http://twitter.com/arr7);
if (index($str2, '-') != -1) {
  [@arr7](http://twitter.com/arr7) = split /-/,$str2;
} else {
  [@arr7](http://twitter.com/arr7) = split /:/,$str2;
}
print "========== array7[0]", $arr7[0], "\n";
print "========== array7[1]", $arr7[1], "\n";
```

这段代码会给出如下输出:

```
========== array7[0]This string has two sentences 
========== array7[1]  A dash separates them
```

然而，通过使用三元运算符，我们可以用更少的代码获得相同的输出。如果按如下方式编写，上面的 if/else 块将给出相同的输出:

```
my ([@arr2](http://twitter.com/arr2)) = (index($str2, '-') != -1) ? split /-/,$str2 : split /:/,$str2;
print "arr2[0] is: ", $arr2[0], "\n";
print "arr2[1] is: ", $arr2[1], "\n";
```

我们的输出是:

```
arr2[0] is: This string has two sentences 
arr2[1] is:   A dash separates them
```

现在让我们使用前面的 if/else 块，并添加两个数组。我们将在 if else 块中设置每个数组的值。我们的两个字符串如下:

```
my $str2 = 'This string has two sentences -  A dash separates them';
my $str4 = 'This string has different punctuation; a semicolon';
```

我们的条件语句是:

```
my ([@arr5](http://twitter.com/arr5), [@arr6](http://twitter.com/arr6));
if (index($str2, '?') != -1) {
  [@arr5](http://twitter.com/arr5) = split /j/,$str2;
  [@arr6](http://twitter.com/arr6) = split /k/,$str4;
} else {
  [@arr5](http://twitter.com/arr5) = split /-/,$str2;
  [@arr6](http://twitter.com/arr6) = split /;/,$str4;
}print "array 5[0] is: ", $arr5[0], "\n";
print "array 5[1] is: ", $arr5[1], "\n";print "array 6[0] is: ", $arr6[0], "\n";
print "array 6[1] is: ", $arr6[1], "\n";
```

我们的输出如下:

```
array 5[0] is: This string has two sentences 
array 5[1] is:   A dash separates them
array 6[0] is: This string has different punctuation
array 6[1] is:  a semicolon
```

# **这就是我卡住的地方:**

现在，考虑到所有这些，您应该能够将上述所有内容组合成一行三元运算符代码。如前所述，我们可以在一个三元块中声明多个变量。所以看起来我们应该能够完全做到上面的代码，但是用一个三元组代替 if else 块。大概是这样的:

```
my ([@arr3](http://twitter.com/arr3), [@arr4](http://twitter.com/arr4)) = (index($str2, '-') != -1) ? split /-/,$str2, split /;/, $str4 : split /:/,$str2, split /;/, $str4;
```

这基本上是做完全相同的事情，在三元条件语句的每个部分设置两个数组的值。但是，如果我们打印结果:

```
print "array 3[0] is: ", $arr3[0], "\n";
print "array 3[1] is: ", $arr3[1], "\n";print "array 4[0] is: ", $arr4[0], "\n";
print "array 4[1] is: ", $arr4[1], "\n";
```

我们得到以下输出:

```
array 3[0] is: This string has two sentences 
array 3[1] is:   A dash separates them
array 4[0] is: 
array 4[1] is:
```

即使将两个评估块都放在括号中，仍然会产生相同的结果。我的结论是，每个 split 函数中额外的逗号扔掉了三元条件块，没有抓住第二组。然而，这只是一个猜测，因为我还没有在其他地方找到答案。

如果有人有任何见解，我将不胜感激。并不是说这在任何真实世界的情况下都是必要的。但是让我恼火的是，它看起来应该是有效的，但是却没有，我也不知道为什么！