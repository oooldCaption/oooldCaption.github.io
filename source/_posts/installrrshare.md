---
title: Nas 安装人人影视
tags:
  - NAS
id: '156'
categories:
  - - 实用技术
  - - 巧篆
date: 2018-11-10 09:04:33
---

喜欢看美剧和电影, 大二以后一直在人人看美剧和电影, 偶尔从射手下载字母来看生肉;好景不长,因为版权问题人人和射手被勒令关停, 我现在还依稀记得当年人人影视和射手字幕网被关停时候的心痛; 买了个 Nas , 发现 Docker 可以安装人人影视客户端, 我说着还得了, 咱赶紧弄一个呀; 网上研究了一下怎么安装, 花了十几分钟装好了; 美美哒,仿佛又回到了看美剧的时候啦; 哈哈哈

### 安装 Docker

*   安装Docker, 在群辉的套件中心安装 Docker, 直接安装就好了

### 安装人人影视

打开 Docker  
![](https://img.52smile.vip/2018-11-10-014820.jpg) 选择注册表, 输入`auska`, 下载安装docker-rrshareweb 下载完成后对容器进行设置, 勾选`使用最高权限执行容器`  
![](https://img.52smile.vip/2018-11-10-014956.jpg)

1.  在 fileStation docker 文件夹中新建子文件夹, eg:`rrshare`
2.  给 人人影视容器 设置卷,点击添加文件夹, 选择刚刚创建的 rrshare; 然后选择一个文件夹用来存放下载后的资源, 我选择的是 video
3.  设置端口, 可以默认, 也可以自定义一个

![](https://52smile.vip/wp-content/uploads/2018/11/15418148858533.jpg)￼ ![](https://img.52smile.vip/2018-11-10-015148.jpg) ![](https://52smile.vip/wp-content/uploads/2018/11/15418149291359.jpg)￼

*   设置环境改变量  
    通过 ssh 登录 Nas, 在终端执行 cat 命令获取UGID 和 PGID, 选择 admin的就可以1204,100  
    ![](https://img.52smile.vip/2018-11-10-015913.jpg) ![](https://52smile.vip/wp-content/uploads/2018/11/15418155867170.jpg)￼
    

到这就完成安装了,不要忘记点击**应用** 然后在浏览器中输入你的群辉 IP, 端口号是刚才设置的端口号; 如果需要解锁, 默认解锁码是123456; tip: 如果不能下载,先停止容器类, 将 P/UGID 设置成 0;