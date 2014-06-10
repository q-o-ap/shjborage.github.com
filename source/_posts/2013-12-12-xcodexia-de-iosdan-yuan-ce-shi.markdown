---
layout: post
title: "XCode下的iOS单元测试"
date: 2013-12-12 09:34
comments: true
categories: iOS
tags: iOS Obj-C 单元测试
---

XCode 内置了 OCUnit 单元测试框架，但目前最好用的测试框架应该是 GHUnit。通过 GHUnit + OCMock 组合，我们可以在 iOS 下进行较强大的单元测试功能。本文将演示如何在 XCode 4.2 下使用 OCUnit， GHUnit 和 OCMock 进行单元测试。

<!-- more -->

###OCUnit

在 XCode 下新建一个 OCUnitProject 工程，选中 Include Unit Tests 选择框，

<a href="http://www.flickr.com/photos/shjborage/11332017455/" title="2013-12-12-1 by EricShj, on Flickr"><img src="http://farm3.staticflickr.com/2805/11332017455_a9679dfeb5.jpg" width="500" height="337" alt="2013-12-12-1"></a>

OCUnit 框架则会为我们自动添加 Unit Test 框架代码：

<a href="http://www.flickr.com/photos/shjborage/11332111874/" title="2013-12-12-2 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5538/11332111874_82dae3bc52.jpg" width="430" height="381" alt="2013-12-12-2"></a>

XCode 在 OCUnitProjectTests.m 中为我们自动生成了一个 Fail 的测试：

```
- (void)testExample
{
    STFail(@"Unit tests are not implemented yet in OCUnitProjectTests");
}
```

让我们来运行 Test，看看效果：

<a href="http://www.flickr.com/photos/shjborage/11332085666/" title="2013-12-12-3 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7321/11332085666_f398e1edd2.jpg" width="500" height="269" alt="2013-12-12-3"></a>


从图中的红色下划线部分可以看出，测试没有通过，符合预期。我们只要像类 OCUnitProjectTests 一样编写继承自 SenTestCase 类的子类，在其中添加形式如：- (void) testXXX(); 的测试函数既可，注意必须是一个无参无返回类型且名称是以 test 为前缀的函数。
OCUnit 的有点是官方支持，于 XCode 集成的比较好。

###GHUnit

GHUnit 是一个开源的单元测试框架，具有可视化界面，功能亦相当强大。Mark 写了一篇 OCUnit vs GHUnit 的文章，有兴趣的童鞋可以看一看。

OCMock 是由 Mulle Kybernetik 为 OS X 和 iOS 平台编写的遵循 mock object 理念的单元测试框架。


####下面来介绍如何配置 GHUnit 和 OCMock

*	1，首先，创建一个名为 GHUnitProject 的单视图应用程序，注意：不要选中 Include Unit Tests 选择框。然后运行，应该出现白屏。
*	2，添加新的 test target，选中左边的工程名，点击右侧的 Add Target，新增一个名为 Tests 的 Empty Application 应用程序，让其附属于 GHUnitProject注意：不要选中 Include Unit Tests 选择框。

<a href="http://www.flickr.com/photos/shjborage/11332017205/" title="2013-12-12-4 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5477/11332017205_d39c81a057.jpg" width="500" height="346" alt="2013-12-12-4"></a>
<a href="http://www.flickr.com/photos/shjborage/11332017075/" title="2013-12-12-5 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7313/11332017075_e828e5525f.jpg" width="500" height="419" alt="2013-12-12-5"></a>


*	3，向 Tests 工程中（注意是 Tests 工程）添加 GHUnitIOS Framework。首先下载与 XCode 版本对应的 GHUnitIOS Framework。英文好的可以直接查看官方 iOS 版的安装文档：点此查看，跳过此第 3 节；否则请接着看。
*	3.1，解压 GHUnitIOS 框架到 GHUnitProject 下，让 GHUnitIOS.framework 与 Tests 在同一目录下。
*	3.2，回到 XCode，右击工程中的 Frameworks group，选中 Add Files to...菜单，选取 GHUnitIOS.framework ，注意 targets 要选择 Tests。

<a href="http://www.flickr.com/photos/shjborage/11332111384/" title="2013-12-12-6 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7352/11332111384_e75ae2182a.jpg" width="478" height="500" alt="2013-12-12-6"></a>


*	3.3，设置 Tests 的 Build Settings：在 Other Linker Flags 中增加两个 flag： -ObjC 和 -all_load。

<a href="http://www.flickr.com/photos/shjborage/11332016815/" title="2013-12-12-7 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3811/11332016815_c8ba09db5d.jpg" width="500" height="316" alt="2013-12-12-7"></a>


*	3.1，删除 Tests 工程中的 UTSAppDelegate.h 和 UTSAppDelegate.m 两个文件；
*	3.2，修改 Tests 工程中的 main.m 为:

```
#import <UIKit/UIKit.h>
#import <GHUnitIOS/GHUnitIOSAppDelegate.h>
int main(int argc, char *argv[])
{
    @autoreleasepool {
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([GHUnitIOSAppDelegate class]));
    }
}
```

3.3，选择编译目标 Tests>iPhone 5.0 Simulator，编译运行，应该能得到如下效果。目前我们还没有编写任何实际测试，所以列表为空。

<a href="http://www.flickr.com/photos/shjborage/11332085166/" title="2013-12-12-8 by EricShj, on Flickr"><img src="http://farm3.staticflickr.com/2872/11332085166_39da2998f9.jpg" width="331" height="492" alt="2013-12-12-8"></a>

