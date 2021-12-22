---
title: css盒子模型
tags: []
id: '63'
categories:
  - - 前端开发
date: 2018-04-17 15:02:57
---

css 框模型(BoxModel) 定义了元素框处理元素内容, 内/外边距,边框, 外边框的方式; CSS 框模型概述: 元素框的最内部分是实际的内容, 直接包围内容的是内边距(\*padding); 内边距呈现的是元素的背景, 内边距的边缘是边框(border, 可设置bordercolor borderwidth); 边框以为是外边框 , 外边框的默认背景色是透明的, 因此不会遮挡其后的任何元素;
<!-- more -->
内边距, 边框,外边距都是可选的, 默认值是0. 但是,许多元素将由用户代理样式表设置外边距和内边距. 可以通过将元素的margin 和padding 设置为0 来覆盖这些浏览器样式. 这样可以分别进行, 也可以使用通用选择器对所有元素进行设置:

```
 * {
    margin : 0;
    padding: 0;
 }
```

在 css 中, width 和height指的是内容区域的宽度和高度;增减内外边距和边框并不会影响内容区域的尺寸,但是影响元素框的总尺寸 假设框的每一个边上都有10 个像素的外边距和5个像素的内边距. 如果希望这个元素框达到100个像素, 那就需要将内容的宽度设置为 70 像素,:

```
#box {
width: 70px;
margin: 10px;
padding: 5px;
}
/*
tip:
 内外边距,边框都可以应用于一个元素的所有便, 也可以用于单独的边
 eg : border-left-width: 10px ;  ---> 单独给左侧边框线设置线宽为10px
 外边距可以使负值, 而且在很多情况下都要使用复制的外边距;
*/
```

  
CSS内边距: 元素的内边距在的边框和内容之间, 控制该区域最简单的属性是padding属性, css 的padding属性定义元素边框与元素内容之间的空白区域; css padding 属性 CSS padding 属性定义元素的内边距。padding 属性接受长度值或百分比值，但不允许使用负值。 例如，如果您希望所有 h1 元素的各边都有 10 像素的内边距，只需要这样： l

```
h1 {padding: 10px;}
```

您还可以按照上、右、下、左的顺序分别设置各边的内边距，各边均可以使用不同的单位或百分比值：

```
h1 {padding: 10px 0.25em 2ex 20%;}
```

单边内边距的属性值:

*   padding-top 上内边距
*   padding-bottom 下内边距
*   padding-left 左内边距
*   padding-right 右内边距 h1 { padding-top: 10px; padding-right: 0.25em; padding-bottom: 2ex; padding-left: 20%; }
    

CSS 边框 在 HTML 中，我们使用表格来创建文本周围的边框，但是通过使用 CSS 边框属性，我们可以创建出效果出色的边框，并且可以应用于任何元素。 元素外边距内就是元素的的边框 (border)。元素的边框就是围绕元素内容和内边据的一条或多条线。 每个边框有 3 个方面：宽度、样式，以及颜色。 边框与背景 CSS 规范指出，边框绘制在“元素的背景之上”。这很重要，因为有些边框是“间断的”（例如，点线边框或虚线框），元素的背景应当出现在边框的可见部分之间。 CSS2 指出背景只延伸到内边距，而不是边框。后来 CSS2.1 进行了更正：元素的背景是内容、内边距和边框区的背景。大多数浏览器都遵循 CSS2.1 定义，不过一些较老的浏览器可能会有不同的表现。 边框的样式 样式是边框最重要的一个方面，这不是因为样式控制着边框的显示（当然，样式确实控制着边框的显示），而是因为如果没有样式，将根本没有边框。 CSS 的 border-style 属性定义了 10 个不同的非 inherit 样式，包括 none。 例如，您可以为把一幅图片的边框定义为 outset，使之看上去像是“凸起按钮”：

```
a:link img {border-style: outset;}
```

*   定义多种样式: p.aside {border-style: solid dotted dashed double;}
    
*   定义单边样式: border-top-style border-right-style border-bottom-style border-left-style
    

边框的宽度

*   通过border-width 置顶边框宽度

为边框指定宽度有两种方法：可以指定长度值，比如 2px 或 0.1em；或者使用 3 个关键字之一，它们分别是 thin 、medium（默认值） 和 thick。 注释：CSS 没有定义 3 个关键字的具体宽度，所以一个用户代理可能把 thin 、medium 和 thick 分别设置为等于 5px、3px 和 2px，而另一个用户代理则分别设置为 3px、2px 和 1px。 所以，我们可以这样设置边框的宽度：

```
p {border-style: solid; border-width: 5px;}
or
p {border-style: solid; border-width: thick;}
```

*   定义单边的线宽 p {border-style: solid; border-width: 15px 5px 15px 5px;} border-right-width border-bottom-width border-left-width border-top-width
    
*   没有边框
    

如果希望显示某种边框, 就需要设置边框样式 , 默认情况下 border-style 默认 是none ; 如果我们设置style 为none , 然后设置边框的宽度, 边框的样式是不存在的, 而且 边框的宽度自动设置成0 , 所以,我们需要设置边框的时候, 一定要使用 border-style 进行设置一下边框线的style; 边框的颜色 border-corlor 设置边框颜色非常简单。CSS 使用一个简单的 border-color 属性，它一次可以接受最多 4 个颜色值。 可以使用任何类型的颜色值，例如可以是命名颜色，也可以是十六进制和 RGB 值：\\

```
p {
  border-style: solid;
  border-color: blue rgb(25%,35%,45%) #909090 red;
  }
```

如果颜色值小于 4 个，值复制就会起作用。例如下面的规则声明了段落的上下边框是蓝色，左右边框是红色：

```
p {
  border-style: solid;
  border-color: blue red;
  }
/*
注释：默认的边框颜色是元素本身的前景色。如果没有为边框声明颜色，它将与元素的文本颜色
相同。另一方面，如果元素没有任何文本，假设它是一个表格，其中只包含图像，那么该表的边
框颜色就是其父元素的文本颜色（因为 color 可以继承）。这个父元素很可能是 body、div
或另一个 table。
*/
```

定义单边颜色

```
border-top-color
border-right-color
border-bottom-color
border-left-color
```

css margin 属性 外边距 绕在元素边框的空白区域是外边距。设置外边距会在元素外创建额外的“空白”。 设置外边距的最简单的方法就是使用 margin 属性，这个属性接受任何长度单位、百分数值甚至负值。 设置外边距最简单的方法就是使用margin属性 , margin 接受任意长度的单位, 像素, 毫米, 英寸,em 也可以设置成auto, 值赋值 我们有时候会输入一些重复的数字,来设置margin 或者 border 或者padding eg :

```
p {margin: 0.5em 1em 0.5em 1em };
```

通过值复制, 你可以不用重复输入数字":

```
p{margin: 0.5em 1em};
```

这两个数值可以取代前面的四个数值, CSS定义了一些规则, 允许外部安居指定少于4个值

*   缺少左外边距, 默认使用右外边距
*   缺少下外边距,默认使用上外边距
*   缺少右外边距,使用上外边距;

eg

```
h1 {margin: 0.25em 1em 0.5em;}  /* 等价于 0.25em 1em 0.5em 1em */
h2 {margin: 0.5em 1em;}     /* 等价于 0.5em 1em 0.5em 1em */
p {margin: 1px;}            /* 等价于 1px 1px 1px 1px */
```