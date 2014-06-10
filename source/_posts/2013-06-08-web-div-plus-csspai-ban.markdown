---
layout: post
title: "Web Div+CSS排版"
date: 2013-06-08 16:08
comments: true
categories: other
tags: CSS Web
---

######显示效果见源网页：http://www.java3z.com/cwbwebhome/article/article9/ht26.html

用DIV把元素定义为块对象，用CSS设置对象的格式和位置。

CSS+DIV的排版方式是目前应用很广的排版方式，它的使用非常灵活，可制作非常复杂的版面。以下是几种常用的CSS+DIV的排版技术。

纵向排列元素
```
用 <div> 标签定义块对象，由于 <div> 标签本身有换行作用，各元素自然排成一列。用CSS的 margin 属性设置对象间的距离，用 padding 属性调整对象的宽度和高度。
```

<!-- more -->

举例：
{% codeblock %} 
<style type="text/css">
#menu {
   width: 100px; font-size: 15px;
}
.dd {
   border: 1px dotted #0000FF; padding-top: 5px; padding-bottom: 5px; padding-left: 5px; margin-bottom: 3px;
}
</style>
<div id="menu">
<div class="dd">HTML</div>
<div class="dd">CSS</div>
<div class="dd">JavaScript</div>
</div>
{% endcodeblock %}
```
用 <div> 标签定义块对象，用CSS的 float 属性设置对象的浮动。
```
举例：
{% codeblock %}
<style type="text/css">
#box {
   height: 110px;
}
#b1 {
   width: 120px; height: 100px; border: 4px double #0000FF; float: left;
}
#b2 {
   width: 120px; height: 100px; border: 4px double #0000FF; float: left; clear: none; margin-left: 5px; margin-right: 5px;
}
#b3 {
   width: 120px; height: 100px; border: 4px double #0000FF; float: left; clear: right;
}
</style>
<div id="box">
<div id="b1"></div>
<div id="b2"></div>
<div id="b3"></div>
</div>
{% endcodeblock %}


用列表排列元素

```
用 <ul> 或 <ol> 标签制作列表，用CSS设置列表项目的效果。这种方法主要用于规则排列的文本块、图片、控件等。
```

举例：
{% codeblock %}
<style type="text/css">
.list1 {
   height: 20px;
}
.list1 ul {
   list-style-type: none; margin: 0px;
}
.list1 li {
   float: left; margin-right: 5px;
}
</style>
<div class="list1">
<ul>
<li>[1]</li>
<li>[2]</li>
<li>[3]</li>
<li>[4]</li>
</ul>
</div>

{% endcodeblock %}


[1] [2] [3] [4]
用绝对坐标定位元素

浏览器窗口的左上角坐标为 (0,0)，x 坐标向右，y 坐标向下。

CSS提供了几个位置属性，可以设置对象在页面中的位置。

position：当它取值为 absolute 时，表示对象使用绝对坐标定位。

left、top：对象的左上角坐标。

right、bottom：对象的右下角坐标。

z-index：对象的层叠顺序。取值为一个整数。

用绝对坐标定位的对象是可以发生重叠的，如果没有指定层叠顺序，则后定义的对象位于上层，如果指定了“z-index”值，则值大的位于上层。

举例：
{% codeblock %}
<style type="text/css">
#m1 {
   width: 120px; height: 100px; border: 4px double #0000FF; position: absolute; left: 50px; top: 10px; z-index: 1;
}
#m2 {
   width: 120px; height: 100px; border: 4px double #0000FF; position: absolute; left: 185px; top: 10px; z-index: 2;
}
#m3 {
   width: 120px; height: 100px; border: 4px double #0000FF; position: absolute; left: 320px; top: 10px; z-index: 3;
}
</style>
<div id="m1"></div>
<div id="m2"></div>
<div id="m3"></div>

{% endcodeblock %}

我们一般不推荐使用这种方法制作网页，这种网页调整起来非常困难，只是在一些特殊情况下使用。

用相对坐标定位元素

父对象的左上角坐标为 (0,0)，对象的坐标是相对于父对象的。

position：当它取值为 relative 时，表示对象使用相对坐标定位。

left、top：对象的左上角坐标。

right、bottom：对象的右下角坐标。

以上坐标也可理解为相对于父对象的左上角偏移的距离。

注意：用相对坐标定位的对象不允许层叠。此时 z-index 属性无效。

用相对坐标定位对象在实现一些不规则的排列或拼接时有很好的效果。

举例：

{% codeblock %}
<style type="text/css">
#area {
   width: 270px; height: 70px; border: 1px solid #0000FF;
}
#h1 {
   position: relative; left: 85px; top: 10px;
}
#h2 {
   position: relative; left: 15px; top: 32px;
}
#h3 {
   position: relative; left: 75px; top: 40px;
}
#h4 {
   position: relative; left: 115px; top: 25px;
}
</style>
<div id="area">
<img id="h1" src="./image/face19.gif" />
<img id="h2" src="./image/face19.gif" />
<img id="h3" src="./image/face19.gif" />
<img id="h4" src="./image/face19.gif" />
</div>

{% endcodeblock %}


