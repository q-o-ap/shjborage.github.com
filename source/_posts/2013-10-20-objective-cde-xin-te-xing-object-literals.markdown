---
layout: post
title: "Objective-C的新特性(Object Literals等)"
date: 2013-10-20 22:28
comments: true
categories: iOS
---

转自：http://blog.devtang.com/blog/2012/08/05/use-modern-objective-c/ (Posted by 唐巧 Aug 5th, 2012  iOS)

苹果在今年的WWDC2012大会上介绍了大量Objective-C的新特性，能够帮助iOS程序员更加高效地编写代码。在不久前更新的XCode4.4版本中，这些新特性已经可以使用了。让我们看看这些新特性有哪些：

###Object Literals

这个是我认为最赞的一个改进。Object Literals允许你方便地定义数字、数组和字典对象。这个功能类似于java5提供的auto boxing功能。这虽然是一个语法糖，但我认为对提高写代码效率帮助很大。让我们先来看看以前定义数字、数组和字典对象的方法：

```
123NSNumber * number = [NSNumber numberWithInt:1];
NSArray * array = [NSArray arrayWithObjects:@"one", @"two", nil];
NSDictionary * dict = [NSDictionary dictionaryWithObjectsAndKeys:@"value1", @"key1", @"value2", @"key2", nil];
```

是不是很恶心？现在以上代码可以简化成以下形式，注意到没有，不用再在参数的最后加恶心的nil了，字典的key和value也不再是倒着先写value,再写key了：

<!-- more -->

```
NSNumber * number = @1;
NSArray * array = @[@"one", @"two"];
NSDictionary * dict = @{@"key1":@"value1", @"key2":@"value2"};
```

更多的示例如下：

{% codeblock %}
// 整数  
NSNumber *fortyTwo = @42;             // 等价于 [NSNumber numberWithInt:42]  NSNumber *fortyTwoUnsigned = @42U;    // 等价于 [NSNumber numberWithUnsignedInt:42U]  NSNumber *fortyTwoLong = @42L;        // 等价于 [NSNumber numberWithLong:42L]  NSNumber *fortyTwoLongLong = @42LL;   // 等价于 [NSNumber numberWithLongLong:42LL]  

// 浮点数  
NSNumber *piFloat = @3.141592654F;    // 等价于 [NSNumber numberWithFloat:3.141592654F]  
NSNumber *piDouble = @3.1415926535;   // 等价于 [NSNumber numberWithDouble:3.1415926535]  

// 布尔值  
NSNumber *yesNumber = @YES;           // 等价于 [NSNumber numberWithBool:YES]  NSNumber *noNumber = @NO;             // 等价于 [NSNumber numberWithBool:NO]  

// 空数组  

NSArray * array = @[];                // 等价于 [NSArray array]  // 空的字典  NSDictionary * dict = @{};            // 等价于 [NSDictionary dictionary]
{% endcodeblock %}

怎么样？是不是简单多了？而且，为了方便你的旧代码迁移到新的写法，xcode专门还提供了转换工具，在xcode4.4中，选择 Edit -> Refactor -> Convert to Modern Objective-C Syntax即可。

###局部的函数调用不用前向申明

这虽然是一个挺小的改进，但是很贴心。假如我们在一个源文件中有2个函数：分别名为foo 和 bar，其中foo的定义在bar前面。那如果在foo函数内部直接调用bar，编译器会报警告说找不到函数bar。
而现在，我们可以随意地在源文件中放置函数bar的位置。编译器在找不到bar时，会再源码后面找，如果找到了bar，就不会报错了。

###带有类型的enum

现在我们可以定义enum是无符号整数还是整数，这样编译器会更加智能的做类型检查。如下所示：

```
typedef enum TableViewCellType : NSInteger {    
TableViewCellTypeQueue,    
TableViewCellTypeNewFans,    
TableViewCellTypeUserInfo,    
TableViewCellTypeOrganization,    
TableViewCellTypeFeedback,    
TableViewCellTypeRateApp,    
TableViewCellTypeRecommendation,    
TableViewCellTypeLogout}TableViewCellType;
```

###默认生成@synthesize代码

以前写完一个诸如 @property (nonatomic, strong) NSString * username; 变量定义后，马上得转到 .m文件中去增加相应的 @synthesize username = _username; 代码。

现在，编辑器发现你没有写 @synthesize时，会自动帮你加上这一行。这同时在另一方面，起到了鼓励大家使用以下划线开头的变量名作为成员变量名的作用。

当然，为了向下兼容，如果你的程序里面已经有了 @property 变量对应的 @synthesize 代码时，编辑器就不会自动帮你增加这个代码了。
另外有2种特殊情况下，即使你没有写 @synthesize ，编辑器也不会自动帮你加上，这2种情况是：

+	你同时提供了该property的setter 和 getter方法。
+	你的这个property是 readonly 的。
  

###遍历元素

你是如何遍历数组的元素的？通常我们有2种做法，一种是用 for in，另一种是用一个变量来循环数组下标。如下：

