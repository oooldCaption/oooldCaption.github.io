---
title: 拥抱一下沉默的 NSString 吧~
tags:
  - iOS
id: '227'
categories:
  - - iOS 开发
date: 2019-04-29 09:36:24
---

NSString 作为作为OC的一种数据类型, 他帮我们做了很多事情, 而且也是我们在项目中运用频率很高的一个数据类型.我们经常会对它进行各式各样的操作,生成新的字符串,修改,查找,比较等等. 但是有人可能会问, 既然我已经有C的字符串了,为撒子还要在定义一个字符串对象呢? 嘿嘿,首先,我们使用NSString对象来处理字符串操作, 一方面是可以统一很多方面的东西,比如api, 例如NSArray,NSDictionary中都是存放的对象, 而且使用NSString还可以使用isKindofCLass的很多常用查询. 假如我们真的去使用了C的字符串去替代NSString的话, 那我们光是桥接代码就会让人写到吐,因为字符串在使用中的频率还是比较高的. 在和其他数据对象进行联合使用的时候,那种怪异的语法肯定也会让使用者大呼痛苦的. 向来注重用户体验的苹果肯定不会这样子干的,所以,定义一个NSString 对象还是很必要的. NSString 主要的方法都在 `Foundation/NSString.h` 中定义, 提供了很多的方法接口
<!-- more -->
### 常量字符串

```
NSString *name = @"两好三坏";
NSString *addName = [@"name:" stringByAppendingString:name];
```

常量字符串不仅可以作为消息的参数, 还可以作为消息的接收者;

### NSString 比较

字符串比较出结果后会返回一个 `NSComparisonResult` 类型的枚举值. 分别是 \* NSOrderedAscending 左侧小于右侧 \* NSOrderedSame 两者相同 \* NSOrderedDescending 右侧小于左侧

```
//比较消息的接受者he参数字符串aString,参数不可以是nil,此方法区分大小写 如果相比较两个字符串是否相同的话还可以使用 isEqualToString:
-(NSComparisonResult)compare:(NSString *) aString;
// 不区分大小写比较, 上一个compare 的方法也可以通过添加参数进行选择大小写是否作为判断标准
-(NSComparisonResult) caseInsensitiveCompare:(NSString *)aString;
- (BOOL)isEqualToString:(NSString *)aString;
NSString *str0 = @"testString";
NSString *str1 = @"testString";
if([str0 isEqualToString:str1]){
   NSLog(@"字符串完全相等");
}
//判断是否以 参数 开头的string
- (BOOL)hasPrefix:(NSString *)str;
//判断是够以 参数 结尾的string
- (BOOL)hasSuffix:(NSString *)str;
```

### 截取字符串

```
// 返回一个anIndex 开始到结尾的新字符串, 不包含form索引
- (NSString *)substringFromIndex:(NSUInteger)from;
//返回一个新字符串, 从to开始到结束,包含to的索引
- (NSString *)substringToIndex:(NSUInteger)to;
//通过一个range返回一个新的字符串对象
- (NSString *)substringWithRange:(NSRange)range;
```

### 检索和置换

```
NSString *str0 = @“nice to meet you";
NSString *temp = @"meet";
NSRange rang = [str0 rangeOfString:temp];
NSLog(@"搜索的字符串在str0中起始点的index 为 %d", rang.location);
NSLog(@"搜索的字符串在str0中结束点的index 为 %d", rang.location + rang.length);
NSString *str = [str0 stringByReplacingCharactersInRange:rang withString:@"met"];
NSLog(@"替换后字符串为%@", str);
//将字符串中" " 全部替换成 *
str = [str0 stringByReplacingOccurrencesOfString :@" " withString:@"@"];
NSLog(@"替换后字符串为%@", str);
//还可以使用下面这个方法进行替换
//stringByReplacingOccurrencesOfString:(NSString *)withString:(NSString *) options:(NSStringCompareOptions) range:(NSRange)
```

### 类型转换

可以使用一些方法将NSString 转成其它基础数据类型; NSString \*temp = @"11.21"; \* \[str doubleValue\]; \* \[str intValue\] \* \[str integerValue\] \* \[str boolValue\] \* ...

### 路径处理

文件的路径可以使用 NSString 来表示,而且NSString提供了常用的处理文件路径的方法; eg:

```
// @"/tmp/image/cat.tiff"
//返回路径的最后一部分 -> cat.tiff
-(NSString *)lastPathComponent;
//将str 追加到现有字符串的末尾并返回.会根据需要自动追加分隔符
- (NSString *)stringByAppendingPathComponent:(NSString *)str;
//扩展名
-(NSString *)pathExtension;
//将 . 和指定的扩展名追加到到最后一个组成部分上
- (nullable NSString *)stringByAppendingPathExtension:(NSString *)str;
// 删除文件的扩展名.如果不存在,返回原来的字符串
-(NSString *)stringByDeletingPathExtension;
//判断是不是一个绝对路径
-(BOOL)isAbsolutePath;
...
```

### 文件的输入和输出

```
// 通过一个 文件内容  初始化一个 nsstring;
[NSString stringWithContentsOfFile:@"" encoding:NSUTF8StringEncoding error:nil]
//将字符串内容写入到 path 的路径当中; 成功就返回YES/
[@"hduashfuiasifbsa" writeToFile:path atomically:YES encoding:NSUTF8StringEncoding error:nil];
```