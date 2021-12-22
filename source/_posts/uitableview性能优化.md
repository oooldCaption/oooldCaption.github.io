---
title: UITableView性能优化
tags:
  - iOS
id: '126'
categories:
  - - iOS 开发
date: 2018-09-18 18:03:26
---

UItableview 是 iOS 开发中最常用的 ui 控件之一,也是最常出现的控件之一; 常常负责一些列表数据的渲染,因此对 UITablevie 进行性能优化是很有必要的;

### 1 cell 重用

#### 1.1 数据源方法优化

```text
(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;
```

每次刷新都去创建新的 cell, 性能; 我们会传建一个静态的变量来作为标识符去创建 cell, 每个 cell 都会注册一个标识符,放入缓存中,如果需要调用,就从缓存池中找到对应的 id, 只有一处屏幕的 cell 才会被放入缓存中

```objectice-c
static NSString *resuseID = @"CellID";
UITableviewCell *cell= [tableview dequeueReusableCellWithIdentifier:resuseID];
```

#### 1.2 缓存池的实现

当 cell 要初始化的时候, tableview 会在堆中开辟内存为 cell 缓存使用. cell 的重用通过 identifier 标识不同类型的 cell,

#### 1.3 缓存池取出可重用 cell 两种方法的区别

```text

 //  这个方法会查询可重用Cell，如果注册了原型Cell，能够查询到，否则，返回nil；而且需要判断if（cell == nil），才会创建Cell，不推荐
-(nullable __kindof UITableViewCell *)dequeueReusableCellWithIdentifier:(NSString *)identifier;
//   使用这个方法之前，必须通过xib（storyboard）或是Class（纯代码）注册可重用Cell，而且这个方法一定会返回一个Cell
-(__kindof UITableViewCell *)dequeueReusableCellWithIdentifier:(NSString *)identifier forIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(6_0);
//注册 cell
- (void)registerNib:(nullable UINib *)nib forCellReuseIdentifier:(NSString *)identifier NS_AVAILABLE_IOS(5_0);
- (void)registerClass:(nullable Class)cellClass forCellReuseIdentifier:(NSString *)identifier NS_AVAILABLE_IOS(6_0);
```

### 2 定义尽量少类型的 cell 以及善用 hidden 隐藏或者显示 subviews

#### 2.1定义一种类型的 cell

​ 分析 cell 的结构,尽量将相同内容抽取到一种样式 cell 中,前面已经说到了 cell 的重用机制,同种类型保证重用

#### 2.2 善用 hidden 去显示或者隐藏 subviews

​ 与2.1对应

### 3 提前计算并缓存 cell 的高度

在iOS中，不设UITableViewCell的预估行高的情况下，会优先调用”tableView:heightForRowAtIndexPath:”方法，获取每个Cell的即将显示的高度，从而确定UITableView的布局，实际就是要获取contentSize（UITableView继承自UIScrollView,只有获取滚动区域，才能实现滚动）,然后才调用”tableView:cellForRowAtIndexPath”,获取每个Cell，进行赋值. 如果项目中 cell 比较多,是非常影响性能的; 可以使用**UITableView-FDTemplateLayoutCell** 去计算 cell 的高度,以及缓存

### 4 异步绘制

减少cell 在主线程的时间消耗,异步渲染视图, 如果图片较多,可以使用预渲染技术

### 5 按需加载

使用 scrollview 的代理方法进行判断, 当 tableview 静止或者滑动减速的时候进行异步加载,之家在目标范围内的 cell; 减少了无关的性能损耗;

### 6 减少动态添加 subviews

### 7 减少 cell 子视图的绘制