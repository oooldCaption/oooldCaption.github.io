---
title: UI事件传递以及事件响应原理
tags:
  - iOS
  - iOS原理
id: '163'
categories:
  - - iOS 开发
date: 2018-12-11 20:47:22
---

CALayer -> content显示内容, 实际是合成了一个个位图; 用来展示 我们平常所说的掉帧也是因为位图合成后未来得及显示绘制造成的 view 提供内容, 负责处理触摸事件,参与视图响应链 layer, 负责内容上的显示, contents; 之所以这样设计是因为单一元件负责单元任务, 即单一原则;

### 事件传递:

```
//返回响应事件的视图
-(UIView *)hitTest:(CGPoint)point WithEvent:(UIEvent *)event;
//判断是否在响应区域内
-(BOOL)pointInside(CGPoint)point WithEvent:(UIEvent *)event;
```

#### 事件传递流程:

如何寻找合适的响应者来处理事件: 1. 判断主窗口是否可以接受触摸事件 2. 判断触摸点是否在自己的区域内( pointInside: withEvent:) 3. 子控件数组中从后往前遍历子控件，重复前面的两个步骤（所谓从后往前遍历子控件，就是首先查找子控件数组中最后一个元素，然后执行1、2步骤） 事件传递给一个控件就会调用`hitTest: withEvent:`, 通过这个特性, 可以重写`hitTest: withEvent`方法返回指定的 view 用来接收事件的处理; ![](https://img.52smile.vip/2018-12-12-070429.jpg) eg: ![](https://img.52smile.vip/2018-12-12-082050.jpg) 在实际中响应事件是的`viewB1`,虽然点击的位置在 `viewA2` 和 `ViewB1`的重复处, 但是由于 `view B1` 的视图索引在 `viewA2` 之上, 所以响应事件的是 \`\`viewB1\`;
<!-- more -->
### hitTest:withEvent: 系统实现

![hitTest:withEvent: 系统实现](https://img.52smile.vip/2018-12-12-083719.jpg)

```
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {
    if (!self.isUserInteractionEnabled  self.isHidden  self.alpha <= 0.01) {
        return nil;
    }
    if ([self pointInside:point withEvent:event]) {
        for (UIView *subview in [self.subviews reverseObjectEnumerator]) {
            CGPoint convertedPoint = [subview convertPoint:point fromView:self];
            UIView *hitTestView = [subview hitTest:convertedPoint withEvent:event];
            if (hitTestView) {
                return hitTestView;
            }
        }
        return self;
    }
    return nil;
}
```

`hitTest:withEvent:` 首先检测是否允许视图接受触摸事件, 如果出现下面的情况, 不允许被触摸:

*   视图被隐藏 `self.hidden == no`
*   未启用用户交互 `self.userInteractionEnabled == NO`
*   视图的透明度小于0.001 `self.alpha < 0.01`
*   视图的 pointInside:withEvent 返回 NO `pointInside:withEvent: == NO`

通过重写`hitTest:withEvent:`方法, 我们可以给某些控件增大可点击范围, 或者让某个控件的指定区域响应点击事件;

#### eg : 修改触摸范围, 向上偏移10个点

```
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {
    if (!self.isUserInteractionEnabled  self.isHidden  self.alpha <= 0.01) {
        return nil;
    }
    CGRect touchRect = CGRectInset(self.bounds, -10, -10);
    if (CGRectContainsPoint(touchRect, point)) {
        for (UIView *subview in [self.subviews reverseObjectEnumerator]) {
            CGPoint convertedPoint = [subview convertPoint:point fromView:self];
            UIView *hitTestView = [subview hitTest:convertedPoint withEvent:event];
            if (hitTestView) {
                return hitTestView;
            }
        }
        return self;
    }
    return nil;
}
```

#### eg : 定义一个方形的 button, 只允许圆形区域内才可以接受响应

![](https://img.52smile.vip/2018-12-12-090217.jpg)

```
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event
{
    if (!self.userInteractionEnabled 
        [self isHidden] 
        self.alpha <= 0.01) {
        return nil;
    }
    if ([self pointInside:point withEvent:event]) {
        //遍历当前对象的子视图
        __block UIView *hit = nil;
        [self.subviews enumerateObjectsWithOptions:NSEnumerationReverse usingBlock:^(__kindof UIView * _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
            // 坐标转换
            CGPoint vonvertPoint = [self convertPoint:point toView:obj];
            //调用子视图的hittest方法
            hit = [obj hitTest:vonvertPoint withEvent:event];
            // 如果找到了接受事件的对象，则停止遍历
            if (hit) {
                *stop = YES;
            }
        }];
        if (hit) {
            return hit;
        }
        else{
            return self;
        }
    }
    else{
        return nil;
    }
}
- (BOOL)pointInside:(CGPoint)point withEvent:(UIEvent *)event
{
    CGFloat x1 = point.x;
    CGFloat y1 = point.y;
    CGFloat x2 = self.frame.size.width / 2;
    CGFloat y2 = self.frame.size.height / 2;
    double dis = sqrt((x1 - x2) * (x1 - x2) + (y1 - y2) * (y1 - y2));
    // 67.923
    if (dis <= self.frame.size.width / 2) {
        return YES;
    }
    else{
        return NO;
    }
}
```

#### eg : 将触摸事件传递给下一层的视图

有时候我们需要忽略当前视图的触摸事件, 让他下一级的视图去响应触摸; 我们可以覆盖此方法, 返回其中一个包含触摸点的子视图;

```
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {
    UIView *hitTestView = [super hitTest:point withEvent:event];
    if (hitTestView == self) {
        hitTestView = nil;
    }
    return hitTestView;
}
```

#### eg : 将触摸事件传递给子视图

假设一个scrollview 构成的图像轮播, pagungEnable=YES, clipsToBounds 设置为 NO , scrollveiw 的响应不仅在自己的边界内, 而且在父视图的边界也要可以响应

```
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {
    UIView *hitTestView = [super hitTest:point withEvent:event];
    if (hitTestView) {
        hitTestView = self.scrollView;
    }
    return hitTestView;
}
```