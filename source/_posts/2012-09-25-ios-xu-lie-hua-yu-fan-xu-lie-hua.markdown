---
layout: post
title: "iOS 序列化与反序列化"
date: 2012-09-25 10:36
comments: true
categories: iOS
tags: iOS Obj-C
---

    
   序列化的问题，在以前做VC的时候就听说过，但实际用的不多。大概意思呢，就是把一个对象（一些数据或者自定义的对象）序列化成文件（一般是XML文件），永久的保存起来。 需要用的时候再通过反序列化恢复原有的数据。  
   在iOS（OC）中，像标准的集合以及字典(NSArray, NSDictionary)，都有直接序列化的方法，wirteToFile...，但这些只适合用于集合中只有标准数据类型，如果有自定义的类，那么就会报错，写入失败了。
    
<!-- more -->   

###标准反序列化
```
   // NSMutableArray *_arDataSource;
   NSString *strCaches = [NSString stringWithFormat:@"%@/tmp/%@",
                        NSHomeDirectory(), kCachedFileName];
   BOOL bWriteSuccess = [_arDataSource writeToFile:strCaches atomically:YES];
   if (bWriteSuccess == NO) {
//            NSLog(@"Wirte file error, unkown error occurred");
   } // end of if (bWriteSuccess == NO) {
```

###标准反序列化
```
   _arDataSource = [[NSMutableArray arrayWithContentsOfFile:strCaches] retain];
```


如果有自定义的类，应该在类中实现NSCoding协议，NSCopying是可选的吧，放在这儿写copy时可以参考下

```
//
//  item_info.h
//  BitCar_Shopping
//
//  Created by Eric on 9/24/12.
//  Copyright (c) 2012 Safe&Quick. All rights reserved.
//
 
#import <Foundation/Foundation.h>
 
#define kClass      @"BC_Class"
#define kSubClass   @"BC_SubClass"
#define kName       @"BC_Name"
#define kPrice      @"BC_Price"
#define kSelected   @"BC_Selected"
 
@interface item_info : NSObject
<
NSCoding,
NSCopying
>
{
    NSString *_strClass;
    NSString *_strSubClass;
    
    NSString *_strName;
    NSString *_strPrice;
    
    BOOL _bSelected;
}
 
@property (nonatomic, copy) NSString *strClass;
@property (nonatomic, copy) NSString *strSubClass;
 
@property (nonatomic, copy) NSString *strName;
@property (nonatomic, copy) NSString *strPrice;
 
@property (nonatomic, assign) BOOL bSelected;
 
@end
```
```
//
//  item_info.m
//  BitCar_Shopping
//
//  Created by Eric on 9/24/12.
//  Copyright (c) 2012 Safe&Quick. All rights reserved.
//
 
#import "item_info.h"
 
@implementation item_info
 
@synthesize strClass = _strClass;
@synthesize strSubClass = _strSubClass;
 
@synthesize strName = _strName;
@synthesize strPrice = _strPrice;
 
@synthesize bSelected = _bSelected;
 
- (void)dealloc
{
    [_strClassrelease];
    [_strSubClassrelease];
    
    [_strPricerelease];
    [_strName release];
    
    [super dealloc];
}
 
- (void)encodeWithCoder:(NSCoder *)aCoder
{
    [aCoder encodeObject:_strClassforKey:kClass];
    [aCoder encodeObject:_strSubClassforKey:kSubClass];
    [aCoder encodeObject:_strNameforKey:kName];
    [aCoder encodeObject:_strPriceforKey:kPrice];
    [aCoder encodeBool:_bSelected forKey:kSelected];
}
 
- (id)initWithCoder:(NSCoder *)aDecoder
{
    if (self = [super init]) {
        _strClass   = [[aDecoder decodeObjectForKey:kClass] copy];
        _strSubClass= [[aDecoder decodeObjectForKey:kSubClass] copy];
        _strName    = [[aDecoder decodeObjectForKey:kName] copy];
        _strPrice   = [[aDecoder decodeObjectForKey:kPrice] copy];
        _bSelected  = [aDecoder decodeBoolForKey:kSelected];
    }
    returnself;
}
 
- (id)copyWithZone:(NSZone *)zone
{
    item_info *item = [[[self class] allocWithZone:zone] init];
    item.strClass   = [[self.strClass copyWithZone:zone] autorelease];
    item.strSubClass= [[self.strSubClass copyWithZone:zone] autorelease];
    item.strName    = [[self.strName copyWithZone:zone] autorelease];
    item.strPrice   = [[self.strPrice copyWithZone:zone] autorelease];
 
    return item;
}
 
@end
```
 
###使用实例：

*序列化：*
```
// KeyedArchiver
NSMutableData *data = [[NSMutableData alloc] init];
NSKeyedArchiver *archiver = [[NSKeyedArchiver alloc] initForWritingWithMutableData:data];
[archiver encodeObject:_arDataSource forKey:kDataKey];
[archiver finishEncoding];
bWriteSuccess = [data writeToFile:strCaches atomically:YES];
[data release];
[archiver release];
```
 
