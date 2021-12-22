---
title: iOS奇巧淫技
tags: []
id: '85'
categories:
  - - iOS 开发
date: 2018-04-17 15:17:50
---

*   修改UITextField文本输入框中的clearButton小图片 // 获取X按钮 UIButton \*passwordBtn = \[\_passwordField valueForKey:@"\_clearButton"\]; // 修改按钮图片 \[passwordBtn setImage:\[UIImage imageNamed:@"login\_checkbox\_checked"\] forState:UIControlStateNormal\]; UIButton \*accountBtn = \[\_accountField valueForKey:@"\_clearButton"\]; \[accountBtn setImage:\[UIImage imageNamed:@"login\_checkbox\_checked"\] forState:UIControlStateNormal\];
    
*   tableview不想显示没有内容的cell
    

self.tableview.tablefooterview = \[UIView new\];

*   自定义了leftBarbuttonItem左滑返回手势失效了怎么办? self.navigationItem.leftBarButtonItem = \[\[UIBarButtonItem alloc\] initWithImage:img style:UIBarButtonItemStylePlain target:self action:@selector(onBack:)\]; self.navigationController.interactivePopGestureRecognizer.delegate = (id)self;
<!-- more -->
*   ScrollView在viewController终不能滑到顶

self.automaticallyAdjustsScrollViewInsets = NO;

*   写键盘事件让你不爽了

github搜索 IQKeyboardManger

*   怎么在不新建一个cell的情况下调整separaLine的位置?

self.tableview.separatorinset = UIEdgeInsetMake(0,100,0,0)

*   点击self.view 就收起键盘
    *   (void)touchesBegan:(NSSet \*)touches withEvent:(UIEvent \*)event { \[self.view endEditing:YES\]; }
*   滑动的时候隐藏navigationbar?
    

navigationController.hidesBarsonSwipe = YES

*   ViewController 实现tableview那种悬停header

CSStickyHeaderFlowLayout

*   CoreData用来不爽?

MagicalRecord

*   修改UITextField placeholder的文字颜色&位置
    
    *   (void) drawPlaceholderInRect:(CGRect)rect {
    
    \[\[UIColor blueColor\] setFill\]; \[self.placeholder drawInRect:rect withFont:self.font lineBreakMode:UILineBreakModeTailTruncation alignment:self.textAlignment\]; }
    
*   navigationbar弄成透明的而不是带模糊的效果? \[self.navigationBar setBackgroundImage:\[UIImage new\] forBarMetrics:UIBarMetricsDefault\]; self.navigationBar.shadowImage = \[UIImage new\]; self.navigationBar.translucent = YES;
    
*   修改tableview里面cell的小对勾颜色
    

tableview.tintcolor = \[uicolor redcolor\]

*   使用一个pan手势来代替UISwipegesture各个方向
    
    *   (void)pan:(UIPanGestureRecognizer \*)sender {
    
    typedef NS\_ENUM(NSUInteger, UIPanGestureRecognizerDirection) { UIPanGestureRecognizerDirectionUndefined, UIPanGestureRecognizerDirectionUp, UIPanGestureRecognizerDirectionDown, UIPanGestureRecognizerDirectionLeft, UIPanGestureRecognizerDirectionRight }; static UIPanGestureRecognizerDirection direction = UIPanGestureRecognizerDirectionUndefined; switch (sender.state) {
    
    ```null
    case UIGestureRecognizerStateBegan: {
        if (direction == UIPanGestureRecognizerDirectionUndefined) {
            CGPoint velocity = [sender velocityInView:recognizer.view];
            BOOL isVerticalGesture = fabs(velocity.y) > fabs(velocity.x);
            if (isVerticalGesture) {
                if (velocity.y > 0) {
                    direction = UIPanGestureRecognizerDirectionDown;
                } else {
                    direction = UIPanGestureRecognizerDirectionUp;
                }
            }
            else {
                if (velocity.x > 0) {
                    direction = UIPanGestureRecognizerDirectionRight;
                } else {
                    direction = UIPanGestureRecognizerDirectionLeft;
                }
            }
        }
        break;
    }
    case UIGestureRecognizerStateChanged: {
        switch (direction) {
            case UIPanGestureRecognizerDirectionUp: {
                [self handleUpwardsGesture:sender];
                break;
            }
            case UIPanGestureRecognizerDirectionDown: {
                [self handleDownwardsGesture:sender];
                break;
            }
            case UIPanGestureRecognizerDirectionLeft: {
                [self handleLeftGesture:sender];
                break;
            }
            case UIPanGestureRecognizerDirectionRight: {
                [self handleRightGesture:sender];
                break;
            }
            default: {
                break;
            }
        }
        break;
    }
    case UIGestureRecognizerStateEnded: {
        direction = UIPanGestureRecognizerDirectionUndefined;
        break;
    }
    default:
        break;
    ```
    
    } }
    
*   图片拉伸
    

在Storyboard或xib中拖入的控件都有Stretching属性，包含四个参数：X、Y、Width、Height。这些参数确定了进行拉伸的区域。 当X = Y = Width = Height = 1时，图片为原始大小。 若让X = Y = 0.5、Width = Height = 0，则按水平垂直位置的那个像素点进行拉伸，如图所示。

*   修改UISearchBar 的占位符文字颜色 /**设置UISearchBar**/ // 创建 UISearchBar \*searchBar = \[\[UISearchBar alloc\] init\]; // 设置导航条背景色 self.navigationController.navigationBar.barTintColor = \[UIColor hollyGreenColor\]; // 风格 searchBar.searchBarStyle = UISearchBarStyleMinimal; // 光标色 searchBar.tintColor = \[UIColor whiteColor\]; // 占位文字 searchBar.placeholder = @"搜索"; // 设置占位文字色彩 UITextField \*searchField = \[searchBar valueForKey:@"searchField"\];
    
    \[searchField setValue:\[UIColor whiteColor\] forKeyPath:@"\_placeholderLabel.textColor"\];