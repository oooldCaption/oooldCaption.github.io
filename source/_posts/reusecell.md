---
title: 模仿 tableViewCell 实现一个简易的重用视图
tags:
  - iOS
  - iOS原理
id: '161'
categories:
  - - iOS 开发
date: 2018-12-10 15:29:15
---

之前面试的时候会有很多人问道 UITableviewCellde 重用机制, 大部分回答都是

> 在缓存池初中通过 dequeueReusableCellWithIdentifier:CellIdentifier 方法取出可重用的 cell, 在一个 cell 离开屏幕显示的范围内, 会自动被加入到缓存池中,然后在缓存池中取出一个 cell, 放到当前屏幕上;用重用机制会节省性能，避免出现一些因为网络因素而造成的卡顿现象。

复用机制避免了大量的初始化过程, 我们可以节约很大一部分资源; 下面我们来试着模仿 tableview 创建一个复用视图吧~

### 实例化一个重用池;

新建一个继承自`NSObject` 的类, reusePool; 在reusePool 中定义三个方法,

*   获取重用 view `-(UIView *)getReuseTableView;`
    
*   添加 view 到重用池中 `-(void)addViewForReusePool:(UIView *)view;`
    
*   重置重用池 `-(void)reset;`
    

在类的`.m`文件中, 实现刚才定义的几个方法, 并定义两个属性, `wattingQueue` 和 `usingQueue`, 作为存放视图的容器; 实现刚才定义的几个方法
<!-- more -->
```
- (instancetype)init
{
    self = [super init];
    if (self) {
        _waitingQueue  = [NSMutableSet set];
        _useingQueue   = [NSMutableSet set];
    }
    return self;
}
// 如果存在, 从等待队列中移除一个, 在使用中的队列增加一个
-(UIView *)getReuseTableView{
    UIView *view = [_waitingQueue anyObject];
    if (view == nil) {
        return nil;
    }else{
        [_waitingQueue removeObject:view];
        [_useingQueue addObject:view];
        return view;
    }
}
//添加可重用视图到重用池
-(void)addViewForReusePool:(UIView *)view{
    if (view == nil) {
        return;
    }
    [_useingQueue addObject:view];
}
//重置重用池
-(void)reset{
    UIView *view = nil;
    while ((view = [_useingQueue anyObject]) ) {
        [_useingQueue removeObject:view];
        [_waitingQueue addObject:view];
    }
}
```

### 实现视图

定义一个继承自`UITableView` 的视图类, `reuseTableView`; 给 `reuseTableView` 设置一个代理方法, 来获取需要用到的数据;

```
#import <UIKit/UIKit.h>
@protocol QKReuseTableViewDataSource <NSObject>
/**
 获取字母索引条
 @param tableview tableview
 @return 结果数组
 */
-(NSArray <NSString*> *)indexTitleFromReuseTable:(UITableView *)tableview;
@end
@interface QKReuseTableView : UITableView
@property (nonatomic,weak) id   <QKReuseTableViewDataSource> indexDataSource;
@end
```

在`. m` 文件中定义两个属性

```
@interface QKReuseTableView ()
{
    UIView *containerView;
    QKReusePool *reusePool;
}
@end
```

重写 `tableview` 的`reloadData` 方法

```
-(void)reloadData{
    [super reloadData];
    if (containerView == nil) {
        containerView = [[UIView alloc] init];
        containerView.backgroundColor = UIColor.whiteColor;
        [self.superview insertSubview:containerView aboveSubview:self];
    }
    if (reusePool == nil) {
        reusePool = [[QKReusePool alloc] init];
    }
    //初始化的时候重置重用池
    [reusePool reset];
//    reload 索引条
    [self reloadIndexTitle];
}
-(void)reloadIndexTitle{
//    设置一个title数组
    NSArray <NSString *> * titleArray  = nil;
    if ([self.indexDataSource respondsToSelector:@selector(indexTitleFromReuseTable:)]) {
        titleArray = [self.indexDataSource indexTitleFromReuseTable:self];
    }
    if (!titleArray  titleArray.count<=0) {
        [containerView setHidden:YES];
        return;
    }
    NSUInteger count = titleArray.count;
    CGFloat btnWidth = 60;
    CGFloat btnHeight = self.frame.size.height/count;
    for (int i = 0; i< count; i++) {
        NSString *title = [titleArray objectAtIndex:i];
        UIButton *btn = (UIButton *)[reusePool getReuseTableView];
        if (btn == nil) {
            btn = [[UIButton alloc] initWithFrame:CGRectZero];
            btn.backgroundColor = UIColor.whiteColor;
            [reusePool addViewForReusePool:btn];
            NSLog(@"creat  a  btn");
        }else{
            NSLog(@"reuse button");
        }
        [containerView addSubview:btn];
        [btn setTitle:title forState:UIControlStateNormal];
        [btn  setTitleColor:UIColor.blackColor forState:UIControlStateNormal];
        [btn setFrame:CGRectMake(0, i*btnHeight, btnWidth, btnHeight)];
    }
    [containerView setHidden:NO];
    containerView.frame = CGRectMake(self.frame.origin.x + self.frame.size.width - btnWidth, self.frame.origin.y, btnWidth, self.frame.size.height);
}
```

代码在reuseCell 文件夹 [git仓库链接地址](https://gitee.com/SuQiankun/iOSTestTool.git)