*反序列化：*
```
NSData *data = [[NSMutableData alloc] initWithContentsOfFile:strCaches];
NSKeyedUnarchiver *unarchiver = [[NSKeyedUnarchiver alloc] initForReadingWithData:data];
        
_arDataSource = [[unarchiver decodeObjectForKey:kDataKey] retain];
[unarchiver finishDecoding];
        
[unarchiver release];
[data release];
``` 
 
 
另附数据源构造核心代码，虽然结果可能设计不太合理，希望大家提出宝贵意见：

```
- (void)initDataSource
{
    NSString *strCaches = [NSString stringWithFormat:@"%@/tmp/%@",
                           NSHomeDirectory(), kCachedFileName];
    if ([[NSFileManagerdefaultManager] fileExistsAtPath:strCaches]) {
//        _arDataSource = [[NSMutableArray arrayWithContentsOfFile:strCaches] retain];
        NSData *data = [[NSMutableData alloc] initWithContentsOfFile:strCaches];
        NSKeyedUnarchiver *unarchiver = [[NSKeyedUnarchiver alloc] initForReadingWithData:data];
        
        _arDataSource = [[unarchiver decodeObjectForKey:kDataKey] retain];
        [unarchiver finishDecoding];
        
        [unarchiver release];
        [data release];
        return;
    }
    
    if (_arDataSource == nil)
        self.arDataSource = [NSMutableArray array];
    else
        [_arDataSourceremoveAllObjects];
    
    NSString *strFilePath = [[NSBundlemainBundle] pathForResource:kShoppingListFileNameofType:@"xml"];
    if ([[NSFileManager defaultManager] fileExistsAtPath:strFilePath]) {
        NSString *strXml = [NSStringstringWithContentsOfFile:strFilePath encoding:NSUTF8StringEncodingerror:nil];
        if ([strXml length] == 0)
            return;
        
        GDataXMLDocument *doc = [[GDataXMLDocument alloc] initWithXMLString:strXml options:0 error:nil];
        if (doc == nil)
            return;
        
        NSArray *arRootChilds = [doc.rootElement children];
        for (GDataXMLElement *element in arRootChilds) {
            NSMutableArray *arTmp = [[NSMutableArray alloc] init];
            
            NSString *strClass = [[element attributeForName:@"name"] stringValue];
            NSArray *arClassChilds = [element children];
            for (GDataXMLElement *element_sub in arClassChilds) {
                NSMutableArray *arTmpSub = [[NSMutableArray alloc] init];
                
                NSString *strSubClass = [[element_sub attributeForName:@"name"] stringValue];
                
                NSArray *arSubClassChilds = [element_sub children];
                for (GDataXMLElement *element_item in arSubClassChilds) {
                    NSString *strName = [[element_item attributeForName:@"name"] stringValue];
                    NSString *strPrice = [[element_item attributeForName:@"price"] stringValue];
                    
                    item_info *item = [[item_info alloc] init];
                    item.strClass = strClass;
                    item.strSubClass = strSubClass;
                    item.strName = strName;
                    item.strPrice = strPrice;
                    item.bSelected = NO;
                    
                    [arTmpSub addObject:item];
                    [item release];
                }
                
                [arTmp addObject:arTmpSub];
                [arTmpSub release];
            }
            
            [_arDataSource addObject:arTmp];
            [arTmp release];
        }
        
        [doc release];
    } // end of fileExists
}
```
 
*kShoppingListFileName：*

```
<?xml version="1.0" encoding="UTF-8"?>
<shoping>
<class name="图书" >
<subclass name="计算机">
<item name="《C＃入门》"price=" 60.00"/>
<item name="《ios入门》"price="24.00"/>
<item name="《架构设计》"price="33.00"/>
<item name="《敏捷实战》"price="55.00"/>
<item name="《ASP.net从入门到精通》"price="56.00"/>
</subclass>
<subclass name="外语">
<item name="《商务口语》"price="9.00"/>
<item name="《朗讯辞典》"price="99.00"/>
<item name="《1天1句》"price="8.0"/>
<item name="《我爱背单词》"price="7.6"/>
<item name="《看图说话》"price="7.0"/>
</subclass>
<subclass name="教材">
<item name="《大学语文》"price="32.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
<subclass name="小说">
<item name="《美人》"price="37.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
<subclass name="励志">
<item name="《学会说NO》"price="98.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
<subclass name="动漫">
<item name="HAHAH"price="3.0"/>
<item name="《??》"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
</class>
<class name="家具">
<subclass name="床">
<item name="HAHAH"price="3.0"/>
<item name="国王大床"price="39898.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
<subclass name="桌">
<item name="小圆桌"price="98.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
<subclass name="椅">
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="太师椅"price="32.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
<subclass name="灯具">
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="熊猫台灯"price="983.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
<subclass name="门窗">
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="玻璃门"price="983.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
<subclass name="地板">
<item name="HAHAH"price="3.0"/>
<item name="楠木地板"price="9333333.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
</class>
<class name="电子设备">
<subclass name="电脑">
<item name="HAHAH"price="3.0"/>
<item name="DELL"price="3000.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
<subclass name="手机">
<item name="HAHAH"price="3.0"/>
<item name="iphone"price="3000.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
<subclass name="摄影摄像">
<item name="HAHAH"price="3.0"/>
<item name="DELL"price="3000.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
<subclass name="打印扫描">
<item name="HAHAH"price="3.0"/>
<item name="HP"price="3000.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
<item name="HAHAH"price="3.0"/>
</subclass>
</class>
</shoping>
```