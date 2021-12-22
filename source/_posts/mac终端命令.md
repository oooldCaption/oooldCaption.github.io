---
title: mac终端命令
tags: []
id: '89'
categories:
  - - 实用技术
date: 2018-04-17 15:18:37
---

*   清除当前输入行 control + u
    
*   删除当前输入命令最后一个单词 control + w
    
*   跳转至行首/行尾 control + a/e
    
*   清除屏幕 control + l or clear
    
*   新建窗口终端标签 cmd + t
    

基本命令 :

*   列出文件 ls 参数 目录名  
    eg:看看驱动目录下有什么:ls /System/Library/Extensions 参数: -w 显示中文， -l 详细信息， -a 包括隐藏文件
    
*   转换目录 cd  
    eg：想到驱动目录下溜达一圈 cd /System/Library/Extensions
    
*   建立新目录 mkdir 目录名 eg： 在驱动目录下建一个备份目录backup mkdir /System/Library/Extensions/backup 在桌面上建一个备份目录 backup mkdir /User/用户名/Desktop/backup
    
*   拷贝文件 cp 参数 源文件 目标文件  
    eg： 将text.txt 拷贝到驱动目录中  
    cp -R /User/用户名/Desktop/text.txt /System/Library/Extensions 参数R表示对目录进行递归操作，kext在图形界面下看起来是个文件，实际上是个文件夹。 把驱动目录下的所有文件备份到桌面backup cp -R /System/Library/Extensions/\* /User/用户名/Desktop/backup
    
*   删除文件 rm 参数 文件  
    eg： 删除驱动的缓存 rm -rf /System/Library/Extensions.kextcache  
    rm -rf /System/Library/Extensions.mkext 参数－rf 表示递归和强制，千万要小心使用，如果执行了 rm -rf / 你的系统就全没了
    
*   移动文件 mv 文件  
    eg： 把AppleHDA.Kext 移到桌面  
    mv /System/Library/Extensions/AppleHDA.kext /User/用户名/Desktop 想把AppleHDA.Kext 移到备份目录中 mv /System/Library/Extensions/AppleHDA.kext /System/Library/Extensions/backup
    
*   文本编辑 nano 文件名  
    eg ： 编辑 Info.plist  
    nano /System/Library/Extensions/Natit.kext/Info.plist vim 文件名 eg: 编辑 Info.plist  
    vim /System/Library/Extensions/Natit.kext/Info.plist
    

目录操作: 命令名 功能描述 使用举例 mkdir 创建一个目录 mkdir dirname rmdir 删除一个目录 rmdir dirname mvdir 移动目录 mv dirname1 dirname2 cd 改变当前目录 cd desktop pwd 显示当前路径 psw ls 显示当前目录内容 ls 文件操作: 命令名 功能描述 使用举例 cat 显示或者链接文件 cat filename od 显示费文本的内容 od -c filename cp 复制 cp path path rm 删除文件 rm test.txt mv 移动文件 mv test.txt touch 创建文件 touch readme.md file 显示文件类型 file filename