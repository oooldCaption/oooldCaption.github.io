---
title: 给你的Mac搭建开发环境
tags:
  - 效率
id: '151'
categories:
  - - 实用技术
  - - 巧篆
date: 2018-10-21 20:40:18
---

本文主要讲述 Mac 一些常用配置,优秀的软件,常见实用技巧

# 系统设置

*   启用触摸板右键;系统设置 -> 触摸板 ->光标与点按 -> 辅助点按; 可以选择轻点右下角或者双指头触摸
*   启用触摸板三指拖移; 系统设置 -> 辅助功能 -> 鼠标与触控板 -> 触控板设定
*   苹果的 F1 ~ F12是常用的影音娱乐功能, 对于码农来讲,会更习惯将 F1~F12 设定为功能按键, 你可以打开你的终端 输入以下代码 `defaults write -globalDomain com.apple.keyboard.fnState -int 1` 将 F1~F12设定为IDE功能按钮
*   我们在安装一些破解软件的时候, 有时候会提示**无法打开已损坏的安装包**; 命令行执行`sudo spctl --master-disable` ,关闭验证
*   Dock 栏优化, 移除不常用的 app, 打开自动隐藏 dock 快捷键 `command + option +D`
*   设置触发角,出发常用的锁屏,开启屏保等功能; 系统设置->桌面与屏幕保护程序->桌面保护程序->触发角; 设定后光标移动到屏幕四个角,会触发不同的操作;

# 扩展优化

mac 系统有一个很强大的预览工具; 选中文件后按空格可以预览选中文件,支持很多格式,但是如果我们想获取**更好的阅读体验**和**更多的文件支持类型**,我们就需要安装一些扩展; 在安装扩展之前需要安装一个超级强大的 brew , brew 是 mac 系统上的一个包管理器,就像 linux 上的 yum 和 apt-get ;

```
<!--在终端执行下面这个语句进行安装-->
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

安装完成之后在终端输入 `brew -h` , 可以查看 brew 的帮助文档; brew 安装完成以后就可以通过 brew 安装一些常用的插件和软件包; 安装上面说到的预览扩张工具,

*   qlcolorcode ,这是一个代码高亮的插件，效果如图所示：

```
brew cask install qlcolorcode
```

![qlcolorcode](https://img.52smile.vip/2018-10-21-131556.jpg)

*   qlmarkdown, 这个插件能让我们预览 markdown 文件的渲染效果:

```
brew cask install qlmarkdown
```

![qlstephen](https://img.52smile.vip/2018-10-21-131813.jpg)
<!-- more -->
\* qlstephen, 这个插件能让我们预览没有后缀的文本文件，效果如图所示：

```
brew cask install qlstephen
```

![brew cask install qlstephen](https://img.52smile.vip/2018-10-21-131944.jpg) \* quicklook-json, 这个插件也很容易理解，提供对 JSON 文件的格式化和高亮支持：

```
brew cask install quicklook-json
```

![![](https://img.52smile.vip/2018-10-21-132017.jpg)](https://img.52smile.vip/2018-10-21-132017.jpg) 以上是和程序员相关的常用插件，还有一些设计师可能用到的插件，可以点击去[quick-look-plugins](https://github.com/sindresorhus/quick-look-plugins)查看更多可用扩展;

# 终端替换

上面提到了一个个工具叫做终端, apple 提供的终端工具并不是那么好用, [iTerm2](https://www.iterm2.com/)是Apple终端的开源替代品。它具有高度可定制性，并具有许多有用的功能。 你可以使用 `brew cask install iterm2`,来安装,也可以在官网下载; 它是开源免费的; iterm 有一个很好用的功能, 他可以悬停到最上层; ![](https://img.52smile.vip/2018-10-21-135026.jpg) 打开iTerm设置 ![](https://img.52smile.vip/2018-10-21-134507.jpg) 配置快捷键 ![](https://img.52smile.vip/2018-10-21-134623.jpg)