# 五个小时四个学期的计算机科学…唷！

> 原文：<https://medium.com/hackernoon/four-semesters-of-computer-science-in-five-hours-phew-53dd8779b79f>

我的博客记录了我作为一名技术人员进入硅谷的经历。我是一个有色人种的怪人，性别不合群，移民，曾被收养的年轻人。我来自一个非传统的编码背景。我在大学里学了几门 CS 课程，最后在成为老师之前主修了人文学科。向青少年教授 web 开发让我对编码产生了兴趣。在一所编码学校毕业后，我开始在旧金山的初创企业工作。我的博客一半是关于技术主题，另一半是关于平等和技术访问(我的旅程)。

本周选择了布莱恩·霍尔特的另一个教程。我喜欢向布莱恩学习的一点是，他理解自学，因为他承认从大学辍学，自学成才。他用简单的英语解释一切，谦逊地承认他使用的工具或他如何着手解决问题是一种“观点”,并强调有其他方法可以达到同样的结果。他说，“第一次学习递归很难。但一旦你得到了，你就开始意识到这很难但很简单。”他还补充了一个警告，即这门课程并不能代替修完 4 个学期[的](https://hackernoon.com/tagged/semesters)[比较科学](https://hackernoon.com/tagged/comp-sci)课程。我知道从大学辍学并“自学成才”是科技行业的一个笑话，如果《硅谷》这部剧可以说明这一点的话:

大外卖:

*   大 O
*   递归
*   排序算法
*   数据结构

**大 O 批注**

简单来说，大 O 就是用 n 个输入执行 x 次运算所需时间的度量。例如，如果你有一个等式，3x + x + 1。大 O 考虑了运算 x，它给出了等式中最大的数。这个方程的大 O 将是 O(n)，因为如果我们将 O(n ) + O(1) + O(1) = O(2n)的运算相加，我们会去掉系数，因为随着数字变大，它变得无关紧要。让我们尝试使用另一个函数来计算大 O。

```
var crossAdd = function(input) {
var answer = [];
for (var i = 0; i < input.length; i++){
  var goingUp = input[i];
  var goingDown = input[input.length-1-i];
  answer.push(goingUp + goingDown);
}
return answer;
}
```

让我们计算一下大 O，crossAdd 是一个赋值所以那是 O(1)，答案也是。所以我们有，O(1) + O(1)。我们的 for 循环 O(1) + O(n) + O(n)。另一个赋值，O(1) + O(n) + O(n) + O(1)。总的来说，我们有 O(5) + O(4n)。因为 O(1)是一个常数，我们可以去掉 O(5)。随着数字变大，n 前面的系数将变得越来越不重要，所以我们剩下 O(n)。试试另一个:

```
function makeTuples(input) {
var answer = [];
for (var i = 0; i < input.length; i++) {
  for (var j = 0; j < input.length; j++) {
    answer.push([input[i], input[j]]);
  }
}
return answer;
}
```

跟踪对程序影响最大的操作，2 for 循环意味着我们必须遍历输入 O(n) * O(n)次，等于 O(n)。

**递归**

*   一个函数在它的代码块内部调用它自己
*   必须有一个基本用例(将函数从递归调用中分离出来)
*   如果您的基本情况失败，您会得到堆栈溢出—调用函数，直到堆栈中没有剩余的内存(高成本，使用 for 或 while 循环—迭代而不是递归)

我们什么时候使用递归？多次调用一个函数会增加开销，因为必须创建函数的堆栈框架。这包括最低级别的内存操作和 CPU 寄存器更新。迭代/循环不需要这些额外的工作。如果我们有一个程序，要求用户多次调用递归函数，这是没有效率的，迭代会工作得更好。

```
function countdown(current) {
  if(current <= 0) return;
  wr(current);
  countdown(current-1)
}
```

**著名的递归函数——斐波那契**

```
function fibonacci(num) {
  if(num <= 2) {
    return 1
  } else {
    return fibonacci(num — 1) + fibonacci(num — 2)
  }
}
```

**同样著名的递归函数——阶乘**

```
function factorial(num) {
  if(num < 2) {
    return 1
  } else {
     return num * factorial(num — 1)
  }
}
```

**排序算法**

```
//create a function that will swap numbers//write a function that will take a list, index1, and index2
function swap(list, index1, index2) {
  let swapped = list[index1]
  list[index1] = list[index2]
  list[index2] = swapped
}
```

1.  冒泡排序——效率非常低，因为有一个外部循环和一个内部循环，加上其他常数运算，算法的大 O 等于 O(n)。

```
//loop across the list to keep track if you are at 
the beginning of the end
//loop across the list to swap letters
//return the new listfunction bubbleSort(items){
  let len = items.length
  for (let i=0; i < len; i++){
    for (j=0, stop=len-i; j < stop; j++){
      if (items[j] > items[j+1]){
       swap(items, j, j+1);
    }
  }
}
 return items;
}
```

2.[插入排序](http://codingmiles.com/sorting-algorithms-insertion-sort-using-javascript/) —更复杂，偶尔有用。参见伪代码进行解释

```
//ex: [5, 3, 1] => inserts 3 into the 5 position, now 2 are sorted to [3, 5, 1] => makes a copy of 1 compares it to 5, moves 5 into its spot => lookfunction insertionSort(array) {
  for(var i = 0; i < array.length; i++) {
    var temp = array[i];
    var j = i — 1;
    while (j >= 0 && array[j] > temp) {
      array[j + 1] = array[j];
      j — ;
    }
   array[j + 1] = temp;
  }
return array;
}
```

3.[合并排序](https://www.tutorialspoint.com/data_structures_algorithms/merge_sort_algorithm.htm) —分而治之，将数组分成左排序和右排序，然后我们将它们放回一起

```
function stitch(left, right) {
  const results = []
  while(left.length && right.length) {
    if(left[0] <= right[0]) {
      results.push(left.shift())
    } else {
     results.push(right.shift())
    }
  }
  while(left.length){
    results.push(left.shift())
  }
  while(right.length) {
    results.push(right.shift())
  }
}
```

合并排序

```
function mergeSort(nums) {
  if(nums.length < 2) {
    return nums
  }
  const length = nums.length
  const middle = Math.floor(length / 2)
  const left = nums.slice(0, middle)
  const right = nums.slice(middle, length)
  const sortedLEft = mergeSort(left)
  const sortedRight = mergeSort(right)
  return stitch(mergeSort(left), mergeSort(right))
}
```

4.[快速排序](http://blog.benoitvallon.com/sorting-algorithms-in-javascript/the-quicksort-algorithm/) —另一种分治算法，最强大的排序算法，递归。基本要点是，你把列表中的最后一个元素作为枢纽。小于中枢的所有东西都放在“左”列表中，大于中枢的所有东西都放在“右”列表中。然后递归调用左右列表的快速排序，连接排序后的左列表、透视，然后是右列表(按此顺序。)基本情况是当长度为 1 或 0 时，返回列表。

```
function quicksortBasic(array) {
  if(array.length < 2) {
    return array;
  }
  var pivot = array[0];
  var lesser = [];
  var greater = [];
  for(var i = 1; i < array.length; i++) {
    if(array[i] < pivot) {
      lesser.push(array[i]);
    } else {
     greater.push(array[i]);
    }
  }
return quicksortBasic(lesser).concat(pivot,quicksortBasic(greater));
}
```

**数据结构**

数组列表与链表

数组列表—需要与分配的内存块进行交互。在 Java 中，你必须声明数组的大小。典型的方法有:push、pop、find 和 delete(在本教程中 _collapseTo 是 delete 的辅助函数)。

链接列表—有头、尾、下一个和上一个(如果是双重链接)。链表有助于避免哈希表中的重复键造成的数据冲突。它们在删除和插入中也很有用。

二叉查找树(BST)-快速搜索，BST 可以有 0，1，2 个孩子。左小于父节点。权大于父。查找次数为 O(log n)。BST 不用于生产。自平衡采油树用于生产。

```
class Node {
  constructor(data, left=null, right=null) {
    this.data = data
    this.left = left
    this.right = right
  }
}class Tree {
  constructor() {
    this.root = null;
  } add(value) {
    if (this.root === null) {
      this.root = new Node(value);
    } else {
       let current = this.root;
    while(true) {
      if (current.value > value) {
      // go left
        if (current.left) {
          current = current.left;
        } else {
           current.left = new Node(value);
           break;
        }
      } else {
        // go right
        if (current.right) {
          current = current.right;
        } else {
            current.right = new Node(value);
            break;
        }
      }
    }
  }
 return this;
} toJSON() {
    return JSON.stringify(this.root.serialize(), null, 4);
  } toObject() {
    return this.root.serialize();
  }
}
```

在这个教程中肯定有很多信息，我肯定是在试图吸收所有的信息。我一定会再看一遍。