4，编写 GHUnit 测试。向 Tests 工程中添加名为 GHUnitSampleTest 的 Objective C class。其内容如下：

```
GHUnitSampleTest.h
#import <GHUnitIOS/GHUnit.h>
@interface GHUnitSampleTest: GHTestCase
{
}
@end
GHUnitSampleTest.m
#import "GHUnitSampleTest.h"
@implementation GHUnitSampleTest
- (void)testStrings
{
    NSString *string1 = @"a string";
    GHTestLog(@"I can log to the GHUnit test console: %@", string1);
    // Assert string1 is not NULL, with no custom error description
    GHAssertNotNULL(string1, nil);
    // Assert equal objects, add custom error description
    NSString *string2 = @"a string";
    GHAssertEqualObjects(string1, string2, @"A custom error message. string1 should be equal to: %@.", string2);
}
@end
```

然后编译运行，点击 Run，效果如下：

<a href="http://www.flickr.com/photos/shjborage/11332159843/" title="2013-12-12-9 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3788/11332159843_1766364257.jpg" width="322" height="488" alt="2013-12-12-9"></a>

图中的 All 栏显示所以的测试，Failed 栏显示没有通过的测试。强大吧，GHUnit。你可以向 GHUnitSampleTest 添加新的测试，比如：

```
- (void)testSimpleFail
{
    GHAssertTrue(NO, nil);
}
```

我们可以向 Tests 添加更多测试类，只要该类是继承自 GHTestCase，且其中的测试方法都是无参无返回值且方法名字是以 test 为前缀即可。

###OCMock

OCMock一般用来模拟一个类，创建其对象，并设置该对象。
下面我们来添加 OCMock。

*	1，我们只能以静态库的方式来添加 OCMock。在 GHUnitTest 目录下新建 Libraries 目录，该目录是与 Tests 目录平级的。下载静态库文件，解压头文件至该目录下。
文件下载：头文件 libOCMock.a ，framework 文件：OCMock framework ，打开下载好的ocmock-1.77.dmg，拷贝其中的‘Release/Library/Headers/OCMock’ 目录至 Libraries 下。最终目录结构如下：

<a href="http://www.flickr.com/photos/shjborage/11332084876/" title="2013-12-12-10 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3782/11332084876_6f67df9eef.jpg" width="500" height="96" alt="2013-12-12-10"></a>

*	2，在 GHUnitTest 工程中新建名为 Libraries 的 group，导入libOCMock.a 和目录 OCMock，注意 target 是 Tests。

<a href="http://www.flickr.com/photos/shjborage/11332084976/" title="2013-12-12-11 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3751/11332084976_15c36cbd88.jpg" width="479" height="500" alt="2013-12-12-11"></a>


*	3，设置 Tests 的 Build Setting。让 Libray Search Paths 包含 $(SRCROOT)/Libraries：

<a href="http://www.flickr.com/photos/shjborage/11332016595/" title="2013-12-12-12 by EricShj, on Flickr"><img src="http://farm3.staticflickr.com/2879/11332016595_f7fae82fb0.jpg" width="500" height="245" alt="2013-12-12-12"></a>


在 Header Search Paths 中增加 $(SRCROOT)/Libraries，并选中 Recursive 选择框。

<a href="http://www.flickr.com/photos/shjborage/11332084796/" title="2013-12-12-13 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7320/11332084796_32d4115828.jpg" width="500" height="277" alt="2013-12-12-13"></a>

*	4，编写 OCMock 测试。向 Tests 工程中添加名为 OCMockSampleTest 的 Objective C class。其内容如下：

```
OCMockSampleTest.h
#import <GHUnitIOS/GHUnit.h>
@interface OCMockSampleTest : GHTestCase
@end
OCMockSampleTest.m
#import "OCMockSampleTest.h"
#import <OCMock/OCMock.h>
@implementation OCMockSampleTest
// simple test to ensure building, linking,
// and running test case works in the project
- (void)testOCMockPass
{
    id mock = [OCMockObject mockForClass:NSString.class];
    [[[mock stub] andReturn:@"mocktest"] lowercaseString];
    NSString *returnValue = [mock lowercaseString];
    GHAssertEqualObjects(@"mocktest", returnValue,
    @"Should have returned the expected string.");
}
- (void)testOCMockFail
{
    id mock = [OCMockObject mockForClass:NSString.class];
    [[[mock stub] andReturn:@"mocktest"] lowercaseString];
    NSString *returnValue = [mock lowercaseString];
    GHAssertEqualObjects(@"thisIsTheWrongValueToCheck",
    returnValue, @"Should have returned the expected string.");
}
@end
```

编译运行，点击 Run，效果如下图。

<a href="http://www.flickr.com/photos/shjborage/11332016435/" title="2013-12-12-14 by EricShj, on Flickr"><img src="http://farm6.staticflickr.com/5507/11332016435_6afa1b6912.jpg" width="322" height="480" alt="2013-12-12-14"></a>

至此，iOS 下的OCUnit，GHUnit，OCMock 单元测试介绍就到此结束了。当然还有其他一些测试框架，比如 google 出品的 GTM。

参考资料：

OCMock: http://ocmock.org/ 

Unit Testing in Xcode 4- use OCUnit and SenTest instead of GHUnit

GHUnit Reference: http://gabriel.github.com/gh-unit/

转自：http://www.uml.org.cn/mobiledev/201201093.asp