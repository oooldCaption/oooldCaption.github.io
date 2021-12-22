---
title: 成吨节约您时间的效率神器--Alfred
tags:
  - 效率
id: '135'
categories:
  - - 实用技术
  - - 巧篆
date: 2018-09-18 19:47:24
---

Alfred, 我叫它小红帽; 是 Mac 平台上一个能够大幅提升生产力的效率工具,通过强大的workflow 机制,能够帮助我们快速打开网页、快速进行自定义搜索、查看剪贴板历史、快速查询单词等一些基础功能,通过自己去编辑脚本,可以实现更多的功能,解决输入输出的痛点，极大的减少了重复的操作,减少不同程序之间的切换步骤,成吨的节约时间。

![alfred](https://img.52smile.vip/2018-09-18-114716.jpg)

先简单介绍一下常见的使用场景: 平常我们搜索关键词需要打开浏览器,然后输入关键词;执行搜索,但是用小红帽,配置好以后输入想要搜索关键词,然后按 enter 就好啦~

![search](https://img.52smile.vip/2018-09-18-2018-09-18%2020.06.41.gif)

有时候突然忘记一个单词的中文意思是什么，先移动下鼠标，打开词典工具，然后输入单词,但是用小红帽,我们通过一个 关键词唤起,然后输入想要查询的单词, enter 就出来了; 还可以通过 shift 键来预览其他释义;

![translate](https://img.52smile.vip/2018-09-18-2018-09-18%2020.08.44.gif)

当然这只是一丢丢的功能;据统计大约扩展出来的 workflow 至少有500+,有热心网友列了一个[表格](http://www.alfredworkflow.com/)来管理; 你可以下载并免费使用其中任何一个 Workflow，甚至还可以基于一些不错的 Workflow 加入自己的想法，改造成属于自己的,符合自己工作习惯的 Workflow（前提是已解锁 Powerpack ）。
<!-- more -->
### 如何安装

首先可以从[Alfred 官网](https://www.alfredapp.com/)自行下载安装，免费用户可以使用除 Workflow 以外的其它功能，如需使用 Workflow，则需要购Powerpack。推荐大家使用正版 列出常用的一些功能:

1.  应用搜索 输入关键词,小红帽自动罗列包含关键词的 application; 搜索结果列出后按住 command 键,会出现不同的功能 ![](https://img.52smile.vip/2018-09-18-123522.jpg)
2.  文件&目录搜索 输入空格或者 find开启命令,然后输入想要搜索的内容,快速定位到搜索文件;输入 enter 可直接打开文件或者文件夹; 搜索结果列出后按住 command 键,会出现不同的功能 ![](https://img.52smile.vip/2018-09-18-123722.jpg)
3.  文本搜索 输入 in 命令，以及待搜索的文本，列出磁盘中包含该文本的相关文件，可以快速定位文件，相当于简易的终端 find 命令。 ![](https://img.52smile.vip/2018-09-18-124113.jpg)![-w601](http://52smile.vip/wp-content/uploads/2018/09/15372745411544.jpg)￼
4.  计算器 可以计算简单的,复杂的运算 ![](https://img.52smile.vip/2018-09-18-124245.jpg)![](https://img.52smile.vip/2018-09-18-124423.jpg)
5.  词典搜索 强烈推荐使用有道词典的 [workflow](https://github.com/wensonsmith/YoudaoTranslate); ![](https://img.52smile.vip/2018-09-18-124524.jpg) 可以自己去申请有道官方的秘钥,替换使用;
6.  剪切板管理
    
    *   在小红帽偏好设置(command+,)中打开 features,点开 Clipboard, 可以看到如下的图片; viewerKeyboard 是命令快捷键,在小红帽中输入可快速唤起建起版管理,也可以自定义唤起命令; clearLeyword 是清除剪切板记录,同上,也可以自定义命令关键词;
    *   这样能极大地提升工作效率,避免在两个应用中来回切换的操作;
    *   不过我个人很少使用小红帽附带的剪切板,一直都在使用 [paste](https://pasteapp.me/),颜值高,谁用谁知道; ![](https://img.52smile.vip/2018-09-18-124836.jpg)![](https://img.52smile.vip/2018-09-18-124828.jpg)
7.  快递单号查询 输入快递单号: biubiubiu~ ![](https://img.52smile.vip/2018-09-18-125700.jpg)
8.  ip查询 输入 `ip` ,直接显示; 再也不用 `ifconf` 了 ![](https://img.52smile.vip/2018-09-18-125758.jpg)
    
9.  shell 命令 输入 `>`,然后可以直接输入 `shell`命令; ![](https://img.52smile.vip/2018-09-18-125919.jpg)![](https://img.52smile.vip/2018-09-18-130016.jpg) 有些人可能不喜欢自带的终端,如果是使用`item2`; 可以通过自定的的脚本去切换默认的终端
    
    ```
    on alfred_script(q)
    tell application "iTerm"
        set _length to count window
    if _length = 0 then
        create window with default profile
    end if
    set aa to (get miniaturized of current window)
    if aa then
        set miniaturized of current window to false
    end if
    set bb to (get visible of current window)
    if bb is false then
        set visible of current window to true
    end if
    set cc to frontmost
    if cc is false then
        activate
    end if
        (*if _length = 0 then*)
            set theResult to current tab of current window
        (*else
            set theResult to (create tab with default profile) of current window
        end if*)
        write session of theResult text q
    end tell
    end alfred_script
    ```
    
10.  系统命令 ![](https://img.52smile.vip/2018-09-18-130201.jpg) 常见命令如下: ![](https://img.52smile.vip/2018-09-18-130246.jpg)

#END 这些常用的一些快捷操作会大大提升我们平时工作的效率; 前人栽树,后人乘凉;这次介绍的大多都是别人写好的 workflow, 但是对于大多数人来说，能做出自己的一个 WorkFlow 才是一件令人激动的事情。其实做出简单的 WorkFlow 是没啥问题,但如果你想实现一些复杂点的功能，那么你可能需要懂一门脚本语言。 今天简单的介绍一下简单又常用的操作, 下次我打算写一个从零开始打造一个 workflow, 自定义一个属于你自己的 workflow;