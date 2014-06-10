---
layout: post
title: "JSP中java代码与js之间的传值"
date: 2013-06-17 15:04
comments: true
categories: JavaWeb
tags: Java Web
---


#####java   ---->   js: 
List的传递：直接赋值就行，如下：   

Js代码
```
var userIdList = "<%=archiveNameList%>";  //不加引号是错误的！  
```

<!-- more -->

为js中var赋java变量值（同样适用于el表达式和struts2标签等）时，切记需要为赋值串加引号"": 
如果java变量为纯数字类型的，可以不加，js可以将其识别为Number类型的；但任何时候都加上不失为一个好的习惯，可以让你不为莫名其妙的错误所困扰。   
  
引用
JSP代码
```
<script type="text/javascript">  
    var ploIds = "<%=request.getAttribute("refreshStrParam")%>"; //写成var ploIds = <%=request.getAttribute("refreshStrParam")%> 是错误的！  
    alert(ploIds);  
    <% String s = "abc";%>  
    var s = "<%=s%>"; //写成 var s = <%=s%>;是错误的  
    alert(s);  
    alert("${param.name}"); //写成alert(${param.name})是错误的  
</script>  
```


数组的传递：不能像List那样直接赋值.可以参考这里
http://wuaner.iteye.com/blog/455481