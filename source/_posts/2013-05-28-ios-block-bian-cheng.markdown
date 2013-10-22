---
layout: post
title: "iOS block 编程"
date: 2013-05-28 09:57
comments: true
categories: iOS
---

##block
Blocks are often used to simplify common tasks such as collection enumeration, sorting and testing. They also make it easy to schedule tasks for concurrent or asynchronous execution using technologies like Grand Central Dispatch (GCD).
<!-- more -->
####声明block：

```
void (^simpleBlock)(void);
```



####定义block：

```
simpleBlock = ^{
        NSLog(@"This is a block");
};
```  

####声明和定义在一起：    


```
void (^simpleBlock)(void) = ^{
    NSLog(@"This is a block");
};
```

####调用block：  


```
simpleBlock();
```  


####带返回值和参数的block:

```
double (^multiplyTwoValues)(double, double) =
                              ^(double firstValue, double secondValue) {
                                  return firstValue * secondValue;
                              };
    double result = multiplyTwoValues(2,4);
    NSLog(@"The result is %f", result);
```  


    
If you need to be able to change the value of a captured variable from within a block, you can use the __block storage type modifier on the original variable declaration.

  

####如果block是递归调用的，必须设置成 __block。e.g.

```
__block int (^recursiveBlock)(int) = ^(int param) {
		if (param == 1) {
			return 1;
		}
		return (param--) * recursiveBlock(param);
};  
recursiveBlock(5);	// 计算5的阶乘
```  


You Can Pass Blocks as Arguments to Methods or Functions
- (void)beginTaskWithCallbackBlock:(void (^)(void))callbackBlock;
The (void (^)(void)) specifies that the parameter is a block that doesn’t take any arguments or return any values. The implementation of the method can invoke the block in the usual way:  


```
- (void)beginTaskWithCallbackBlock:(void (^)(void))callbackBlock {
    ...
    callbackBlock();
}
```  



A Block Should Always Be the Last Argument to a Method
Objects Use Properties to Keep Track of Blocks
The syntax to define a property to keep track of a block is similar to a block variable:  


```
@interface XYZObject : NSObject
@property (copy) void (^blockProperty)(void);
@end
```    


Note: You should specify copy as the property attribute, because a block needs to be copied to keep track of its captured state outside of the original scope.
A block property is set or invoked like any other block variable:  


```
    self.blockProperty = ^{
        ...
    };
    self.blockProperty();
typedef void(^BlockCC)(void);
```  


// e.g.更新myTableview并显示最后一行  


```
if (dataArray.count > 0) {
		[myTableView reloadData];	
		// reloadData后不能直接调用scrollToRowAtIndexPath，有可能lastIndex在table中不存在，
	 // 所以要[NSObject performBlock: afterDelay:0];
		[myTableView retain];	// 防止关闭视图，myTableView释放之后scrollToBottom出错
		NSIndexPath *lastIndex = [NSIndexPath indexPathForRow:dataArray.count-1 inSection:0];
		void (^scrollToBottom)(void) = ^{
			[myTableView scrollToRowAtIndexPath:lastIndex atScrollPosition:UITableViewScrollPositionBottom animated:YES];
			[myTableView release];
		};
		//
		[NSObject performBlock:scrollToBottom afterDelay:0];
}
```