```
// 12345678    NSArray * lines = ...    
for (NSString * line in lines) {
       // ...    
}
for (int i = 0; i < lines.count; ++i) {        
	NSString * s = [lines objectAtIndex:i];        
	...    
}
```

如果是字典，遍历的代码就要稍微复杂一点了：

```
NSDictionary * dict = …    NSArray * keys = [dict allKeys];
for (NSString * key in keys) {        
    NSString * value = [dict objectForKey:key];    
}
```

现在，xcode对于iOS4.0以上的系统，支持用block来遍历元素了。用block来遍历字典可以简化代码的编写，建议大家都使用上这个新特性。

```
[lines enumerateObjectsUsingBlock:^(NSString * obj, NSUInteger idx, BOOL *stop) {}];[_urlArguments enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) {}];
```

###Subscripting Methods
这个新特性在WWDC2012的视频中提到了，但是在XCode4.4中没有实现（在XCode4.5中实现了）。也是一个很体贴的语法糖，它允许你用中括号来代替原本的方法来获取和设置数组元素。

简单来说，以前的 [array objectAtIndex:idx] 和 [array replaceObjectAtIndex:idx withObject:obj]，可以直接写作 array[idx] 和 array[idx] = obj了。其实这个特性在很多高级语言中都实现了，只是Objective-C生于80年代，一直没改进这个。

以下是一些示例代码：

```
NSArray * array = @[ @"111", @"222", @"333"];
for (int i = 0; i < 3; ++i) {
    NSLog(@"array[i] = %@", array[i]);
}
NSMutableDictionary * dict =[@{  @1: @"value1", @2: @"value2",                                 @3: @"value3" } mutableCopy];
for (int i = 0; i < 3; ++i) {
    NSLog(@"dict[%d] = %@", i, dict[@(i+1)]);
    dict[@(i+1)] = [NSString stringWithFormat:@"new %@", dict[@(i+1)]];}
    [dict enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop{
   		NSLog(@"dict[%@] = %@", key, dict[key]);
    }];
}
```

这个改进同样对NSDictionary有效。甚至，你也可以给你自己的类提供中括号操作符对应的方法。具体做法是实现如下两个方法：

```
- (id)objectAtIndexedSubscript:(NSUInterger)idx;- (void)setObject:(id)value atIndexedSubscript:(NSUInteger)idx;
```

###Tips
上面提到了不用写 @synthesize 了，那原本写的那么多 @synthesize 怎么办呢？作为有代码洁癖的我很想把它们删掉，但怎么删呢？一个文件一个文件打开，然后行一行删掉吗？放心，苹果已经帮我们想了解决方案。在WWDC2012 Session 400 Developer Tools Kickoff 中，苹果介绍了具体做法。步骤如下：

+ 	首先使用区域查找，因为一般项目都会依赖第三方的开源库，我们可不想更改别人的库，所以我们只查找我们库中的文件，如下图所示：  
  
<a href="http://www.flickr.com/photos/105999540@N03/10382148323/" title="2013-10-20-1 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3803/10382148323_3e5d2aabbc.jpg" width="317" height="449" alt="2013-10-20-1"></a>
  
+	接着我们用正则匹配，找到以 @synthesize开头，后面接着是 var = _var; 格式的行。插入正则表达式很简单，直接点击查找输入框左边的放大镜，选择“insert pattern”，苹果就会把常见的正则表达式都列出来，你直接选择就可以了，非常方便。如下图所示：  

<a href="http://www.flickr.com/photos/105999540@N03/10382148463/" title="2013-10-20-2 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7415/10382148463_a1fe6d797a.jpg" width="441" height="181" alt="2013-10-20-2"></a>

在插入好合适的正则表达式后，我们按回车，就可以搜索到结果。
<a href="http://www.flickr.com/photos/105999540@N03/10382148643/" title="2013-10-20-3 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3724/10382148643_1604c7809b.jpg" width="296" height="315" alt="2013-10-20-3"></a>

+	我们点击搜索界面的preview按钮，查看替换效果，可以看到，对于我们测试代码，XCode生成的预览图已经正确地当对应代码删掉了。然后我们就可以点击替换，去掉所有的 @synthesize 代码了。  

<a href="http://www.flickr.com/photos/105999540@N03/10382148923/" title="2013-10-20-4 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3769/10382148923_5836e42c21.jpg" width="500" height="97" alt="2013-10-20-4"></a>

在下载完XCode4.4后，我就把我们的工程代码都转换成了新特性的语法。在转换后，我发现原本25000行的代码少了将近1000行。心里还是很开心的，因为又可以少写一些体力活类型的代码了。
还是那句话，希望这些新特性能够让大家玩得开心。
参考资料

   * LLVM官方网站比较全面地介绍了 Object Literal： http://clang.llvm.org/docs/ObjectiveCLiterals.html
   * WWDC2012 Session 400 Developer Tools Kickoff
   * WWDC2012 Session 405 Modern Objective-C
   * WWDC2012 Session 413 Migrating to Modern Objective-C
