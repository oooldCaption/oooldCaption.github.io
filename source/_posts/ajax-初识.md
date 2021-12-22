---
title: Ajax 初识
tags: []
id: '59'
categories:
  - - 后端开发
date: 2018-04-17 15:01:46
---

Ajax AJAX即“Asynchronous Javascript And XML”（异步JavaScript和XML），是指一种创建交互式网页应用的网页开发技术。并不是一种开发语言,是聪明的程序员对 javascript 的创造性用法; Ajax 提供与服务器异步通信的能力,最简单的应用就是无需刷新整个页面而更新网页某一个部分的数据,更快,更高,更强~~~ 使用这个技术还是很简单的.
<!-- more -->
1.创建 XMLHttpRequest 对象

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

//创建对象的语法:

variable = new XMLHttpRequest()lt;br/gt;

//如果需要向下兼容 ie5,5,我们需要 ActiveX 对象lt;br/gt;

variable = new ActiveXobject(quot;Microsoft.XMLHTTPquot;)lt;br/gt;

var xmlhttp;

if (window.XMLHttpRequest){

xmlhttp=new XMLHttpRequest();

}else{

xmlhttp=new ActiveXObject(quot;Microsoft.XMLHTTPquot;);

}

这样我们就创建一个 XMLHttpRequest 的对象,我们就可以使用这个对象进行请求数据; ２.向服务器发送请求 如果我们向服务器发送请求，就需要用到xmlhttp 的 open()和send() 方法

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

!--

open()有三个参数,第一个参数是请求类型 put,get,post;

第二个参数是 url,即文件在服务器上的地址

第三个参数是同步 or 异步

\--

open(method,url,asyns)

!--

发送请求,string 用于 post 请求

但我们需要提交一些表单之类的数据的时候,需要用到 post 提交

需要在 setRequestHeader()中添加请求有信息,在后再 send()中添加数据

\--

send(string)

xmlhttp.onreadystatechange=function()

{

if (xmlhttp.readyStatelt;markgt;4 amp;amp; xmlhttp.statuslt;/markgt;200)

{

// responseText amp; responseXML;一个是字符串形式的响应数据,一个是 xml 形式的响应数据XML 可以使用 name.getElementsByName(quot;节点名称quot;)进行读取

document.getElementById(quot;myDivquot;).innerHTML=xmlhttp.responseText;

}

}

xmlhttp.open(quot;GETquot;,url,true);

xmlhttp.send();

3

上面的 readyState表示存有 XMLHttpRequest 的状态

*   0:请求未初始化
*   1:服务器连接已建立
*   2:请求已经接受
*   3:请求处理中
*   4:请求已完成,响应就绪; 上面的 status
*   200:”OK
*   404:未找到页面