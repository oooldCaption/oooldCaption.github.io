---
title: iOS 分类源码解读
tags:
  - iOS
  - iOS原理
id: '165'
categories:
  - - iOS 开发
date: 2018-12-13 11:27:31
---

### 分类的好处

用分类声明一些私有方法;  
分类一些体积庞大的类文件,eg: AppDelegate, 组织代码更灵活  
把 framework 的私有方法公开

### 分类的特点

*   运行时决议, 在 runtime 添加到类中, 在编辑的时候分类会将分类方法作为一个数组,动态的添加到宿主类中, 如果存在同名的扩展方法, 那么最后一个参与编译的方法生效并覆盖之前的同名方法(**但是还是实际存在的,不过优先级没有分类中的高**), 但是可以通过一些代码决定调用最后一个参与编译的方法还是调用其他同名方法`memcopy(array->lists, addedLists,addedCount *sizeof*array()->lists[0])) 覆盖宿主 方法的原因,`;
*   为系统类添加分类
*   名字相同的分类会引起编译报错

### 分类中可以添加哪些内容

*   可以添加实例方法
*   类方法
*   协议
*   属性 只声明了 set get , 并没有在分类中添加实例变量
*   添加分类的实例变量, 需要通过关联对象进行设置

### 分类的结构体构成

```text
//分类文件
struct category_t{
    //分类名称
    const char *name;
    //分类所属的数组类
    classref_t cls;
    //实例方法列表
    struct method_list_t *instanceMethods;
    //类方法列表
    struct method_list_t *classMethods;
    //所有协议的方法列表
    struct protocol_list_t *protocols;
    //添加的所有属性
    struct protocol_list_t *instanceProperties;
    method_list_t *methodsForMeta(bool isMeta) {
        if(isMeta) return classMethods;
        else return instanceMethods;
    }
    protocol_list_t *propertiesForMeta(bool isMeta) {
        if(isMeta) return nil;
        else return instanceProperties;
    }
}
```

#### 分类的加载调用栈

1.  \_objec\_init  
    入口调用
    
2.  map\_2\_images  
    数据镜像映射之类的吧, 我也不怎么懂
    
3.  map\_images\_nolock  
    数据镜像映射之类的吧, 我也不怎么懂
    
4.  read\_images  
    objec 源代码中, map\_images\_noloc会调用此方法, 将读取到的数据镜像,将解析到的信息(方法和协议)传递给`remethodizeClass`(猜的), 添加到类和类的元类上  
    ![](https://img.52smile.vip/2018-12-13-070041.jpg)
    
5.  remethodizeClass  
    整合class 中的所有分类, 进行操作;  
    此过程会将分类的属性,协议,方法都添加到类中;  
    下面会有详细的说明关于**分类是如何加载的**
    
    ```text
    static void remethodizeClass(Class cls)
    {
        category_list *cats;
        bool isMeta;
        runtimeLock.assertWriting();
        isMeta = cls->isMetaClass();
        // Re-methodizing: check for more categories
        if ((cats = unattachedCategoriesForClass(cls, false/*not realizing*/))) {
            if (PrintConnecting) {
                _objc_inform("CLASS: attaching categories to class '%s' %s", 
                             cls->nameForLogging(), isMeta ? "(meta)" : "");
            }
            attachCategories(cls, cats, true /*flush caches*/);        
            free(cats);
        }
    }
    ```
    

### 分类是如何加载的

![remethodizeClass](https://img.52smile.vip/2018-12-13-072228.jpg) ![](https://img.52smile.vip/2018-12-13-073451.jpg)

### 方法覆盖

我们上面说了一些`category`原理, 如果存在同名方法的话, 宿主类中的方法会被覆盖掉, 那么我们如何调用宿主类中被覆盖的方法呢?  
刚才说了, 并不是被覆盖掉了, 而是优先级的问题, 我们只要顺着方法列表找到最后一个对应名字的方法就可以啦~

```text
Class currentClass = [MyClass class];
MyClass *my = [[MyClass alloc] init];
if (currentClass) {
    unsigned int methodCount;
    Method *methodList = class_copyMethodList(currentClass, &methodCount);
    IMP lastImp = NULL;
    SEL lastSel = NULL;
    for (NSInteger i = 0; i < methodCount; i++) {
        Method method = methodList[i];
        NSString *methodName = [NSString stringWithCString:sel_getName(method_getName(method))
                                        encoding:NSUTF8StringEncoding];
        if ([@"printName" isEqualToString:methodName]) {
            lastImp = method_getImplementation(method);
            lastSel = method_getName(method);
        }
    }
    typedef void (*fn)(id,SEL);
    if (lastImp != NULL) {
        fn f = (fn)lastImp;
        f(my,lastSel);
    }
    free(methodList);
}
```

### 通过关联对象给分类添加成员变量

关联对象由`AssociationsManager`管理并在 `AssociationsHashMap`中存储;  
`AssociationsHashMap` 是一个全局容器, 通过一个映射表来操作到具体不同的类

```text
#import "MyClass.h"
@interface MyClass (Category1)
@property(nonatomic,copy) NSString *name;
@end
#import "MyClass+Category1.h"
#import <objc/runtime.h>
@implementation MyClass (Category1)
+ (void)load
{
    NSLog(@"%@",@"load in Category1");
}
- (void)setName:(NSString *)name
{
    objc_setAssociatedObject(self,
                             "name",
                             name,
                             OBJC_ASSOCIATION_COPY);
}
- (NSString*)name
{
    NSString *nameObject = objc_getAssociatedObject(self, "name");
    return nameObject;
}
@end
```

关联对象实现原理  
![关联对象实现原理](https://img.52smile.vip/2018-12-13-083719.jpg)