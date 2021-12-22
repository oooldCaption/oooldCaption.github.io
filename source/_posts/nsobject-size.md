---
title: 从 NSObject 占用的内存说起
tags:
  - iOS
  - iOS原理
id: '220'
categories:
  - - iOS 开发
date: 2019-03-30 13:38:49
---

之前iOS的一个 git 仓库看到一个面试问题, 一个 NSObject 对象占用多少内存, 看到这个面试题以后我想不是4个就是8个嘛, 因为之前我打印过 64位设备下 NSString 对象的内存大小就是8; 可是答案却有一点出乎意料, 是16, 于是我就找了些资料进行了一下深入的研究, 果然.....
<!-- more -->
我们都知道, Objective-C 底层是使用 C和C++ , oc 的对象基本就是 C/C++的结构体;我们将 oc 的 m 文件使用 clang 编译后输出 cpp, 代码会告诉你对象的数据结构是类似如下所示的一个结构体:  
使用命令行将 `.m` 转成 `cpp`文件:

```
命令行
xcrun  -sdk  iphoneos  clang  -arch  arm64  -rewrite-objc  OC源文件  -o  输出的CPP文件
eg:
xcrun -sdk iphoneos clang -arch arm64 -rewrite-objc main.m -o main.cpp
struct NSObject_IMPL {
    Class isa;
};
```

![](https://img.52smile.vip/2019-04-11-033022.jpg)

当然我们也可以跳转到 NSObject 的头文件看到类似的代码:

```
@interface NSObject <NSObject> {
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wobjc-interface-ivars"
    Class isa  OBJC_ISA_AVAILABILITY;
#pragma clang diagnostic pop
}
...
@end
```

然后我们可以点击跳转到 class 的定义去看一下:

```
typedef struct objc_class *Class;
struct objc_object {
    Class _Nonnull isa __attribute__((deprecated));
};
```

我们发现, Class 其实是一个指针, 我们使用`NSObject *obj = [[NSObject alloc]init];` 去初始化了一个 obj对象, 那么 obj 的指针就指向了刚刚分配给 class 指针的内存地址;

我们可以使用一个方法来打印一下这个对象的大小:

```
 NSObject *obj = [[NSObject alloc]init];
//  获得NSObject类的实例对象的大小
NSLog(@"%zd", class_getInstanceSize([NSObject class]));
```

打印结果就8, 但为什么会有人说对象的大小是16呢. 我们可以使用另外一个 api 来进行访问,再次打印一下这个对象的 size,

```
//获取obj指针指向内存的大小
 NSObject *obj = [[NSObject alloc]init];
 NSLog(@"%zd", malloc_size((__bridge const void *)obj));
```

上面方法返回的都是关于对象的大小, 为什么一个是 8 一个是 16 呢...

我们可以在 iOS 的 [obj4源码](https://opensource.apple.com/tarballs/objc4/) 中找到答案

```
id
_objc_rootAllocWithZone(Class cls, malloc_zone_t *zone)
{
    id obj;
#if __OBJC2__
    // allocWithZone under __OBJC2__ ignores the zone parameter
    (void)zone;
    obj = class_createInstance(cls, 0);
#else
    if (!zone) {
    //不存在内存空间的话 去开辟一快新的内存
        obj = class_createInstance(cls, 0);
    }
    else {
        obj = class_createInstanceFromZone(cls, 0, zone);
    }
#endif
    if (slowpath(!obj)) obj = callBadAllocHandler(cls);
    return obj;
}
id
class_createInstance(Class cls, size_t extraBytes){
    return _class_createInstanceFromZone(cls, extraBytes, nil);
}
id
_class_createInstanceFromZone(Class cls, size_t extraBytes, void *zone,
                              bool cxxConstruct = true,
                              size_t *outAllocatedSize = nil){
    if (!cls) return nil;
    assert(cls->isRealized());
    ...
    size_t size = cls->instanceSize(extraBytes);
    if (outAllocatedSize) *outAllocatedSize = size;
    ...
    return obj;
}
size_t instanceSize(size_t extraBytes) {
    size_t size = alignedInstanceSize() + extraBytes;
    // CF requires all objects be at least 16 bytes.
    if (size < 16) size = 16;
    return size;
}
```

我们可以通过 AllocWithZone 进行查找, 确定这个方法就是 alloc 时候调用的; 然后看到在这个方法中, 通过 `class_createInstance()`进行创建; 我们可以用同样的方法进行查找关于`class_createInstance`的信息, 可以看到在最后一个函数`size_t instanceSize`中有一段关于限制 `size`的代码, `if (size < 16) size = 16;`

这样就解决了我们上面关于为什么 nsobject 对象是16 的的疑问;

那么假如我们自定义一个继承自NSObject的 `TestTempClass`，然后给这个类添加几个int类型的成员变量呢？

这里就涉及到一个内存对齐的法则：

1.  数据成员对齐规则：struct 或 union （以下统称结构体）的数据成员，第一个数据成员A放在偏移为 0 的地方，以后每个数据成员B的偏移为(#pragma pack(指定的数n) 与 该数据成员（也就是 B）的自身长度中较小那个数的整数倍，不够整数倍的补齐。

1.  数据成员为结构体：如果结构体的数据成员还为结构体，则该数据成员的“自身长度”为其内部最大元素的大小。(struct a 里存有 struct b，b 里有char,int,double等元素，那 b “自身长度”为 8)
2.  结构体的整体对齐规则：在数据成员按照上述第一步完成各自对齐之后，结构体本身也要进行对齐。对齐会将结构体的大小调整为(#pragma pack(指定的数n) 与 结构体中的最大长度的数据成员中较小那个的整数倍，不够的补齐。

Xcode 中默认为#pragma pack(8）， 所以我们可以根据这个法则对对象占用的内存进行计算；