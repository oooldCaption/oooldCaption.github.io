---
title: '分身不乏术,Objective-C的多线程'
tags:
  - iOS
id: '231'
categories:
  - - iOS 开发
date: 2019-05-03 20:48:49
---

我们在现实生活中,往往会在统一调时间线上同时做好几件事情, 比如刷牙的时候看手机,为了不浪费时间,我们总是同时进行两件三件甚至更多的事情;而在编程的世界中, 我们可以通过软件或者硬件上实现多个线程并发执行的技术来做到类似现实世界中的行为, 这就叫做多线程;能够大大提升整体处理性能;

很多编程语言都实现了这种功能,java, .net,C++, php的pthreads和swoole,oc当然也不例外, 下面我们就来了解一下我们在oc中会经常用到的多线程技术GCD和NSOperation,NSOperationQueue;
<!-- more -->
## GCD

### GCD的好处

能够充分利用iOS或者macOS多核心的特性,大大提高整体的处理性能和计算能力;

### GCD的队列和任务

#### 任务

即一个操作行为,或者是要执行的代码块; 类似买票的行为,即一个任务;

#### 队列

执行任务的调度池, 用来存放任务,一个一个的任务被创建好之后放入队列中等待执行; 采用先进先出的原则,类似排队买票的行为;

##### 同步队列(sync)

添加任务同步队列,任务会一个接着一个去执行,后一个任务等待前一个任务的完毕状态; 同步队列不会开启新的线程; 类似一个窗口买票的而行为, 长长的队伍只能慢慢的等待;

##### 异步队列(async)

任务被添加到异步队列后, 如果线程开启的足够多, 那么每一个任务不会做任何等待. 每个任务都可已开启一新的线程去执行; 类似多个窗口买票的行为, 10个窗口,10个人买票,一个人一个窗口, 不用等待; 当然啦, 理论上是可行的,但是实际开启线程过多会很影响CPU的效率, CPU会一直对调度池进行轮询; CPU就累死了;所以, 合理开启线程个数还是很有必要的;

### GCD的使用

GCD的使用非常简单, 我们创建一个队列,然后将想要执行的任务添加到队列中;

#### 创建队列

```
// 串行队列的创建方法
dispatch_queue_t queue = dispatch_queue_create("com.test.gcd", DISPATCH_QUEUE_SERIAL);
// 并发队列的创建方法
dispatch_queue_t queue = dispatch_queue_create("com.test.gcd", DISPATCH_QUEUE_CONCURRENT);
//全局并发队列
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0)
//主队列
dispatch_queue_t queue = dispatch_get_main_queue();
```

#### 任务

```
// 同步执行任务创建方法
dispatch_sync(queue, ^{
    // 这里放同步执行任务代码
});
// 异步执行任务创建方法
dispatch_async(queue, ^{
    // 这里放异步执行任务代码
});
```

实际使用当中,使用的比较多的就是`异步任务+并行队列` ,

```
-(void)asyncWithParallelQueue{
   NSLog(@"currentThread---%@",[NSThread currentThread]);  // 打印当前线程
    NSLog(@"asyncConcurrent---begin");
    dispatch_queue_t queue = dispatch_queue_create("com.test.gcd", DISPATCH_QUEUE_CONCURRENT);
    dispatch_async(queue, ^{
        // 追加任务1
        for (int i = 0; i < 2; ++i) {
            [NSThread sleepForTimeInterval:2];              // 模拟耗时操作
            NSLog(@"1---%@",[NSThread currentThread]);      // 打印当前线程
        }
    });
    dispatch_async(queue, ^{
        // 追加任务2
        for (int i = 0; i < 2; ++i) {
            [NSThread sleepForTimeInterval:2];              // 模拟耗时操作
            NSLog(@"2---%@",[NSThread currentThread]);      // 打印当前线程
        }
    });
    dispatch_async(queue, ^{
        // 追加任务3
        for (int i = 0; i < 2; ++i) {
            [NSThread sleepForTimeInterval:2];              // 模拟耗时操作
            NSLog(@"3---%@",[NSThread currentThread]);      // 打印当前线程
        }
    });
    NSLog(@"asyncConcurrent---end");
    }
```

