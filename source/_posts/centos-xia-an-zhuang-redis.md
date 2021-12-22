---
title: CentOS 下安装 Redis
tags:
  - 我的博客
id: '116'
categories:
  - - 后端开发
  - - 实用技术
date: 2018-09-15 14:51:47
---

### 前言

> 安装Redis需要知道自己需要哪个版本，有针对性的安装，比如如果需要redis GEO这个地理集合的特性，那么redis版本就不能低于3.2版本，由于这个特性是3.2版本才有的。另外需要注意的是，Redis约定次版本号（即第一个小数点后的数字）为偶数的版本是稳定版（如2.8版、3.0版），奇数版本是非稳定版（如2.7版、2.9版），生产环境下一般需要使用稳定版本。

### 下载安装包

```
wget http://download.redis.io/releases/redis-4.0.2.tar.gz
```

### 解压并安装

```
tar xzf redis-4.0.2.tar.gz
cd redis-4.0.2
make
make install
```

> Redis没有其他外部依赖，安装过程很简单。编译后在Redis源代码目录的src文件夹中可以找到若干个可执行程序，安装完后，在/usr/local/bin目录中可以找到刚刚安装的redis可执行文件。 如下:

![redis 安装目录](https://img.52smile.vip/2018-09-15-134934.jpg) 那你也可以通过命令 `redis -server` 命令来执行启动 redis; 启动后界面如下: ![redis 启动界面](https://img.52smile.vip/2018-09-15-135404.jpg)
<!-- more -->
### 配置

1.  配置初始化脚本
    
    > 首先将初始化脚本复制到/etc/init.d 目录中，文件名为 redis\_端口号，其中端口号表示要让Redis监听的端口号，客户端通过该端口连接Redis。然后修改脚本第6行的REDISPORT变量的值为同样的端口号。
    
2.  建立需要使用的文件夹
    
    目录名
    
    配置项
    
    /etc/redis
    
    存放redis 的配置文件
    
    /etc/redis/端口号
    
    存放 redis持久化文件
    

tip: vim 模式下使用 `?` 命令进行搜索, `n`跳转到下一个搜索结果;`N`跳转上一个搜索结果 3.修改配置文件

> 首先将配置文件模板（redis-4.0.2/redis.conf）复制到/etc/redis 目录中，以端口号命名（eg: “6379.conf”），然后按照下表对其中的部分参数进行编辑。

params

value

tip

daemonize

yes

redis 以守护进程模式运行

pidfile

/var/run/redis\_端口号.pid

设置Redis的PID文件位置

port

端口号

被监听的端口号 dir /var/redis/端口号 redis持久化文件

配置完成以后你可以通过命令行启动 redis

```
/etc/init.d/redis_6379 start
/etc/init.d/redis_6379 stop
```

别忘记配置自动启动~ 修改配置文件: `etc/init.d/redis_6379.conf;`

```
vim etc/init.d/redis_6379.conf
在头部的第五行添加
# chkconfig: 2345 90 10
# description: Redis is a persistent key-value database
```

### 正确的停止 redis

> 考虑到 Redis 有可能正在将内存中的数据同步到硬盘中，强行终止 Redis 进程可能会导致数据丢失。正确停止Redis的方式应该是向Redis发送SHUTDOWN命令，方法为：

`redis-cli SHUTDOWN`

> 当Redis收到SHUTDOWN命令后，会先断开所有客户端连接，然后根据配置执行持久化，最后完成退出。Redis可以妥善处理 SIGTERM信号，所以使用 kill Redis 进程的 PID也可以正常结束Redis，效果与发送SHUTDOWN命令一样。

[参考基友Martin的文章](https://www.ioser.net/archives/27)