---
layout: post
title: "Eclipse将java的工程转换为web工程"
date: 2013-10-14 15:30
comments: true
categories: JavaWeb
---

很多时候，Java的项目并不像C++的项目那么“友好”，好多东西写在隐藏文件里，而且开发方法差异也比较大。比如很多用MyEclipse，有些人用Eclipse，这些工具创建出来的项目不太一样，在进行导入时会需要对工程进行转换，方法如下：

转换步骤： 

*	1、进入项目目录，可看到.project文件，打开。 
*	2、找到<natures>...</natures>代码段。 
*	3、在第2步的代码段中加入如下标签内容并保存： 
*	
```
<nature>org.eclipse.wst.common.project.facet.core.nature</nature> 
<nature>org.eclipse.wst.common.modulecore.ModuleCoreNature</nature> 
<nature>org.eclipse.jem.workbench.JavaEMFNature</nature>
```

*	4、在eclipse的项目上点右键，刷新项目。 
*	5、在项目上点右键，进入属性（properties） 
*	6、在左侧列表项目中点击选择“Project Facets”，在右侧选择“Dynamic Web Module”和"Java"，点击OK保存即可 

{% img http://b.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=58929a39c45c1038207ecec7822ae22e/342ac65c103853434a301d029113b07eca808812.jpg?referer=64a02a26e7dde711bec577c6e8f0&x=.jpg %}

