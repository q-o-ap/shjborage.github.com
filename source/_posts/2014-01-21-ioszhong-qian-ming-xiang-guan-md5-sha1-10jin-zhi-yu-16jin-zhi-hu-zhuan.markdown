---
layout: post
title: "iOS中签名相关-MD5-sha1-10进制与16进制互转"
date: 2014-01-21 21:32
comments: true
categories: iOS
tags: iOS Obj-C 底层
---

费话少说，直接上代码

####md5, sha1

```
+ (NSString *)md5:(NSString *)str
{
  if ([str length] == 0)
    return @"";
  
  unsigned char result[CC_MD5_DIGEST_LENGTH];
  CC_MD5([str UTF8String], (CC_LONG)strlen([str UTF8String]) , result);
  NSMutableString *digest = [NSMutableString string];
  for (int i = 0; i < CC_MD5_DIGEST_LENGTH; i++)
    [digest appendFormat:@"%02X", result[i]];
  return [digest lowercaseString];
}

+ (NSString *)sha1:(NSString *)input
{
  if ([input length] == 0)
    return @"";
  
  const char *cstr = [input cStringUsingEncoding:NSUTF8StringEncoding];
  NSData *data = [NSData dataWithBytes:cstr length:input.length];
  
  uint8_t digest[CC_SHA1_DIGEST_LENGTH];
  
  CC_SHA1(data.bytes, data.length, digest);
  
  NSMutableString *output = [NSMutableString stringWithCapacity:CC_SHA1_DIGEST_LENGTH * 2];
  
  for(int i=0; i<CC_SHA1_DIGEST_LENGTH; i++) {
    [output appendFormat:@"%02X", digest[i]];
  }
  
  return output;
}
```

####10进制转16进制

```
NSString *num16Str = [NSString stringWithFormat:@"%x", numBase10];
```

####16进制转10进制

```
NSString *num10Str = [NSString stringWithFormat:@"%d", strtoul([numBase16 UTF8String], 0, 16)]; 
```