可以看到除了主线程之外，系统开启了3个线程，并且任务可以看做是同时执行的。（异步执行具备开启新线程的能力。且并发队列可开启多个线程，同时执行多个任务）。所有任务是在打印的syncConcurrent---begin和syncConcurrent---end之后才执行的。说明当前线程没有等待，而是直接开启了新线程，在新线程中执行任务（异步执行不做等待，如果存在空闲的线程可以继续执行任务）。

### 线程之间的通信

如果我们在主线中使用了耗时任务例如下载,合成图片,I/O等操作时, 会对UI造成阻塞, 造成界面不能响应触摸,滚动,刷新等异常,所以我们一般会将这些耗时操作放到子线程中进行执行, 这样就会有一个不可避免的问题,就是我们在子线程中处理完操作后需要把处理结果放到主线程中进行更新数据或者UI,这就会有一个线程之间通信的问题, 万幸的是,GCD已经为我们提供了相应的方法;

```
/**
 * 线程间通信
 */
- (void)sendMSG{
    // 获取全局并发队列
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    // 获取主队列
    dispatch_queue_t mainQueue = dispatch_get_main_queue();
    dispatch_async(queue, ^{
        // 异步追加任务
        for (int i = 0; i < 2; ++i) {
            [NSThread sleepForTimeInterval:2];              // 模拟耗时操作
            NSLog(@"1---%@",[NSThread currentThread]);      // 打印当前线程
        }
        // 回到主线程
        dispatch_async(mainQueue, ^{
            // 追加在主线程中执行的任务
            [NSThread sleepForTimeInterval:2];              // 模拟耗时操作
            NSLog(@"2---%@",[NSThread currentThread]);      // 打印当前线程
        });
    });
}
```

### 线程安全

假设我们有这样一个出售车票的场景: 余票500张,同时存在两个或者多个窗口进行售票,如果我们同时访问了这个余票并进行出售是不是会存在问题呢?

```
- (void)unSaveTicket{
    NSLog(@"currentThread---%@",[NSThread currentThread]);  // 打印当前线程
    NSLog(@"semaphore---begin");
    self.ticketCount = 500;
    // queue1 代表售卖窗口1
    dispatch_queue_t queue1 = dispatch_queue_create("com.test.gcd", DISPATCH_QUEUE_SERIAL);
    // queue2 代表售卖窗口2
    dispatch_queue_t queue2 = dispatch_queue_create("com.test.gcd", DISPATCH_QUEUE_SERIAL);
    __weak typeof(self) weakSelf = self;
    dispatch_async(queue1, ^{
        [weakSelf saleTicketunSafe];
    });
    dispatch_async(queue2, ^{
        [weakSelf saleTicketunSafe];
    });
}
/**
 * 售卖火车票(非线程安全)
 */
- (void)saleTicketunSafe {
    while (1) {
        if (self.ticketCount > 0) {  //如果还有票，继续售卖
            self.ticketCount--;
            NSLog(@"%@", [NSString stringWithFormat:@"剩余票数：%d 窗口：%@", self.ticketCount, [NSThread currentThread]]);
            [NSThread sleepForTimeInterval:0.2];
        } else { //如果已卖完，关闭售票窗口
            NSLog(@"所有火车票均已售完");
            break;
        }
    }
}
```

