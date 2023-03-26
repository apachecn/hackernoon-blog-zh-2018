# 反转 O(log n)中的 n 位数

> 原文：<https://medium.com/hackernoon/reversing-an-n-bit-number-in-o-log-n-time-9bf69363d452>

上周，在写我的[上一篇文章](/p/537cc8bb9f52)时，我遇到了一个有趣的整数反转算法。比如它取`10100011`，转换成`11000101`。这个算法的酷之处在于它只需要 O(log n)次迭代。这是代码:

Reverse an n-bit number in O(log n). From [http://graphics.stanford.edu/~seander/bithacks.html#ReverseParallel](http://graphics.stanford.edu/~seander/bithacks.html#ReverseParallel)

是的。9 行，包括签名和右括号。**我们来看看效果如何。**

下一部分逐行研究代码。如果你对技术细节感到厌烦，可以直接跳到最后的 [**动画**](/@ehudt/reversing-an-n-bit-number-in-o-log-n-9bf69363d452#2326) 。

**更新:**O(logn)运行时间复杂度只适用于 n ≤机器字长，现在一般是 64 位。感谢所有让我注意到这一点的人。

# 技术:递归块交换

该算法通过递归交换相邻的比特块来工作，在每次迭代中减小块的大小。它首先交换 2 个 n/2 位的块，然后是 4 个 n/4 位的块，8 个 n/8 位的块，依此类推，最后是 n 个 1 位的块。在每次迭代中，所有成对的相邻块被并行交换**。结果是颠倒的数字。为了并行交换块，该算法使用 [**逐位** **操作**](https://en.wikipedia.org/wiki/Bitwise_operation) 和**位屏蔽**。**

****注**:原码使用 32 位输入。我们将使用 **8 位整数**来使事情更容易理解。**

## **交换相邻的比特块**

**并行交换块是通过特制的位掩码完成的。**

**对于**块大小** `s`，位屏蔽`mask`由交替的`s`0 和`s`1 块组成。比如对于`s = 4`、`mask == 00001111`。对于`s = 2`，`mask == 00110011`。**

**该行使用掩码并行交换所有块对**:****

```
**num = ((num >> s) & mask) | ((num << s) & ~mask);**
```

*   ****`(num >> s) & mask)`向右移动*偶数块* `s`位置，并使用掩膜清除*奇数块*。****
*   ****`(num << s) & ~mask`向左移动*奇数块* `s`位置，并使用掩码的逐位非清除*偶数块*。****
*   ****按位“或”用于将这些结果加到交换数中。****

****非常简单，不是吗？****

****![](img/ad2af6285467eba645b55b25f3a5ef10.png)****

****A flower XORed against itself. Original By Sam Oth (User:World Trekker) — Own work, CC BY-SA 2.5, [https://commons.wikimedia.org/w/index.php?curid=863943](https://commons.wikimedia.org/w/index.php?curid=863943)****

## ****创建遮罩****

****另一个很酷的技巧在于`mask`是如何创造的。`mask`由大小为`s`的 0 和 1 的交替块组成。掩码以全 1 数字开始，并在循环的第一行更新:****

```
**while ((s >>= 1) > 0) {
  mask ^= (mask << s);
  ...**
```

****这发生在`s`减半之后，因此`s`是`mask`块大小的一半。在第一次迭代中，`mask == 11111111`和`s == 4`。掩码通过将其自身与左移`s`位的其自身的另一个副本进行异或运算来更新:****

```
**mask = 
    11111111 XOR  // mask
    11110000      // mask << s
  = 00001111**
```

****当且仅当两位彼此不相等时，两位的异或为 1。在掩码更新的每次迭代中，所有块都向左移动一半大小。当一个块与前一个掩码进行异或运算时，该块的一半与 0 重叠，另一半与 1 重叠。这在新的遮罩中创建了 2 个块，每个块的大小是前一个块的一半。下面是一个例子:****

```
**0000000011111111  // original mask, 8-bit blocks
0000111111110000  // mask shifted left by block-size/2
0000111100001111  // XORed: new mask, 4-bit blocks**
```

# ****把这一切联系在一起****

****下面是一个简短的动画，展示了该算法的实际应用:****

****令人惊讶的是，在 9 行代码中可以找到多少深度。如果你想让我研究一段有趣的代码，请在下面**留下回复**！****