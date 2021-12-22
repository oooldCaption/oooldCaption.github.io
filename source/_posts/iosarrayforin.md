---
title: iOS中for 和 for-in 循环数组的小区别
tags:
  - iOS
id: '199'
categories:
  - - iOS 开发
date: 2019-03-26 17:06:28
---

在 OC 开发中, 数组是我们经常会用到的数据结构;而对数据进行遍历是很常见的操作, OC也为我们提供一下几种常用的操作:

*   for 循环,普通遍历
*   for-in,快速 遍历
*   Block
*   枚举器NSEnumerator

之前大部分情况下都是用for循环进行普通遍历, 但前几天我使用 for-in 去遍历一个数据的时候却报了异常;

代码如下:

```
NSMutableArray *testArray = [NSMutableArray arrayWithObjects:@"耐克",@"阿迪达斯",@"李宁",@"匡威",nil];
for(NSString *temp in testArray)
{
if{[temp isEqualToString:@"阿迪达斯"])
{
NSLog(@"我不喜欢");
[testArray removeObjective:temp];
}
}
```

报错信息如下:

```
Collection <__NSArrayM: 0x100601400> was mutated while being enumerated.
```

但是如果我们去使用 for 进行普通遍历, 然后对数组进行修改或者删除就不会有这个错误;

> 快速枚举的过程中不允许改变容器中的对象, 即: 不允许删除或者增加枚举容器中的对象, 因为对于一个既定的枚举器来说, 其内部的对象都已经对其进行了枚举排序, 如果你私自添加或者删除对象, 那么其既定的枚举序列就发生了改变, 而这种改变时系统不能识别的, 所以 OC中不允许这么做. 但有时候我们也必须在快速枚举的过程中删除一些枚举容器中的对象, 这时候, 我们就需要在删除完一个对象的时候, 对其进行break.