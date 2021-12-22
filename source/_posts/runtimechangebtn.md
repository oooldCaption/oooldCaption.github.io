---
title: 运用runtime扩大UIButton的响应区域
tags:
  - iOS
id: '124'
categories:
  - - iOS 开发
date: 2018-09-18 17:57:53
---

在日常开发中, 设计师有时候会设计一个看起来小小的按钮, 用户很难点击到这个按钮; 为了解决这个问题,简单粗暴的就是将 button 的 frame 放大, 分别调整button的 frame 和显示内容的 frame ,即麻烦,又显得怪异;如果我们使用 runtime 关联对象的方法对 button 进行扩展,去扩大 UIButton 的可交互区域,这样会更好一点 ​ 其执行原理为：OC中创建类别(Categroy)的方式，并不允许给已有的类扩展属性，只可以给其扩展方法。所以，需要使用Runtime“黑魔法”中的关联对象(Associative Object)的一些方法，动态地为某个button对象添加扩展距离的属性，然后检测UITouch事件的触摸点是否在我们扩展距离后Rect内，从而达到想要的效果。 ​ 首先，创建一个UIButton的Category，起名为EnlargeTouchArea，设置一个外界可访问的方法setEnlaEdgeWithTop:right:bottom:left，在使用时也只需使用这个方法即可，传入的四个参数分别是上、右、下、左的扩展距离。
<!-- more -->
```
//  UIButton+EnlargeTouchArea.h
#import <Foundation/Foundation.h>
#import <UIKit/UIKit.h>
@interface UIButton (EnlargeTouchArea)
- (void)setEnlargeEdgeWithTop:(CGFloat) top right:(CGFloat) right bottom:(CGFloat) bottom left:(CGFloat) left;
@end
```

​ 然后导入<objc/runtime.h>，所有Runtime的黑魔法都在这里

```
//  UIButton+EnlargeTouchArea.m
#import "UIButton+EnlargeTouchArea.h"
#import <objc/runtime.h>
@implementation UIButton (EnlargeTouchArea)
static char topNameKey;
static char rightNameKey;
static char bottomNameKey;
static char leftNameKey;
```

objc\_setAssociatedObject是一个C语言函数，这个函数被称之为“关联API”，它的作用是把top、right、bottom、left这四个从外界获取到的值与本类(self)关联起来，然后设置一个static char作为能够找到他们的Key

```
- (void) setEnlargeEdgeWithTop:(CGFloat) top right:(CGFloat) right bottom:(CGFloat) bottom left:(CGFloat) left
{
    objc_setAssociatedObject(self, &topNameKey, [NSNumber numberWithFloat:top], OBJC_ASSOCIATION_COPY_NONATOMIC);
    objc_setAssociatedObject(self, &rightNameKey, [NSNumber numberWithFloat:right], OBJC_ASSOCIATION_COPY_NONATOMIC);
    objc_setAssociatedObject(self, &bottomNameKey, [NSNumber numberWithFloat:bottom], OBJC_ASSOCIATION_COPY_NONATOMIC);
    objc_setAssociatedObject(self, &leftNameKey, [NSNumber numberWithFloat:left], OBJC_ASSOCIATION_COPY_NONATOMIC);
}
```

bjc\_getAssociatedObject同样也是一个关联API(c语言函数)，它可以通过刚刚设置的Key找到上个方法中从外界传入的top、right、bottom、left，这个api和obj\_setAssociatedObject一起使用就可以达到给已有类扩展属性的效果。最后我们通过self.bounds设置一个新的CGRect，作为扩大后的点按区域，并且返回

```
- (CGRect) enlargedRect
{
    NSNumber* topEdge = objc_getAssociatedObject(self, &topNameKey);
    NSNumber* rightEdge = objc_getAssociatedObject(self, &rightNameKey);
    NSNumber* bottomEdge = objc_getAssociatedObject(self, &bottomNameKey);
    NSNumber* leftEdge = objc_getAssociatedObject(self, &leftNameKey);
    if (topEdge && rightEdge && bottomEdge && leftEdge)
    {
        return CGRectMake(self.bounds.origin.x - leftEdge.floatValue,
                          self.bounds.origin.y - topEdge.floatValue,
                          self.bounds.size.width + leftEdge.floatValue + rightEdge.floatValue,
                          self.bounds.size.height + topEdge.floatValue + bottomEdge.floatValue);
    }
    else
    {
        return self.bounds;
    }
}
```

​ 这个方法UIView的一个实例方法，作用是，捕获当前的UITouch事件中的触摸点，检测它是否在最上层的子视图内，如果不是的话就递归检测其父视图。这样的话，我们就只是将当前某一个触摸的point与某一个rect进行比较，并没有改变Button真实的frame，从而真正的从逻辑上达到了只是扩大点按区域的效果。

```
1. - (UIView) hitTest:(CGPoint) point withEvent:(UIEvent) event
2. {
3.     CGRect rect = [self enlargedRect];
4.     if (CGRectEqualToRect(rect, self.bounds))
5.     {
6.         return [super hitTest:point withEvent:event];
7.     }
8.     return CGRectContainsPoint(rect, point) ? self : nil;
9. }
```

​ 总结一下，为什么要用runtime去为已有类达到一个扩展属性的效果呢，正是因为上面这个方法，这个系统提供的方法并没有提供接受其他参数的地方，而我们却必须要指定一个扩大的区域作为参数，所以就必须为这个类扩展一个新的属性。