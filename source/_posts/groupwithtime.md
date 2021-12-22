---
title: 对 tableView 数据源中的时间进行分组排序
tags:
  - iOS
id: '159'
categories:
  - - iOS 开发
date: 2018-12-10 15:15:33
---

遇到一个需求, 需要从后台返回的消息或者交易列表中根据日期进行分组排序, 界面类似 ![](https://img.52smile.vip/2018-12-10-071940.jpg) 根据日期或者消息类型,等等进行排序; 数据格式类似下面这种:

```
[
    {
    "date":"2018-12-12",
    "titleInfo":"xxxxx",
    },
    {
    "date":"2018-12-12",
    "titleInfo":"xxxxx",
    },
    {
    "date":"2018-12-12",
    "titleInfo":"xxxxx",
    },
    {
    "date":"2018-12-12",
    "titleInfo":"xxxxx",
    },
]
```
<!-- more -->
可以再获取到数据后对数据先分好组 然后在每一个创建空数组 在遍历数据源中的数据 如果时间和组头一样就加入对应空数组中; 代码如下:

```
- (void)handleArraysAction {
    NSMutableArray *timeArr = [NSMutableArray array];
    __weak typeof(self) weakSelf = self;
    [self.requestDataArray enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        QKTimeGroupModel* headModel = obj;
        [timeArr addObject:headModel.date_str];
    }];
    NSSet *set = [NSSet setWithArray:timeArr];//使用asset把timeArr的日期去重
    NSArray *userArray = [set allObjects];//去重后的时间数组
    //遍历userArray   按照userArray里的时间分成几个组每个组都是空的数组
    //    self.dataSource = [NSMutableArray array];
    [userArray enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        NSMutableArray *arr = [NSMutableArray array];
        [weakSelf.dataSource addObject:arr];
    }];
    //遍历self.requestDataArray取其中每个数据的日期看看与userArray里的那个日期匹配就把这个数据装到sectionArray对应的组中
    [self.requestDataArray enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        QKTimeGroupModel* headModel = obj;
        for (NSString *timeStr in userArray) {
            if ([timeStr isEqualToString:headModel.date_str]) {
                NSMutableArray *emptyArr = [weakSelf.dataSource objectAtIndex:[userArray indexOfObject:timeStr]];//取到对应的空数组
                [emptyArr addObject:headModel];
            }
        }
    }];
}
```

也可以额外定义一个时间变量和一个 section 的变量, 循环对比每个 date 的值, 然后根据对比结果决定加入到数组不同的 section 中; 代码如下:

```
-(void)testDict{
    NSMutableArray *modelArray = [QKTimeGroupModel getData];
    NSString *pervDate = @"temp";
    NSUInteger sectionNumber = 0;
    for (int i = 0; i < modelArray.count; i++) {
        QKTimeGroupModel *model = modelArray[i];
        if ([model.date_str isEqualToString:pervDate]) {
            NSMutableArray *arr = (NSMutableArray *)modelArray[sectionNumber];
            [arr addObject:model];
            [modelArray replaceObjectAtIndex:sectionNumber withObject:arr];
        }else{
            pervDate = model.date_str;
            NSMutableArray *arr = [NSMutableArray array];
            [arr addObject:model];
            [modelArray addObject:arr];
            sectionNumber = modelArray.count - 1;
        }
    }
}
```