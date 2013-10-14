---
layout: post
title: "Cocoa 自省机制"
date: 2013-08-01 16:30
comments: true
categories: iOS
---

###内省

内省（Introspection）是面向对象语言和环境的一个强大特性，Objective-C和Cocoa在这个方面尤其的丰富。内省是对象揭示自己作为一个运行时对象的详细信息的一种能力。这些详细信息包括对象在继承树上的位置，对象是否遵循特定的协议，以及是否可以响应特定的消息。NSObject协议和类定义了很多内省方法，用于查询运行时信息，以便根据对象的特征进行识别。

明智地使用内省可以使面向对象的程序更加高效和强壮。它有助于避免错误地进行消息派发、错误地假设对象相等、以及类似的问题。下面的部分将介绍如何在代码中有效地使用NSObject的内省方法。

***本部分包括如下内容：***

* 评估继承关系
* 方法实现和协议遵循
* 对象的比较


####评估继承关系
一旦您知道一个对象属于什么类，就可能已经相当了解这个对象了。您可以知道它具有什么能力、哪些属性、以及可以响应哪些消息。即使在内省之后不能了解对象所属的类，也可以知道该对象不能响应特定的消息。

NSObject协议声明了几个方法，用于确定对象在类层次中的位置。这些方法在不同粒度上进行操作，比如class和superclass实例方法分别返回代表类和超类的Class对象。使用这些方法需要将一个Class对象和另一个进行对比。列表2-7给出了一个简单（可能是没有价值）的用法实例。

列表2-7  使用类和超类的方法

```
// ...
while ( id anObject = [objectEnumerator nextObject] ) {
    if ( [self class] == [anObject superclass] ) {
        // do something appropriate...
    }
}
```
请注意：有些时候您需要通过class或superclass方法得到正确的类消息接收者。
更加常见的是检查对象类的从属关系，这种情况下您需要向该对象发送isKindOfClass:或isMemberOfClass:消息。前一个方法返回接收者是否为给定类或其继承类的实例，isMemberOfClass:消息则告诉您接收者是否为指定类的实例。isKindOfClass: 方法通常更有用，因为通过它可以知道是否可以向该对象发送一系列消息。考虑列表2-8中的代码片断：

列表2-8  使用isKindOfClass:方法

```
if ([item isKindOfClass:[NSData class]]) {
    const unsigned char *bytes = [item bytes];
    unsigned int length = [item length];
    // ...
}
```
确定tem对象是NSData类的继承类的实例之后，代码就知道可以向它发送NSData的bytes和length消息。假定item是NSMutableData类的一个实例，则isKindOfClass:和isMemberOfClass:之间的差别就变得更加明显。如果您调用的是isMemberOfClass:，而不是isKindOfClass:，条件控制块中的代码将永远不会被执行，因为item并不是NSData类的实例，而是其子类NSMutableData的实例。

####方法实现和协议遵循
NSObject还有两个功能更加强大的内省方法，即respondsToSelector:和conformsToProtocol:。这两个方法分别告诉您一个对象是否实现特定的方法，以及是否遵循指定的正式协议（即该对象是否采纳了该协议，且实现了该协议的所有方法）。

在代码中，您可以在类似的情况下使用这些方法。通过这些方法，您可以在将消息或消息集合发送给某些潜在的匿名对象之前，确定它们是否可以正确地进行响应。在发送消息之前进行检查可以避免由不能识别的选择器引起的运行时例外。在实现非正式协议（这种协议是委托技术的基础）时，Application Kit就是在调用委托方法之前检查委托对象是否实现该方法（通过respondsToSelector:方法）。

列表2-9显示了如何在代码中使用respondsToSelector:方法。

列表2-9  使用respondsToSelector:方法
```
- (void)doCommandBySelector:(SEL)aSelector {
    if ([self respondsToSelector:aSelector]) {
        [self performSelector:aSelector withObject:nil];
    } else {
        [_client doCommandBySelector:aSelector];
    }
}
```
列表2-10显示如何在代码中使用conformsToProtocol:方法：

列表2-10  使用conformsToProtocol:方法

```
// ...
if (!([((id)testObject) conformsToProtocol:@protocol(NSMenuItem)])) {
    NSLog(@"Custom MenuItem, '%@', not loaded; it must conform to the
        'NSMenuItem' protocol.\n", [testObject class]);
    [testObject release];
    testObject = nil;
}
```

####对象的比较
hash和isEqual:方法虽然不是严格的内省方法，但是可以发挥类似的作用，是进行对象的识别和比较时不可或缺的运行时工具。它们并不向运行环境查询对象信息，而是依赖于具体类的比较逻辑。

hash和isEqual:方法都在NSObject协议中声明，且彼此关系紧密。实现hash方法必须返回一个整型数，作为哈希表结构中的表地址。两个对象相等（isEqual:方法的判断结果）意味着它们有相同的哈希值。如果您的对象可能被包含在象NSSet这样的集合中，则需要定义hash方法，并确保该方法在两个对象相等的时候返回相同的哈希值。NSObject类中缺省的isEqual:实现只是简单地检查指针是否相等。

isEqual:的使用相当直接，它将消息的接收者和通过参数传入的对象进行比较。对象的比较常常可以在运行时决定应该对对象做些什么。如列表2-11所示，您可以通过isEqual:来确定是否执行某一个动作。在这个例子中，动作是指保存被修改了的预置信息。

列表2-11  使用isEqual:方法
```
- (void)saveDefaults {
    NSDictionary *prefs = [self preferences];
    if (![origValues isEqual:prefs]) 
        [Preferences savePreferencesToDefaults:prefs];
}
```
如果您正在创建子类，则可能需要重载isEqual:方法，以进一步检查对象是否相等。子类可能定义额外的属性，当两个实例被认为相等时，属性的值必须相同。举例来说，假定您创建一个名为MyWidget的NSObject子类，类中包含两个实例变量：name和data。当MyWidget的两个实例被认为是相等时，这些变量必须具有相同的值。列表2-12显示如何在MyWidget类中实现isEqual:方法。

列表 2-12  重载isEqual:方法
```
- (BOOL)isEqual:(id)other {
    if (other == self) 
        return YES;
    if (!other || ![other isKindOfClass:[self class]]) 
        return NO;
    return [self isEqualToWidget:other];
}
 
- (BOOL)isEqualToWidget:(MyWidget *)aWidget {
    if (self == aWidget) 
        return YES;
    if (![(id)[self name] isEqual:[aWidget name]])
        return NO;
    if (![[self data] isEqualToData:[aWidget data]])
        return NO;
    return YES;
}
```
isEqual:方法首先检查指针的等同性，然后是类的等同性，最后调用对象的比较器进行比较。比较器的名称指示出参与比较的对象的类名称。这种类型的比较器对传入的对象进行强制类型检查，是Cocoa中常见的约定，NSString的isEqualToString:和NSTimeZone的isEqualToTimeZone:就是两个这样的例子。特定类的比较器（在这个例子中是isEqualToWidget:）负责执行name和data变量的等同性。

在Cocoa框架的所有isEqualToType:方法中，nil都不是正当的参数，这些方法的实现在接收到nil参数时会抛出例外。然而为了向后兼容，Cocoa框架中的isEqual:方法可以接收nil值，在这种情况下返回NO。