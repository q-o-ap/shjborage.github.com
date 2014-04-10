---
layout: post
title: "iCloud 学习笔记"
date: 2012-09-20 17:47
comments: true
categories: iOS
---

1.准备新的mobileprovision,不能用通配符，并打开iCloud功能，然后在工程里Summary中选中iCloud，并编辑entitlements文件。    
2.在后台线程中执行`NSURL *urlUC = [[NSFileManager defaultManager] URLForUbiquityContainerIdentifier:nil];` 注意只有真机才行，模拟器不行。

测试环境，打开网页：https://developer.icloud.com/#documents

<!-- more -->

大概的测试代码如下：

```
- (void)getUbiquityContainerUrl
{
    NSURL *urlBase = [[NSFileManagerdefaultManager] URLForUbiquityContainerIdentifier:nil];
   if (urlBase == nil)
        return; // 未激活iCloud或模拟器环境

    NSDirectoryEnumerator *dirEnumerater = [[NSFileManagerdefaultManager] enumeratorAtPath:[selfgetDefaultDir]];
NSString *filePath = nil;
while((filePath = [dirEnumerater nextObject]) != nil) {
        NSRange rPosPath = [filePath rangeOfString:@"/"];
        if (rPosPath.length == 0) {
            NSURL *urlFile = [NSURL URLWithString:filePath relativeToURL:urlBase];
            
            // 判断iCloud里该文件是否存在
            if ([[NSFileManagerdefaultManager] isUbiquitousItemAtURL:urlFile]) {
                // 存在则修改
                if (![[NSStringstringWithContentsOfFile:[[selfgetDefaultDir] stringByAppendingPathComponent:filePath] encoding:NSUTF8StringEncodingerror:nil] writeToURL:urlFile atomically:YES])
                {
                    SQTRACE(@"存在则修改");
                }
                
                SQTRACE(@"上传成功");
                
                continue;
            }
            
            // 上传至iCloud
            // 指定本地文件完整路径
            NSURL *url = [NSURLfileURLWithPath:[[selfgetDefaultDir] stringByAppendingPathComponent:filePath]];
            NSError *error;
            // 官方文档建议本方法不要在主进程里执行
            if (![[NSFileManagerdefaultManager] setUbiquitous:YESitemAtURL:url destinationURL:urlFile error:&error])
            {
                NSLog(@"setUbiquitous error %@,\n%@", error, [error userInfo]);
                SQTRACE(@"上传失败");
                continue;
            } else {
                SQTRACE(@"上传成功");
            }// end of iCloud upload
        }
    } // end of while dir enumerator
}
 
- (void)testCloud
{
    [selfperformSelectorInBackground:@selector(getUbiquityContainerUrl) withObject:nil];
}
 
// 此方法是官方文档提供,用来检查文件状态并下载
- (BOOL)downloadFileIfNotAvailable:(NSURL*)file
{
    NSNumber*  isIniCloud = nil;
    
    if ([file getResourceValue:&isIniCloud forKey:NSURLIsUbiquitousItemKey error:nil]) {
        // If the item is in iCloud, see if it is downloaded.
        if ([isIniCloud boolValue]) {
            NSNumber*  isDownloaded = nil;
            if ([file getResourceValue:&isDownloaded forKey:NSURLUbiquitousItemIsDownloadedKey error:nil]) {
                if ([isDownloaded boolValue])
                    return YES;
                
                // Download the file.
                NSFileManager*  fm = [NSFileManager defaultManager];
                if (![fm startDownloadingUbiquitousItemAtURL:file error:nil]) {
                    return NO;
                }
                return YES;
            }
        }
    }
    
    // Return YES as long as an explicit download was not started.
    returnYES;
}
```

###如何忽略Documents文件夹里面的某些文件或文件夹：
(Documents中设置某些文件不去同步方法)

Documents路径里的文件会被默认同步到iCloud中（如果启用了iCloud功能）
Documents路径里，文件夹名以.nosync结尾的，将不被同步
如: Documents/mydata.nosync/ 该路径下的东西不被同步
Using Core Data with iCloud Release Notes 里有部分提到

