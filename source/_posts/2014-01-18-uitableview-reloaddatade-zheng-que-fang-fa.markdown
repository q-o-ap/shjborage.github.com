---
layout: post
title: "UITableView reloadData的正确方法"
date: 2012-02-08 12:04
comments: true
categories: iOS
---

相信很多人会遇到这种情况，当tableView正在滚动的时候，如果reloadData，偶尔发生App crash的情况。 这种情况有时候有，有时候没有，已经难倒了很多人。直至今天，我在stackoverflow上面，仍没有发现真正有说到其本质的帖子。我的处女贴，选择这个问题来阐述一下我的观点。  

小弟我英语很好，一般都是用英语记笔记，当然，我知道，论坛愤青很多，如果只贴英文出来，肯定找骂。 故简单翻译一下，以显示我的诚意。 原英文笔记附在后面。 请大家不要挑英语语法错误了，笔记就是笔记，不是出书。 

<!-- more -->

###第一句话，阐述问题的本质：

在tableView的dataSource被改变 和 tableView的reloadData被调用之间有个时间差，而正是在这个期间，tableView的delegate方法被调用，如果新的dataSource的count小于原来的dataSource count，crash就很有可能发生了。

下面的笔记提供了两种解决方案，和记录了一个典型的错误,即 在background thread中修改了datasource，虽然调用`[self.tableView performSelectorOnMainThread:@selector(reloadData) withObject:nilwaitUntilDone:NO];`

####记住正确的原则： 

```
Always change the dataSource and（注意这个and) reloadData in the mainThread. What's more, reloadData should be called immediately after the dataSource change. 
If dataSource is changed but tableView's reloadData method is not called immediately, the tableView may crash if it's in scrolling. 
Crash Reason: There is still a time gap between the dataSource change and reloadData. If the table is scrolling during the time gap, the app may Crash!!!!
```

####WRONG WAY: 

```
Following codes is WRONG: even the reloadData is called in main thread, there is still a time gap between the dataSource change and reloadData. If the table is scrolling during the time gap, the app may Crash!!!!
```

####wrong codes samples: 

```
-(void) changeDatasource_backgroundThread
{
	@autoreleasepool{
		[self.dataSourceArray removeAllObjects]; 
		[self.tableViewperformSelectorOnMainThread:@selector(reloadData) withObject:nil waitUntilDone:NO];
    }
}
```


####RIGHT WAY: 

Principle:  Always change dataSource in MAIN thread and call the reloadData immediately after it. 

#####Option 1:

If the operation to change the dataSource should be executed in background, the operation can create a temp dataSource array and pass it to main thread with notification, the main thread observes the notification,  assign the tmpDataSource to dataSource and reload the tableView by reloadData.

####Option 2: 

In the background, call the GDC dispatch_async to send the two methods to main thread together.

```
dispatch_async(dispatch_get_main_queue(), ^{
        self.dataSourceArray= a new Array.
        [self.tableView reloadData];
});
```

####个人的第三个解决方案

在reloadData前，把dataSource和delegate设置为nil，reloadData前再设置回来

PS:
其实我的第三个解决方案基本就是人家提供的第一种。
这种方案还是没解决问题，有时还是会Crash。
原文里说的第二种方法看起来倒是很靠谱，把新数据单独放一份，准备好再一起丢给主线程。

