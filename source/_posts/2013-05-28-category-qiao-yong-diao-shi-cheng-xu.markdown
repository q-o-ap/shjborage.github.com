---
layout: post
title: "Category 巧用-调试程序"
date: 2013-05-28 10:52
comments: true
categories: iOS
---

###Category可以用来调试

除了隐藏私有方法外，我主要用它截住函数。 


例1：测试时我想知道TableViewCell有没有释放，就可以这样写 


```
@implementation UITableViewCell(dealloc) 
-(void)dealloc 
{ 
   NSLog(@"%@",NSStringFromSelector(_cmd)); 
    NSArray *array = allSubviews(self); 		// allSubviews是cookBook里的函数，可以取一个view的所有subView ,在这个文档后面也有
    NSLog(@"%@",array); 
    [super dealloc]; 
}
@end 
```


其它的类也可以这样写，你随便输出什么 



例2：我调试程序，觉得table的大小变了，想找到在哪改变的，这样做：


```
 @implementation UITableView(setframe) 
-(void)setFrame:(CGRect)frame 
{ 
   NSLog(%"%@",self); 
    [super setFrame: frame]; 
} 
@end
``` 

