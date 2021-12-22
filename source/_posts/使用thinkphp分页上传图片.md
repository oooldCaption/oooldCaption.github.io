---
title: 使用thinkphp分页&上传图片
tags: []
id: '32'
categories:
  - - 后端开发
date: 2018-04-17 14:49:42
---

使用 thinkPHP 实现分页: 新建控制器LoginController.class.php 在 LoginController 中创建 index 方法:
<!-- more -->
Page 类 和 limit方法实现

```
$data  = M('userinfo');
$map['user_id'] = array('gt',0);
$count = $data->where()->count(); // 满足条件的记录数
//Page 的构造方法,第一个参数为总记录数,第二个是每页显示的数量
$page = new Page($count,5);
$show = $page->show();
$list = $User->where($map)->order('create_time')->limit($Page->firstRow.','.$Page->listRows)->select();
$this->assign('list',$list);// 赋值数据集
$this->assign('page',$show);// 赋值分页输出
$this->display();
```

在对应的试图模板中编写如下代码:

```
<table cellpadding=3 cellspacing=5>
    <volist name="list" id="vo">
        <tr>
            <td >[ {$vo.user_id} ] {$vo.user_id} </td>
        </tr>
    </volist>
    <tr>
    </tr>
</table>
<div class="resultpage" style="width: 500px">{$page}</div>
```

这样在对应的视图就会显示相应的页码和数据信息;不过有点难看,不过别担心,稍后咱们在重新设置; 接下来咱们看第二种实现方式: 分页类和 page 方法的实现 将 index 方法中的代码替换成如下:

```
$data  = M('userinfo');
$map['user_id'] = array('gt',0);
$list = $data->where($map)->order('user_id)->page($_GET['p'].',5')->select();
$this->assgin('list',$list);
//引入分页模块
$count = $data->where($map)->count();
$page = new Page($count, '5');
$show = $page->show();
$this -> assgin('page',$show);
$this->display();
```

修改分页显示效果 thinkphp 中的分页类 Page 提供了一个方法 setConfig 来修改分页栏的显示效果:

*   header：头部描述信息，默认值 “条记录”
*   prev：上一页描述信息，默认值是“上一页”
*   next：下一页描述信息，默认值是“下一页”
*   first：第一页描述信息，默认值是“第一页”
*   last：最后一页描述信息，默认值是“最后一页”
*   theme ：分页主题描述信息， \`\` $page->lastSuffix = false; $page->setConfig('header','共**%TOTAL\_ROW%**条记录'); $page->setConfig('prev','<'); $page->setConfig('next','>'); $page->setConfig('first','首页'); $page->setConfig('last','尾页'); $page->setConfig('theme', '%FIRST%%UP\_PAGE%%LINK\_PAGE%%DOWN\_PAGE%%END%%HEADER%'); 我们还可以使用 `css` 属性对分页栏的显示效果进行修改: 上面列表中的 `header` `prev` `next` 等等再视图模板中都是以元素的 `class` 值体现的,所以我们可以通过修改 class 的样式表定制显示效果,达到我们想要的效果:
    

.resultpage{

```
background: black;
color: honeydew;
margin-right: 5px;
margin-left: 5px;
padding-left: 5px;
padding-right: 5px;
}
```

.num {

```
color: honeydew;
padding-left: 5px;
padding-right: 5px;
```

} .current{color: red;} .prev{color: honeydew;} .next{color: honeydew;} .last{color: honeydew;} .first{color: honeydew;} a{text-decoration: none}