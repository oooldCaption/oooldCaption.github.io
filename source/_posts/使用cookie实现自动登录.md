---
title: 使用cookie实现自动登录
tags: []
id: '30'
categories:
  - - 后端开发
date: 2018-04-17 14:48:57
---

关于 cookie 在日常开发中我们会遇到这样的需求场景: 用户在首次登录使用后,离开了这个网站(没有点击注销登录), 再次回到这个站点的时候,不需要重新登录,仍是自己上次登陆的账号信息; 我们可以使用 cookie 中存储用户的信息,并且判断是否需要加载用户信息并重新登录; php 使用 setCookie 函数来设置 Cookie 信息,但是有一点值得注意,Cookie是HTTP协议头的一部分，用于浏览器和服务器之间传递信息，
<!-- more -->
所以必须在任何属于HTML文件本身的内容输出之前调用Cookie函数。 也就是说,在 setcookie 之前不能进行输出:

```
echo '我要设置 cookie了';
setCookie('value','key',time());
```

这种会造成设置 cookie 无效; 使用 cookie 实现自动登录 登陆成功后存储用户的信息: 在登录成功后通过 setCookie 设置用户信息,

```
 public function login()
 {
    if (!empty($_POST)){
        $userpsw = array(
            'username'=>$_POST['username'],
            'password'=>$_POST['password'],
         );
        $info = D('userinfo')->where($userpsw)->find();
        if ($info){
            $re = setcookie('userinfo',md5($_POST['username']),time()+3600,'/');
            $re = setcookie('password',MD5($_POST['password']),time()+3600,'/');
            dump($re);
            echo '登陆成功';
            $this->display();
            }else{
                echo '登录失败';
            }
        }
    }else{
        $this->display();
    }
}
```

通过 cookie 我们还可以保存用户的偏好设置信息,在用户下次访问的时候推送用户可能会感兴趣的信息; 判断是否需要自动登录 可以再模板文件中通过 js 获取 cookie 进行判断是否需要自动加载用户信息,也可以在 php 的控制其中进行判断,

```
<script type="text/javascript">
$(document).ready(function () {
var name = getCookie('nickname');
var password = getCookie('psw');
if(name && password){
    //自动登录
}else{
    //手动登录
}
   console.log(getCookie('nickname'));
});
function getCookie(c_name)
{
    if (document.cookie.length>0)
    {
        c_start=document.cookie.indexOf(c_name + "=")
        if (c_start!=-1)
        {
            c_start=c_start + c_name.length+1
            c_end=document.cookie.indexOf(";",c_start)
            if (c_end==-1) c_end=document.cookie.length
            return unescape(document.cookie.substring(c_start,c_end))
        }
    }
    return ""
}
</script></script>
```