---
title: iOS设计模式之单例
tags: []
id: '24'
categories:
  - - iOS 开发
date: 2018-04-17 14:45:14
---

单例模式的意思就是这个类只有一个实例，这个类就是单例类。在iOS中有不少都是单例NSNull，NSFileManager ，UIApplication，NSUserDefaults ，UIDevice，还有一些第三方也有能用到了这种设计模式例如Afhttpmanger。。。
<!-- more -->
（1）单例模式的作用 :可以保证在程序运行过程，一个类只有一个实例，而且该实例易于供外界访问，从而方便地控制了实例个数，并节约系统资源。 （2）单例模式的使用场合：在整个应用程序中，共享一份资源（这份资源只需要创建初始化1次），应该让这个类创建出来的对象永远只有一个。 实现思路： 创建一个一个全局的static的实例 static id \_instance; 提供1个类方法让外界访问唯一的实例 重写allocWithzone:方法，控制内存分配。因为alloc内部会调用该方法，每次调用allocWithzone:方法，系统都会创建一块新的内存空间。

```
> <span class="s1">实现copyWithZone:方法</span>
>
> <span class="s1">//</span>
>
> <span class="s1">// AudioPlayer.m</span>
>
> <span class="s1">// 单例</span>
>
> <span class="s1">//</span>
>
> <span class="s1">// Created by 两好三坏 on 16/2/21.</span>
>
> <span class="s1">// Copyright © 2016年 ;. All rights reserved.</span>
>
> <span class="s1">//</span>
>
> <span class="s1">#import &amp;quot;AudioPlayer.h&amp;quot;</span>
>
> <span class="s2">@interface</span> <span class="s1">AudioPlayer</span><span class="s3"> ()</span>
>
> <span class="s1">@end</span>
>
> <span class="s1">@implementation</span> <span class="s4">AudioPlayer</span>
>
> <span class="s1">//创建一个一个全局的static的实例 static id _instance;</span>
>
> <span class="s2">static</span> <span class="s2">id</span><span class="s1"> _instance;</span>
>
> <span class="s1">//提供1个类方法让外界访问唯一的实例</span>
>
> <span class="s1">+(instancetype)shareAudioPlayer{</span>
>
> <span class="s2">static</span> <span class="s1">dispatch_once_t</span><span class="s3"> onceToken;</span>
>
> <span class="s5">dispatch_once</span><span class="s1">(&amp;amp;onceToken, ^{</span>
>
> <span class="s1">_instance = [[</span><span class="s2">self</span><span class="s1"> alloc] init];</span>
>
> <span class="s1">});</span>
>
> <span class="s2">return</span><span class="s1"> _instance;</span>
>
> <span class="s1">}</span>
>
> <span class="s1">//重写allocWithzone:方法，控制内存分配。因为alloc内部会调用该方法，每次调用allocWithzone:方法，系统都会创建一块新的内存空间。</span>
>
> <span class="s1">+(instancetype)allocWithZone:(</span><span class="s2">struct</span><span class="s1"> _</span><span class="s5">NSZone</span><span class="s1"> *)zone{</span>
>
> <span class="s2">static</span> <span class="s1">dispatch_once_t</span><span class="s3"> onceToken;</span>
>
> <span class="s5">dispatch_once</span><span class="s1">(&amp;amp;onceToken, ^{</span>
>
> <span class="s1">_instance = [</span><span class="s2">super</span><span class="s1"> allocWithZone:zone];</span>
>
> <span class="s1">});</span>
>
> <span class="s2">return</span><span class="s1"> _instance;</span>
>
> <span class="s1">}</span>
>
> <span class="s1">//实现copyWithZone:方法</span>
>
> <span class="s1">-(</span><span class="s2">id</span><span class="s1">)copyWithZone:(</span><span class="s5">NSZone</span><span class="s1"> *)zone{</span>
>
> <span class="s2">return</span><span class="s1"> _instance;</span>
>
> <span class="s1">}</span>
>
> <span class="s1">@end</span>
>
> <span class="s1">在控制其中创建单例类的对象，打印地址：</span>
>
> <span class="s1">- (</span><span class="s2">void</span><span class="s1">)viewDidLoad {</span>
>
> <span class="s1">[</span><span class="s2">super</span><span class="s1"> viewDidLoad];</span>
>
> <span class="s1">AudioPlayer *player1 = [AudioPlayer shareAudioPlayer];</span>
>
> <span class="s1">AudioPlayer *player2 = [[AudioPlayer alloc] init];</span>
>
> <span class="s1">AudioPlayer *player3 = [AudioPlayer new];</span>
>
> <span class="s1">AudioPlayer *player4 = [player1 </span><span class="s2">copy</span><span class="s1">];</span>
>
> <span class="s5">NSLog</span><span class="s1">(@&amp;quot;%p,%p,%p,%p&amp;quot;,player1,player2,player3,player4);</span>
>
> <span class="s1">}</span>
>
> <span class="s1">//打印结果</span>
>
> <span class="s1">2016</span><span class="s3">-</span><span class="s1">02</span><span class="s3">-</span><span class="s1">21</span> <span class="s1">23</span><span class="s3">:</span><span class="s1">27</span><span class="s3">:</span><span class="s1">13.990</span><span class="s3"> 单例[</span><span class="s1">2847</span><span class="s3">:</span><span class="s1">329685</span><span class="s3">] </span><span class="s1">0x7fb6e3e080a0</span><span class="s3">,</span><span class="s1">0x7fb6e3e080a0</span><span class="s3">,</span><span class="s1">0x7fb6e3e080a0</span><span class="s3">,</span><span class="s1">0x7fb6e3e080a0</span>
>
> <span class="s1">四个实例的内存地址是一样的，证明只创建了一个实例；</span>
>
> <span class="s1">MRC环境下通常需要在实现下面几个方法：</span>
>
> <span class="s1">- (</span><span class="s2">oneway</span> <span class="s2">void</span><span class="s1">)release {}</span>
>
> <span class="s1">- (</span><span class="s2">id</span><span class="s1">)retain { </span><span class="s2">return</span><span class="s1"> _instance; }</span>
>
> <span class="s1">- (</span><span class="s2">id</span><span class="s1">)autorelease { </span><span class="s2">return</span><span class="s1"> _instance; }</span>
>
> <span class="s1">- (</span><span class="s5">NSUInteger</span><span class="s1">)retainCount { </span><span class="s2">return</span> <span class="s5">UINT_MAX</span><span class="s1">; }</span>
>
> <span class="s1">[/objc]</span>
>
> <span class="s1">可以使用宏判断是否为mrc，</span>
>
> <span class="s1">#if __has_feature(objc_arc)</span>
>
> <span class="s1">// ARC</span>
>
> <span class="s1">#else</span>
>
> <span class="s1">// MRC</span>
>
> <span class="s1">#endif</span>
>
> &nbsp;
<span class="s1">当然还可以把单例也抽取成为宏，用起来还挺方便的；你们也可以试试试～～～</span>
```