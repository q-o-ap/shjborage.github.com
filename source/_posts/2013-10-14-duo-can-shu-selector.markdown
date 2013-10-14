---
layout: post
title: "多参数selector"
date: 2013-10-14 16:09
comments: true
categories: iOS
---

Selector是Objective-C一个非常强大的特性，合理使用Selector可以大大简化实现并避免重复代码。但NSObject提供 的performSelector最多只支持两个参数，对于两个以上的参数就无能为力了。一番调查后针对NSObject增加了如下扩展，使得 performSelector可以支持传入参数数组。多个参数就不再是问题了。

{% codeblock %}
@interface NSObject (Addition)
- (id)performSelector:(SEL)selector withObjects:(NSArray *)objects;
@end

@implementation NSObject (Addition)
- (id)performSelector:(SEL)selector withObjects:(NSArray *)objects { 
    NSMethodSignature *signature = [self methodSignatureForSelector:selector]; 
    if (signature) { 
        NSInvocation* invocation = [NSInvocation invocationWithMethodSignature:signature]; 
        [invocation setTarget:self]; 
        [invocation setSelector:selector]; 
        
        for(int i = 0; i &lt; [objects count]; i++){ 
            id object = [objects objectAtIndex:i]; 
            [invocation setArgument:&object atIndex: (i + 2)];        
        } 

        [invocation invoke]; 

        if (signature.methodReturnLength) { 
            id anObject; 
            [invocation getReturnValue:&anObject]; 
            return anObject; 
        } else { 
            return nil; 
        } 
    } else { 
        return nil; 
    } 
} 

@end
{% endcodeblock %}

本文出自 “林家男孩” 博客，请务必保留此出处http://bj007.blog.51cto.com/1701577/538995