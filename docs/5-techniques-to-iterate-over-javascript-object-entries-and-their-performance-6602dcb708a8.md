# 迭代 JavaScript 对象条目及其性能的 5 种技术

> 原文：<https://medium.com/hackernoon/5-techniques-to-iterate-over-javascript-object-entries-and-their-performance-6602dcb708a8>

![](img/112a9a89afdb80832bc0be7fe68a7366.png)

这篇文章包括 5 种不同的 JavaScript 对象条目迭代方法，以及这些技术的性能比较。

## 技术 1:对象.条目

Object.entries()返回键、值对的列表。此列表只包括可枚举的属性，不包括属性…