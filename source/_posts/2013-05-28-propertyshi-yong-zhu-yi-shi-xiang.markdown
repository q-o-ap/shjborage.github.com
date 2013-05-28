---
layout: post
title: "Property 使用注意事项"
date: 2013-05-28 11:04
comments: true
categories: iOS
---

*	NSString property的属性必须是copy
*	delegate property的属性是assign
*	dealloc 时，如果有delegate，要把delegate置成nilj,避免收到消息等
*	init 和dealloc中不要用self.XX=;

####NSCoping  

Any object that you wish to set for a copy property must support NSCopying, which means that it should conform to the NSCopying protocol.  
  

If you need to set a copy property’s instance variable directly, for example in an initializer method, don’t forget to set a copy of the original object:

```
- (id)initWithSomeOriginalString:(NSString *)aString {
    self = [super init];
    if (self) {
        _instanceVariableForCopyProperty = [aString copy];
    }
    return self;
}
```

