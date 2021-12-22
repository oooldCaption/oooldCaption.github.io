---
title: iOS 界面传值
tags: []
id: '22'
categories:
  - - iOS 开发
date: 2018-04-17 14:43:55
---

在iOS开发新中经常会遇到在两个界面之间进行传值的情况,在页面之间进行数据传递有正向传递(控制器A跳转到控制器B),也有反向传值(控制器B跳转到A).可以通过属性,代理,block,通知等等. 就解释一下怎么使用block和代理进行页面之间反向传值;
<!-- more -->
使用block进行反向传值 在Xcode中创建新的项目 创建两个控制器A,B. 在A控制器中添加一个按钮,对按钮添加点击跳转到B控制器,然后再A中再次添加一个label,作为接受B控制器中传回来的值. 在B控制器中创建一个UITextField,用于输入信息. 在A控制器中.m文件中代码如下:

```
//
// ViewController.m
// 反向传值
//
// Created by 两好三坏 on 16/3/3.
// Copyright © 2016年 qinakun. All rights reserved.
//
import "ViewController.h"
使用block传值的控制器
import "BlockVC.h"
使用delegate传值的控制器
import "DelegateVC.h"
@interface ViewController ()
//用于接受数据的label
@property (weak, nonatomic) IBOutlet UILabel *inputLabel;
@end
@implementation ViewController
- (void)viewDidLoad {
  [super viewDidLoad];
  }
//跳转控制器的
- (IBAction)jumpVC:(id)sender {
  //调用block进行传值
  // [self block];
  //调用bdelegate进行传值
  [self delegate];
  }
//使用block进行反向传值
-(void)block{
UIStoryboard *sb = [UIStoryboard storyboardWithName:@"BlockSB" bundle:nil];
BlockVC *blockVc = [sb instantiateViewControllerWithIdentifier:@"blockVC"];
//inputBlock是定义好的block
[blockVc setInputBlock:^(NSString *string) {
//在这里对A控制器的label进行设置,这个string参数是从B控制器中传过来的;
self.inputLabel.text = string;
}];
[self.navigationController pushViewController:blockVc animated:YES];
}
//使用delegate进行反向传值
-(void)delegate{
DelegateVC *vc = [DelegateVC new];
//需要让控制器A成为控制器B的代理.
vc.delegate = self;
[self.navigationController pushViewController:vc animated:YES];
}
//代理方法中给input进行传值
-(void)changeValue:(NSString *)string{
self.inputLabel.text = string;
}
@end
```

接下来是在控制器B中定义一个ｂｌｏｃｋ的代码块，这个block的参数是字符串,在控制器B的.h文件中声明如下:

```
@property (nonatomic, copy) void (^inputBlock)(NSString *string);
控制器B的.m文件代码如下:
import "BlockVC.h"
@interface BlockVC ()
@property (weak, nonatomic) IBOutlet UITextView *input;
@end
@implementation BlockVC
- (void)viewDidLoad {
  [super viewDidLoad];
  }
  //点击屏幕是输入框失去第一响应者的资格
  -(void)touchesBegan:(NSSet _)touches withEvent:(UIEvent _)event{
[self.input resignFirstResponder];
}
//在控制器即将消失的时候调用,
-(void)viewWillDisappear:(BOOL)animated{
[super viewWillDisappear:animated];
//取到输入框的内容保存在代码块中,在需要的时候将参数传给控制器A
if (self.inputBlock) {
self.inputBlock (self.input.text);
}
}
@end
```

这样就算完成了一个简单的在两个页面之间进行反向传值的功能. 使用delegate进行反向传值 代理应用场景及使用步骤 对象A内部发生了一些事情,想通知对象B 对象B想监听对象A内部发生了什么事情 对象A想在自己的方法内部调用对象B的某个方法,并且对象A不能对对象B有耦合依赖 对象A想传递数据给对象B 以上情况,结果都一样:对象B是对象A的代理(delegate) 1.先搞清楚谁是谁的代理(delegate) 2.定义代理协议,协议名称的命名规范:控件类名 + Delegate 3.定义代理方法 代理方法一般都定义为@optional 代理方法名都以控件名开头 代理方法至少有1个参数,将控件本身传递出去 设置代理(delegate)对象 (比如myView.delegate = xxxx;) 代理对象遵守协议 代理对象实现协议里面该实现的方法 在恰当的时刻调用代理对象(delegate)的代理方法,通知代理发生了什么事情 (在调用之前判断代理是否实现了该代理方法) 这是代理的使用场景以及使用步骤,下面是使用delegate实现前面block实现的功能 假如控制器C想利用代理给控制器A传值,那么 控制器C要有一个协议 控制器要有一个代理属性 控制器A遵守C的代理协议,并且成为C的代理对象. 控制器C的.h文件中作出如下声明:

```
//
// DelegateVC.h
// 反向传值
//
// Created by 两好三坏 on 16/3/5.
// Copyright © 2016年 qinakun. All rights reserved.
//
import UIKit/UIKit.h
@class DelegateVC;
//协议
@protocol DelegateVCDelegate
//A需要实现的代理方法
-(void)changeValue:(NSString *)string;
@end
@interface DelegateVC : UIViewController
//代理属性
@property(nonatomic,weak) iddelegate;
@end
[/objc]
然后在.m文件中代码:
[objc]
//
// DelegateVC.m
// 反向传值
//
// Created by 两好三坏 on 16/3/5.
// Copyright © 2016年 qinakun. All rights reserved.
//
import "DelegateVC.h"
define kWidth [UIScreen mainScreen].bounds.size.width
define kHeight [UIScreen mainScreen].bounds.size.height
@interface DelegateVC ()
@property(nonatomic,strong) UITextField *textValue;
@end
@implementation DelegateVC
- (void)viewDidLoad {
  [super viewDidLoad];
  self.view.backgroundColor = [UIColor whiteColor];
  创建一个输入框,添加到控制器C的view上;
  UITextField _tf = [[UITextField alloc] init];
  tf.backgroundColor = [UIColor grayColor];
  tf.frame = CGRectMake(kWidth _0.5 - 50, kHeight * 0.5, 100, 40);
self.textValue = tf;
[self.view addSubview:_textValue];
}
//当控制器C消失的时候调用的方法
-(void)viewWillDisappear:(BOOL)animated{
[super viewWillDisappear:animated];
//如果有对象响应了代理,就执行if判断中的方法,
if ([self.delegate respondsToSelector:@selector(changeValue:)]) {
//把输入框中的text取出来作为代理方法的参数传送给控制器A
[self.delegate changeValue:self.textValue.text];
}
}
@end
```

在页面中传值得方法有很多,使用通知,KVO,单例,还有偏好设置,归档解档都可以实现,看情况哪个方便就是用哪个咯….