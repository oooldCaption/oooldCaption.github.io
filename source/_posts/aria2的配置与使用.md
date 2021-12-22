---
title: Aria2的配置与使用;
tags: []
id: '61'
categories:
  - - 实用技术
date: 2018-04-17 15:02:30
---

Aria2的配置与使用; 百度网盘现在很多人在用,但是对Mac用户并不太友好.客户端下载文件出现中断,浏览器下载速度太慢.得了,咱们花点时间来配置Aria2,再配上chrome或者Aria2的GUI工具,让您用着放心,下着舒心.早日与大姐姐相见~~
<!-- more -->
Aria2是这么一款支持多种协议的命令行下载工具 可能有人看到命令行这三个字就不想再继续往下走了，不过这个配置起来真的很方便，很简单．而且支持多平台，OS X,Windows,Linux都可以使用; 开始配置吧~ 下载Aria2; 可以在Aria2下载安装程序,如果你的电脑安装的有brew,你只需要执行 brew install aria2 配置 这是别人做好的配置文件,在终端进入到您的当前用户目录下,然后执行 mkdir .aria2 创建一个文件夹 需要使用终端创建,因为以.开头的文件夹被系统预留,然后进入到创建好的文件夹中,执行 touch aria2.conf,来新建一个文件, 执行open aria2.conf,打开这个文件,将下面的代码复制进去,注意请将代码段dir=/Users/xxxx/Downloads中的xxxx换成你自己的用户名; 在返回到 .aria2 目录中执行touch aria2.session创建一个aria2.session文件; 　在终端输入 aria2c,查看是否可以运行;

1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

39

40

41

42

43

44

45

46

47

#用户名

#rpc-user=user

#密码

#rpc-passwd=passwd

#上面的认证方式不建议使用,建议使用下面的token方式

#设置加密的密钥

#rpc-secret=token

#允许rpc

enable-rpc=true

#允许所有来源, web界面跨域权限需要

rpc-allow-origin-all=true

#允许外部访问，false的话只监听本地端口

rpc-listen-all=true

#RPC端口, 仅当默认端口被占用时修改

#rpc-listen-port=6800

#最大同时下载数(任务数), 路由建议值: 3

max-concurrent-downloads=5

#断点续传

continue=true

#同服务器连接数

max-connection-per-server=5

#最小文件分片大小, 下载线程数上限取决于能分出多少片, 对于小文件重要

min-split-size=10M

#单文件最大线程数, 路由建议值: 5

split=10

#下载速度限制

max-overall-download-limit=0

#单文件速度限制

max-download-limit=0

#上传速度限制

max-overall-upload-limit=0

#单文件速度限制

max-upload-limit=0

#断开速度过慢的连接

#lowest-speed-limit=0

#验证用，需要1.16.1之后的release版本

#referer=\*

#文件保存路径, 默认为当前启动位置

dir=/Users/xxxx/Downloads

#文件缓存, 使用内置的文件缓存, 如果你不相信Linux内核文件缓存和磁盘内置缓存时使用, 需要1.16及以上版本

#disk-cache=0

#另一种Linux文件缓存方式, 使用前确保您使用的内核支持此选项, 需要1.15及以上版本(?)

#enable-mmap=true

#文件预分配, 能有效降低文件碎片, 提高磁盘性能. 缺点是预分配时间较长

#所需时间 none lt; falloc ? trunc « prealloc, falloc和trunc需要文件系统和内核支持

file-allocation=prealloc

启动rpc模式 在终端输入aria2c –conf-path=”/Users/xxxxxx/.aria2/aria2.conf” -D，然后Aria2就启动了,但是如果想使用Aria2下载百度网盘的资料,我们还需要下载插件。如果链接不了,请自行寻找梯子或者百度搜索BaiduExporter(百度云下载地址导出工具);迅雷也可以这样使用 安装完成之后再打开百度网盘的话,会发现网页上多出一个「导出下载」按钮，点击它弹出的「ARIA2 RPC」就自动添加到你的下载队列里了，然后利用 WebUI 提供的网页界面管理你的下载任务. BaiduExporter配置: 打开BaiduExporter设置,将User-agent设置为 : netdisk;5.2.7;PC;PC-Windows;6.2.9200;WindowsBaiduYunGuanJia referer 设置为：http://pan.baidu.com/disk/home 使用 打开百度云盘,找到你想要下载的文件,勾选,然后选择上面的导出到 aria 2选项即可. Aria2 JSON-RPC Path 配置如下 http://localhost:6800/jsonrpc