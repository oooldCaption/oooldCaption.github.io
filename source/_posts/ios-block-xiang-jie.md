---
title: iOS  block详解
tags:
  - iOS
id: '224'
categories:
  - - iOS 开发
date: 2019-04-28 11:35:44
---

### 什么是Block

block又叫块对象， 是MacOS X 10.6 以及 iOS4.0 平台下都可以使用的功能，`block`不是oc的实现， 而是c语言的实现； 与其他编程语言中的`闭包`

### 如何定义一个块对象

```
^(返回值类型)(参数列) {表达式主体}
```

#### 无参数无返回值

```
void(^testBlock)(void) = ^(void){
    NSLog(@"无参数无返回值");
}
testBlock();
```

#### 无参数有返回值

```
int(^testRetunValueBlock)(void) = ^(int){
    return  100;
}
int value = testRetunValueBlock();
```

#### 有参数无返回值

```
  void(^ testParamsBlcok) (NSString *) = ^(NSString *string){
            NSLog(@"%@",string);
  };
  testParamsBlcok(@"hhh");
```

#### 有参数有返回值

```
NSString * (^tempBlock)(NSString *) = ^(NSString *string){
    return [NSString stringWithFormat:@"你最帅,%@",string];
};
NSLog(@"%@",tempBlock(@"两好三坏"));
```

#### 使用typeof定义

```
typedef int (^MyBlock)(int , int);
@property (nonatomic,copy) MyBlock myBlockOne;
self.testBlock = ^int (int ,int){
}
```

### Block中变量行为

```
#include <stdio.h>
void myFunc(int m, void (^b)(void) {
    printf("%d: ",m);
    b();
}
int glob = 1000;
int main (void) {
    void (^block)(void);
    static int s = 20;
    int a = 20;
    block = ^{ print("%d, %d, %d \n",glob, s, a);};
    myFunc(1,block);
    s = 0;
    a = 0;
    glob = 5000;
    myFunc(2,block);
    testblock = ^{ print("%d, %d, %d \n",glob, s, a);};
    myFunc(3,testblock);
    return 0 ;
}
//输入结果:
1: 1000, 20,20
2: 5000, 0, 20
3: 5000, 0, 0
```

第一个`block`定义后, `block`中包含外部变量 `glob` 和 `main`函数中局部变量,a,s;然后将`block` 作为参数传递给`myFunc`. 然后改变glob,a,s的值后再次调用`myFunc`. 在`testBlock`定以后, 调用`myFunc`; 对比上面三个输出, block 只在代码块中保存自动变量的值,block 复制了block内部会使用到的变量;即使变量的值发生了变化, block在使用的时候也不会知道这个值变化; 那如果我们想知道这个值得变化或者想要修改这个变量该怎么办呢, 我们可以使用 `__block`对想要使用的变量进行修饰, 如果我们使用了`__block`修饰,name就是引用了这个变量的地址; 而且block只能访问,不能修改全局变量; \[TOC\] block使用变量相当于拷贝了一份变量在堆内存中，要注意对变量的影响