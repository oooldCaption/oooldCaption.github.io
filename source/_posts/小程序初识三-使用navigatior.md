---
title: 小程序初识三-使用navigatior
tags: []
id: '39'
categories:
  - - 前端开发
date: 2018-04-17 14:56:31
---

前两节介绍了小程序的开发配置和对视图 进行简单的数据渲染, 下面来介绍一些简单的基础控件和页面跳转: 小程序使用navigator来作为导航, 这一点和iOS的navigationBar很像;

*   点击跳转新的界面

可以给按钮设置点击事件,然后再点击事件中设置跳转事件

```
<button bindtap='jump2page'>点我跳转</button>
<!-- 在对应页面的js文件中编写方法 -->
1. 这种方式是以打开新页面的形式进行跳转,可以使用 ? 拼接params
    jump2page:function(){
        wx.navigateTo({
            url: '/pages/index/test?title=打开新页面啦',
         })
    }
```

*   点击在当前的页面打开新的页面 点我跳转
    
    1.  这种方式是在当前页面打开,可以使用 ? 拼接params jump2page:function(){ wx.redirectTo({
        
    
    ## url: '/pages/index/test?title=打开新页面啦',
    
    ```
         })
    }
    ```
    
*   关闭当前页面 关闭当前页面，返回上一页面或多级页面。可通过 getCurrentPages()) 获取当前的页面栈，决定需要返回几层。 // 此处是A页面 wx.navigateTo({ url: 'B?id=1' }) // 此处是B页面 wx.navigateTo({ url: 'C?id=1' }) // 在C页面内 navigateBack，将返回A页面 wx.navigateBack({ delta: 2 })
    
*   还可以在wxml中使用 navigation 标签进行设置页面跳转 /\*\* wxss **/ /** 修改默认的navigator点击态 **/ .navigator-hover { color:blue; } /** 自定义其他点击态样式类 \*\*/ .other-navigator-hover { color:red; }
    
    跳转到新页面 在当前页打开 切换 Tab