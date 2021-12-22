---
title: iOS中KVO使用和底层原理
tags:
  - iOS
id: '167'
categories:
  - - iOS 开发
date: 2018-12-17 14:45:49
---

iOS 中 **KVO**, 是`key-value-observing` 的缩写, 是Objective-C 对**观察者设计模式**的一种实现,类似观察者设计模式的还有`NSNotificationCenter`,不过一个是一对一(`KVO`),一个是一对多(`NSNotificationCenter`) ; 一般继承自`NSObject` 的对象都支持KVO. 日常开发中我们常常会监听数据模型的变化, 从而达到根据数据模型的修改对视图进行更新的要求;

### KVO 常用方法

```
/*
注册监听器
监听器对象为observer，被监听对象为消息的发送者即方法的调用者在回调函数中会被回传
监听的属性路径为keyPath支持点语法的嵌套
监听类型为options支持按位或来监听多个事件类型
监听上下文context主要用于在多个监听器对象监听相同keyPath时进行区分
添加监听器只会保留监听器对象的地址，不会增加引用，也不会在对象释放后置空，因此需要自己持有监听对象的强引用，该参数也会在回调函数中回传
*/
- (void)addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath options:(NSKeyValueObservingOptions)options context:(nullable void *)context;
eg:
[obj addObserver:observer forKeyPath:@"value" options:NSKeyValueObservingOptionNew context:NULL];
/*
删除监听器
监听器对象为observer，被监听对象为消息的发送者即方法的调用者，应与addObserver方法匹配
监听的属性路径为keyPath，应与addObserver方法的keyPath匹配
监听上下文context，应与addObserver方法的context匹配
*/
- (void)removeObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath context:(nullable void *)context;
/*
监听器对象的监听回调方法
keyPath即为监听的属性路径
object为被监听的对象
change保存被监听的值产生的变化
context为监听上下文，由add方法回传
*/
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context;
```

### 举个栗子 🌰

假如我的一个数据模型(student)的属性(age)发生了变化, 我需要在对应的视图进行修改这个属性的显示;

```
@property (nonatomic, strong) StudentModel  *student;
- (void)viewDidLoad {
  [super viewDidLoad];
  _student = [[Student alloc] init];
  _student.age = 10;
  [_student addObserver:self forKeyPath:@"age" options:NSKeyValueObservingOptionNew context:nil];
  [self requestData];
}
- (void)requestData{
    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
    [manager POST:urlString parameters:params progress:nil success:^(NSURLSessionDataTask * _Nonnull task, id  _Nullable responseObject) {
       NSInteger age = [(NSDictionary *)responseObject[@"age"] integerValue];
       _student.age = age;
   } failure:^(NSURLSessionDataTask * _Nullable task, NSError * _Nonnull error) {
   }];
}
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary*)change context:(void *)context{
  if ([keyPath isEqualToString:@"age"] && object == _student){
        NSLog(@"当前年龄是: %ld",_student.age);
    }
}
- (void)dealloc{
  [_student removeObserver:self forKeyPath:@"age"];
}
```

### 实现原理 🚀

Apple 使用 isa 混写技术实现 KVO , 当观察对象 `testClass`的时候, KVO 会动态创建一个继承自`testClass`的类 `NSKVONotifying_testClass` 并重写监听属性的 setter 方法,在调用 setter 的前后, 会观察对象属性的更改情况;子类拥有自己的 set 实现,

```
    [self willChangeValueForKey:@"value"];
    _value = value;
    [self didChangeValueForKey:@"value"];
```

所以, 通过 `set`方法进行赋值, 通过 KVC `setValue:forKey:`两种方式都可以对 KVO 生效; 但是如果想通过对成员变量直接赋值的话, 需要手动添加 KVO 才可以生效

```
   QKTestObject *obj = [[QKTestObject alloc] init];
    QKTestObserver *observer = QKTestObserver.alloc.init;
//    通过 KVO 监听 obj 的变化
    [obj addObserver:observer forKeyPath:@"value" options:NSKeyValueObservingOptionNew context:NULL];
//    通过 setter 修改 value, 监听生效
//    obj.value = 2;
//    通过 kvo 修改 value , 监听生效
//    [obj setValue:@4 forKey:@"value"];
//    通过成员变量赋值. 未执行监听方法中的输出
    /*
        成员变量需要手动 添加 KVO , 监听才会生效
         [self willChangeValueForKey:@"value"];
         _value += 1;
         [self didChangeValueForKey:@"value"];
     */
    [obj changeValue];
```

[代码地址:](https://gitee.com/SuQiankun/iOSTestTool/blob/master/testTool/KVO/QKKVOTestCtrl.m)