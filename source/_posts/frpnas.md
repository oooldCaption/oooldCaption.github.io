---
title: 'Frp 内网穿透,把你的 NAS 放到公网上'
tags:
  - NAS
id: '140'
categories:
  - - 实用技术
  - - 巧篆
date: 2018-10-08 10:15:53
---

前段时间觉得自己的硬盘不够用,而且有一些远程访问资源和远程下载任务的需求; 跟女朋友商量一下, 批了资金买了一个群辉 NAS; 因为家里的网络条件没有公网 ip, 光猫拨号上网, 群辉自带的 quickconnect 连接速度太慢了; 趁着国庆在家就研究了一下内网穿透的技术;想通过内网穿透来访问家里的群辉

### 关于内网穿透

本来打算用路由器做内网穿透,然后给 nas 做端口转发, 但是华硕 AC66U-B1刷了梅林之后打游戏老掉包, 于是就在 nas 上做了内网转发; 内网穿透的服务有好几种: eg: \* 花生壳 -----> 死贵 \* nat123 -----> 没用过 \* ngork -----> 刚开始用的这个, 但是找了好几个帖子,提供的客户端的配置文件和应用程序都不能下载了(不想花钱); 放弃了; \* frp -----> 当前使用的 \* 还有一些淘宝上的盒子, 都比较贵 选择 frp 是因为之前购买的 vps 除了 ss 服务没有其他业务运行, 放着也是浪费, 而且测了一下连接速度很快,不得不提我这个 vps速度真的很快, [vultr的vps](https://www.vultr.com/?ref=7205024)加了 bbr 之后在线看4K无压力的 ;
<!-- more -->
### 需要准备的材料

*   一台有独立 IP 的服务器 (例如:[vps](https://www.vultr.com/?ref=7205024), 阿里[ECS](https://promotion.aliyun.com/ntms/yunparter/invite.html?userCode=whtofl6r))
*   一台 NAS 或者需要做内网穿透的客户端
*   frp 文件

### 安装

#### 服务器端配置

1.  登录到服务器,选择适合自己服务器的文件下载, [点击下载](https://github.com/fatedier/frp/releases)
    
    ```
    eg: 我的服务器是 arm64, 系统版本是 linux-centOS
    wget https://github.com/fatedier/frp/releases/download/v0.21.0/frp_0.21.0_linux_arm64.tar.gz
    ```
    
2.  下载后完成解压
    
    ```
    tar -xzf  frp_0.21.0_linux_arm64.tar.gz
    ```
    
3.  进入到服务器 frp 目录,并删除无用文件
    
    ```
    cd frp_0.21.0_linux_arm64
    //frpc 开头的是客户端的配置文件和资源, 服务端可删除
    //frps 开头的是服务端的配置文件和资源, 客户端可删除
    rm -f frpc.ini frpc_full.ini
    ```
    
4.  编辑服务端配置文件
    
    ```
    [common]
    bind_port = 7000
    vhost_http_port = 8080
    dashboard_port = 7500
    dashboard_user = you's name
    dashboard_pwd = you's psw
    max_pool_count = 5
    authentication_timeout = 900
    subdomain_host = lekee.cc
    [ssh]
    listen_port = 6000
    auth_token = you's token,(客户端的保持一致)
    /**
    [common] 必填的
    bind_port Frp 服务端口（可自定义）
    vhost_http_port http 访问端口（可自定义）
    dashboard_port dashboard 界面端口
    dashboard_user 登录 dashboard 用户名
    dashboard_pwd 登录 dashboard 密码
    max_pool_count 最大连接池数量
    authentication_timeout 超时验证时间
    subdomain_host 自定义二级域名
    [ssh]
    listen_port ssh 访问端口
    auth_token 用户身份认证
    */
    ```
    
    如果需要更详细的,可以参考[官方文档](https://github.com/fatedier/frp/blob/master/README_zh.md#%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6)
    
5.  保存配置文件, 然后运行
    
    ```
    ./frps -c ./frps.ini
    也可以通过 screen 或者 nohup 命令进行后台运行
    ```
    

#### 客户端配置

客户端配置与服务端很相似, 区别在于配置文件的不同; frp 客户端支持 windows, Mac, 路由器,和linux 内核设备;群辉 nas 是基于 linux 的, 可以使用 linux 版本的 frp 客户端;

1.  开启 shh 登录,控制面板打开终端机和 SNMP, 勾选启动 ssh ; ![](https://img.52smile.vip/2018-10-08-030049.jpg)
2.  登录到 nas ,下载 frp
    
    ```
    eg:
    ssh xiaoxiaosu@192.168.xxx.xxx
    ssh user@ipAdd
    //下载
    wget https://github.com/fatedier/frp/releases/download/v0.21.0/frp_0.21.0_linux_arm64.tar.gz
    //解压
    tar -xzf  frp_0.21.0_linux_arm64.tar.gz
    //删除无用配置文件
    rm -f frps.ini frps_full.ini
    ```
    
3.  编辑客户端配置文件
    
    ```
    vi frpc.ini
    [common]
    server_addr = xxx.xxx.xxx.xxx(服务器的 ip)
    server_port = 7000
    auth_token = password
    pool_count = 1
    [ssh]
    type = tcp
    local_ip = xxx.xxx.xxx.xxx(NAS 的局域网ip)
    local_port = 22
    remote_port = 6000
    [nas]
    type = http
    local_port = 5000
    subdomain = nas
    ```
    
    > \[common\] 必填的 server\_addr ----->服务器端公网 server\_port ----->frp 服务端口，和服务器端 bind\_port 一致 auth\_token -----> 和前面服务器端 \[ssh\] auth\_token 一致 pool\_count ----->连接池数量 \[ssh\] type -----> 服务类型（tcp、http、https、udp） local\_ip -----> NAS 本地局域网内网 ip local\_port -----> NAS 开启 ssh 服务端口号，默认 22 remote\_port -----> 服务器端 ssh 端口，和服务器端 \[ssh\] listen\_port 配置一致 \[nas\] NAS 管理界面 subdomain -----> 二级域名 nas.52smile.vip 访问 NAS type = http -----> 服务类型为 http local\_port -----> NAS 默认端口 5000 \[web\] NAS web Station 服务，没用可以不用设置 type = http -----> 类型为 http local\_port = 80 -----> NAS web 服务端口 subdomain = web -----> 二级域名 web.52smile.vip tip: 使用自定义二级域名的时候，域名 52smile.vip 要解析到服务器 IP
    
4.  保存配置文件, 然后运行
    
    ```
    ./frpc -c ./frpc.ini
    也可以通过 screen 或者 nohup 命令进行后台运行
    ```
    

![](https://img.52smile.vip/2018-10-08-031701.jpg)

### 到此就完成了;

我测试了一下在线观看视频, 包括下载文件的速度.都是可以的.