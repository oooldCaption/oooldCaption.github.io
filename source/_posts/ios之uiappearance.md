---
title: iOS之UIAppearance
tags: []
id: '87'
categories:
  - - iOS 开发
date: 2018-04-17 15:18:13
---

UIAppearance是苹果在iOS5之后为开发者提供的一个能够快速修改系统控件外观的一个协议;

*   UIAppearance的作用:

UIAppearance 实际上是一个协议,可以用它快速的获取一个类的外观(UINavigation UItabBaritem), 我们可以利用需要修改的类获取外观代理,然后自定义一个实例对象的外观;

*   UIAppearance 的使用:
<!-- more -->
我们可以给一个类所有的实例对象设置外观 eg:

```null
+(instancetype)appearance;
// 修改navigation实例对象的颜色和标题外观
UINavigationBar.appearance().titleTextAttributes =
[NSFontAttributeName: [UIFont systemFontofSIze(15.0f)],NSForegroundColorAttributeName: [UIColor whiteColor]];
```

我们也可以给制定一类容器,在这个容器中自定义一个类所有的实例外观 eg:

```null
+ (instancetype)appearanceWhenContainedIn:(nullable Class <UIAppearanceContainer>)ContainerClass, ... NS_REQUIRES_NIL_TERMINATION NS_DEPRECATED_IOS(5_0, 9_0, "Use +appearanceWhenContainedInInstancesOfClasses: instead") __TVOS_PROHIBITED;
//修改导航栏所有外观的外观, 可以放置多个容器类
[[UIBarButtonItem appearanceWhenContainedIn:self.nil] set
BackgroundImage: myNavBarButtonBackgroundImage forState:state barMetrics:metrics];
[[UIBArBUttonItem appearanceWhenContainedIn:self,[UIPopoverControll class],nil] setBackgroundColor: myNavBarButtonBackgroundImage forState:state barMetrics:metrics];
```