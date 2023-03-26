# 面向 SQL 人员的熊猫备忘单(第 1 部分)

> 原文：<https://medium.com/hackernoon/pandas-cheatsheet-for-sql-people-part-1-2976894acd0>

如今，andas 库是数据科学家事实上的标准工具。它被全球许多数据科学家广泛使用。在熟悉它之后，无论我在做什么项目，我总是用它来处理表结构数据。它工作快速可靠，支持 CSV，Excel，JSON 等。

然而，作为一个在 [SQL](https://hackernoon.com/tagged/sql) 方面经验丰富的人，一开始我在操作表格(也就是数据框架)时遇到了一些困难和困惑。最终，我学到了更多的 API 和正确做事的方法。相信很多踏上[熊猫](https://hackernoon.com/tagged/pandas)第一步的人可能都有同样的经历。不久前，我使用 SQL 查询和它们在 Pandas 中的类比编写了一份备忘单。我很乐意与你们分享。

在这篇文章中，我分享了使用“选择”的查询。稍后我会贴出其他问题以及它们与熊猫的类比。我很高兴收到您关于备忘单的反馈和愿望清单。

开始了。

我们有三张简单的桌子:

*   用户
*   课程
*   出席

其余的查询将是关于对它们的数据操作。

让我们从基本的选择命令开始。

```
SELECT * FROM users
```

```
SELECT * FROM users LIMIT 0,10
```

```
SELECT * FROM users WHERE email IS NULL
```

```
SELECT first_name, last_name FROM users
```

```
SELECT DISTINCT birth_year FROM users
```

基本的数学和算术运算。

```
SELECT AVG(points) FROM users;
SELECT SUM(points) FROM users;
```

条件运算之类。

```
SELECT * FROM users WHERE birth_year BETWEEN 1998 AND 2018
```

```
SELECT * FROM users WHERE first_name LIKE 'Ch%'
```

```
SELECT * FROM users WHERE first_name LIKE '%es'
```

```
SELECT * FROM users WHERE first_name LIKE '%on%'
```

```
SELECT first_name, last_name FROM users WHERE first_name LIKE '%on%'
```

我用相关 SQL 查询的 JOIN 和 ORDER 完成了这篇文章。为了使代码更加简单易读，我将把结果赋给新的变量，并在下一个变量中重用它们。

```
SELECT * FROM attendance atn
LEFT JOIN users usr ON atn.user_id = usr.id
```

现在让我们把上面的结果和课程名称联系起来。其结果将与以下 SQL 命令的结果相同。

```
SELECT * FROM attendance atn
LEFT JOIN users usr ON atn.user_id = usr.id
LEFT JOIN courses co ON co.id = atn.course_id
```

上面的查询将返回包含所有列的完整表/数据帧。要选择必要的列，我们可以使用与前面相同的方法。

```
SELECT * FROM users ORDER BY first_name, last_name
```

```
SELECT * FROM users ORDER BY first_name, last_name DESC
```

```
SELECT first_name, last_name, birth_year,
points, course_name, instructor FROM attendance atn
LEFT JOIN users usr ON atn.user_id = usr.id
LEFT JOIN courses co ON co.id = atn.course_id
ORDER BY first_name, last_name
```