![-w1086](https://52smile.vip/wp-content/uploads/2019/05/15568928052630.jpg)￼ 可以看到,这里的票的个数是不安全的;造成这样的原因是因为多个线程访问同一个资源造成的,如果我们想要避免这种问题,我们需要对这个数据进行加锁;

```
/**
 * 线程安全：使用 synchronized 加锁
 * 初始化火车票数量、卖票窗口(线程安全)、并开始卖票
 */
- (void)initTicketStatusSave {
    NSLog(@"currentThread---%@",[NSThread currentThread]);  // 打印当前线程
    NSLog(@"semaphore---begin");
    self.ticketCount = 50;
    // queue1 代表票售卖窗口1
    dispatch_queue_t queue1 = dispatch_queue_create("com.test.gcd", DISPATCH_QUEUE_SERIAL);
    // queue2 代表售卖窗口2
    dispatch_queue_t queue2 = dispatch_queue_create("com.test.gcd", DISPATCH_QUEUE_SERIAL);
    __weak typeof(self) weakSelf = self;
    dispatch_async(queue1, ^{
        [weakSelf saleTicketSafe];
    });
    dispatch_async(queue2, ^{
        [weakSelf saleTicketSafe];
    });
}
/**
 * 售卖火车票(线程安全)
 */
- (void)saleTicketSafe {
    while (1) {
        @synchronized (self) {
            if (self.ticketCount > 0) {  //如果还有票，继续售卖
                self.ticketCount--;
                NSLog(@"%@", [NSString stringWithFormat:@"剩余票数：%zd 窗口：%@", self.ticketCount, [NSThread currentThread]]);
            } else { //如果已卖完，关闭售票窗口
                NSLog(@"所有火车票均已售完");
                break;
            }
        }
    }
}
```

### 栅栏任务

有时候我们需要在某个函数后执行特定的任务. 这时候我们需要用到oc提供的栅栏函数'

```
//栅格
-(void)barrier{
    /**
     不能使用quan'jue'de全局的并发队列. 会出现紊乱
     栅格函数使用后, 在栅格函数之前的, 先进行执行;
     在栅格函数之后, 等待栅格函数执行完成后在执行;
     */
    dispatch_queue_t queue = dispatch_queue_create("dispatch_barrier", DISPATCH_CURRENT_QUEUE_LABEL);
    dispatch_async(queue, ^{
        NSLog(@"---1----%@",[NSThread currentThread]);
    });
    dispatch_async(queue, ^{
        NSLog(@"---2----%@",[NSThread currentThread]);
    });
    dispatch_barrier_async(queue, ^{
        NSLog(@"---barrier----%@",[NSThread currentThread]);
    });
    dispatch_async(queue, ^{
        NSLog(@"---3----%@",[NSThread currentThread]);
    });
    dispatch_async(queue, ^{
        NSLog(@"---4----%@",[NSThread currentThread]);
    });
}
```

这个执行是执行完1,2之后执行`barrier`,然后等待`barrier`执行完成后,执行3,4; 栅栏作为阻挡3,4的屏障,等待1,2 执行完成后才开始下一步的执行;

### 延迟执行

在指定某个时间间隔后执行某段任务.

```
//延时执行
-(void)disafter{
   //延时两秒钟执行 run
//    [self performSelector:@selector(run:) withObject:@"两好三坏" afterDelay:2];
    //可以传入其他队列
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2 * NSEC_PER_SEC)),
                   dispatch_get_main_queue(), ^{
                       [self run:@"两好三坏"];
    });
    // 使用NSTimer, 作为定时器进行操作
    [NSTimer scheduledTimerWithTimeInterval:2 target:self selector:@selector(run:) userInfo:nil repeats:NO];
}
```

### 一次性代码

我们在创建单例、或者有整个程序运行过程中只执行一次的代码时，我们就用到了 GCD 的 dispatch\_once 函数。使用 dispatch\_once 函数能保证某段代码在程序运行过程中只被执行1次，并且即使在多线程的环境下，dispatch\_once也可以保证线程安全。

```
//一次性函数, 类似单例,资源加载等;注意与懒加载的区别;
-(void)onceCode{
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        NSLog(@"hhhh");
    });
}
```

eg: 单例:

```
static QKSingleton *_singleton;
/**
 返回一个单例对象
 @return 单例对象
 */
+(instancetype)shareSingleton{
        static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        _singleton = [[self alloc] init];
    });
    return _singleton;
}
+(instancetype)allocWithZone:(struct _NSZone *)zone{
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        _singleton = [super allocWithZone:zone];
    });
    return _singleton;
}
-(id)copyWithZone:(NSZone *)zone{
    return _singleton;
}
```

### 快速迭代

一般我们使用for循环进行数据遍历,然后再循环内对单个元素进行操作; 这样的问题在于如果我的数据量足够大或者单个元素操作耗时很高,那么在效率上会很低,因为这个实际上是在一个时间线的一个任务列中进行操作的,相当于在同步队列中执行一个串行队列; 而GCD为我们提供了一个快速迭代的方案: 场景如下: 我想把`From`文件夹中的文件一个一个的移动或者复制到`TO`文件夹中, 我们都知道I/O操作是很耗时间的. 下面有两个方法,一个是使用普通的for循环进行遍历, 一个是使用GCD提供的`dispatch_apply`进行操作;大家可以看一下对比效果;

```
//快速遍历
-(void)quickBlock{
    /**
     可以i替代部分for循环的功能;
     eg: w想将一个文件夹中的文件剪切或者拷贝到另外一个文件夹中,如果我们通过for循环
     去做这件事情,当文件数量达到一定的数量的时候效率肯定降低; 但是如果我们使用GCD进行
     处理, 会好很多;
     */
    // 使用常规操作移动文件
    // [self  normalMoveFile];
    // 使用 GCD 移动文件
//    [self applayGCDMoveFile];
}
//使用 GCD 移动文件, 快速遍历
-(void)applayGCDMoveFile{
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    // 常规操作
    NSFileManager *mgr = [NSFileManager defaultManager];
    NSString *fromPath = @"/User/su/Desktop/Form";
    NSString *toPath = @"/User/su/Desktop/To";
    NSArray *subPaths = [mgr subpathsAtPath:fromPath];
    dispatch_apply(subPaths.count, queue, ^(size_t index) {
        NSString *subPath = subPaths[index];
        NSString *fromFullPath = [fromPath stringByAppendingPathComponent:subPath];
        NSString *toFullPath = [toPath stringByAppendingPathComponent:subPath];
        [mgr moveItemAtPath:fromFullPath toPath:toFullPath error:nil];
    });
}
//常规移动文件操作
-(void)normalMoveFile{
    // 常规操作
    NSFileManager *mgr = [NSFileManager defaultManager];
    NSString *fromPath = @"/User/su/Desktop/Form";
    NSString *toPath = @"/User/su/Desktop/To";
    NSArray *subPaths = [mgr subpathsAtPath:fromPath];
    for (NSString *subPath  in subPaths) {
        NSString *fromFullPath = [fromPath stringByAppendingPathComponent:subPath];
        NSString *toFullPath = [toPath stringByAppendingPathComponent:subPath];
        [mgr moveItemAtPath:fromFullPath toPath:toFullPath error:nil];
    }
}
```

### GCD队列组

我们有的时候可能会遇到需要合并多个线程中的执行结果, 例如下载多个素材后进行合并; 或者是类似迅雷分块下载后的文件合并;

```
//GCD队列组
-(void)GCDWithGroup{
    /*
     合并两张图片或者多个任务处理完成后合并结果
     */
    //定义一个c并行队列
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    //创建一个队列组
    dispatch_group_t group = dispatch_group_create();
    //异步任务添加到队列组
    dispatch_group_async(group, queue, ^{
        //sub task
    });
    //异步任务添加到队列组
    dispatch_group_async(group, queue, ^{
        //sub task
    });
    //异步任务添加到队列组
    dispatch_group_async(group, queue, ^{
        //sub task ....
    });
    //监听队列组各个子任务完成后的合并
    dispatch_group_notify(group, queue, ^{
        // sub task finish
        //TODO
    });
}
```

## NSOPeration NSOperationQueue

与GCD的定义类似, 也存在任务和队列的概念,基本一致;在此就不在重复啦;

### NSOperationQueue

任务队列,可以开启串行&并行队列 我们可以通过 `maxConcurrentOperationCount`进行设置,决定是以何种队列进行执行

*   maxConcurrentOperationCount 默认情况下为-1，表示不进行限制，可进行并发执行。
*   maxConcurrentOperationCount 为1时，队列为串行队列。只能串行执行。
*   maxConcurrentOperationCount 大于1时，队列为并发队列。

### NSOperation

`NSOPeration`是一个抽象类, 不作具体事务;具体事务由他的子类进行操作; 他有三个子类

*   使用子类 NSInvocationOperation
*   使用子类 NSBlockOperation
*   自定义继承自 NSOperation 的子类，通过实现内部相应的方法来封装操作。

#### NSInvocationOperation

```
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];
    //通过suspened 可以执行挂起; 任务挂起后并咩有消失 , 通过设置suspended = NO. 继续任务
    //注意如果实在自定义的op对象时,是否需要考虑到界面优化
    //queue.suspended = YES;
    //定义操作任务
    NSInvocationOperation *opOne = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(run:) object:@"xiaoxiaosu"];
    NSInvocationOperation *opTwo = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(run:) object:@"xiaoxiaosu"];
    //添加任务
    [queue addOperation:opOne];
    [queue addOperation:opTwo];
```

#### NSBlockOperation

```
//测试并行OperationQueue
-(void)operationQueue{
/*
    [NSOperationQueue mainQueue]; 如果使用mainqueueu, 那么获取到的是主队列
    如果使用的是alloc 方法获取,那么是 并行
 */
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];
    //设置最大并发数量, 值取1的时候可以看做是串行队列
    queue.maxConcurrentOperationCount = 3;
    //通过suspened 可以执行挂起; 任务挂起后并咩有消失 , 通过设置suspended = NO. 继续任务
    //注意如果实在自定义的op对象时,是否需要考虑到界面优化
    //queue.suspended = YES;
    //定义操作任务
    NSBlockOperation *opBlock = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"opBlock");
    }];
    [opBlock addExecutionBlock:^{
        NSLog(@"opblock add block task");
    }];
    //添加任务
    [queue addOperation:opBlock];
    //还可以通过block直接添加task
    [queue addOperationWithBlock:^{
    }];
    //取消所有队列, 注意与suspended的区别 ;
//    [queue cancelAllOperations];
}
```

#### 自定义NSOperation子类实现

这种子类一般可以做比较复杂的操作,我们可以省去在上面两种任务中大段又长又臭的代码,封装在子类实现;

```
// QKOperation.h 文件
#import <Foundation/Foundation.h>
@interface QKOperation.h : NSOperation
@end
// QKOperation.h.m 文件
#import "QKOperation.h"
@implementation QKOperation.h
- (void)main {
    if (!self.isCancelled) {
        for (int i = 0; i < 2; i++) {
            [NSThread sleepForTimeInterval:2];
            NSLog(@"1---%@", [NSThread currentThread]);
        }
    }
}
@end
```

### 依赖操作

我们有的时候可能会遇到这种情况: 任务1中的数据在任务2中是必须的,也就是说任务2是依赖于任务1的. 在GCD中我们可以使用栅栏进行操作, 在NSOperation中我们可以更方便的对任务进行设置依赖;

```
//NSOperation的依赖和监听
-(void)opRelyOnOp{
    NSBlockOperation *op1 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"task1");
    }];
    NSBlockOperation *op2 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"task2");
    }];
    NSBlockOperation *op3 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"task3");
    }];
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];
    [queue addOperation:op1];
    [queue addOperation:op2];
    [queue addOperation:op3];
    /*
     如果想要设置op3依赖op1,2执行完成后执行,可以使用如下的代码:
     也s可以op3 -> op2  -> op1
     但是要注意防止出现循环依赖: op3 -> op2  -> op1 -> op3
     # 也可以设置不同队列进行依赖
     queue queue1 两个不同队列之间的任务依赖
     */
    [op3 addDependency:op1];
    [op3 addDependency:op2];
    /*
     监听操作完成
     */
    op3.completionBlock = ^{
        NSLog(@"op3 task finish");
    };
}
```

### NSOperation 线程通信

可以以很简明的代码实现GCD中的线程通信

```
//NSOperation 的线程通信
-(void)opMsg{
    //eg:合成图片
    NSBlockOperation *down1 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"img1 download");
    }];
    NSBlockOperation *down2 = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"img2 download");
    }];
    NSBlockOperation *complate = [NSBlockOperation blockOperationWithBlock:^{
        NSLog(@"合并 img1 & img2");
    }];
    [complate addDependency:down1];
    [complate addDependency:down2];
    //线程间的基本通信;
    NSOperationQueue *queue = [[NSOperationQueue alloc]init];
    [queue addOperationWithBlock:^{
        NSURL *url = [NSURL URLWithString:@"remoteImgURL"];
        NSData *data = [NSData dataWithContentsOfURL:url];
        UIImage *image = [UIImage imageWithData:data];
        [[NSOperationQueue mainQueue] addOperationWithBlock:^{
            UIImageView *imgview = [UIImageView new];
            imgview.image = image;
        }];
    }];
}
```

### NSOperation 线程安全

```
-(void)safeSallTicket{
    self.ticketCount = 50;
    NSOperationQueue *queue1 = [[NSOperationQueue alloc] init];
    NSOperationQueue *queue2 = [[NSOperationQueue alloc] init];
    queue1.maxConcurrentOperationCount = queue2.maxConcurrentOperationCount = 1;
    __weak typeof(self) weakSelf = self;
    NSBlockOperation *op1 = [NSBlockOperation blockOperationWithBlock:^{
        [weakSelf sallTicketWithOp];
    }];
    NSBlockOperation *op2 = [NSBlockOperation blockOperationWithBlock:^{
        [weakSelf sallTicketWithOp];
    }];
    [queue1 addOperation:op1];
    [queue2 addOperation:op2];
}
-(void)sallTicketWithOp{
    while (1) {
        // 加锁
        [self.lock lock];
        if (self.ticketCount > 0) {
            //如果还有票，继续售卖
            self.ticketCount--;
            NSLog(@"%@", [NSString stringWithFormat:@"剩余票数:%zd 窗口:%@", self.ticketCount, [NSThread currentThread]]);
            [NSThread sleepForTimeInterval:0.2];
        }
        // 解锁
        [self.lock unlock];
        if (self.ticketCount <= 0) {
            NSLog(@"所有火车票均已售完");
            break;
        }
    }
}
```

### NSOperation 常用属性和方法

*   取消操作方法
    *   (void)cancel; 可取消操作，实质是标记 isCancelled 状态。
*   判断操作状态方法
    *   (BOOL)isFinished; 判断操作是否已经结束。
    *   (BOOL)isCancelled; 判断操作是否已经标记为取消。
    *   (BOOL)isExecuting; 判断操作是否正在在运行。
    *   (BOOL)isReady; 判断操作是否处于准备就绪状态，这个值和操作的依赖关系相关。
*   操作同步
    *   (void)waitUntilFinished; 阻塞当前线程，直到该操作结束。可用于线程执行顺序的同步。
    *   (void)setCompletionBlock:(void (^)(void))block; completionBlock 会在当前操作执行完毕时执行 completionBlock。
    *   (void)addDependency:(NSOperation \*)op; 添加依赖，使当前操作依赖于操作 op 的完成。
    *   (void)removeDependency:(NSOperation \*)op; 移除依赖，取消当前操作对操作 op 的依赖。 @property (readonly, copy) NSArray \*dependencies; 在当前操作开始执行之前完成执行的所有操作对象数组。