---
layout: post
title: "iOS Quick Tip: 7 Tips to Speed Up Your Development"
date: 2013-11-23 16:18
comments: true
categories: iOS
---

[origin](http://mobile.tutsplus.com/tutorials/iphone/ios-quick-tip-7-tips-to-speed-up-your-development/)

Most developers are always looking for ways to improve or automate their workflow being it with a simple script to automate a common task or by learning every possible keyboard shortcut of their favorite text editor. In this article, I will show you seven tips that will speed up and streamline your workflow in Xcode.

<!-- more -->

###1. Keyboard Shortcuts

Virtually every Xcode command has a keyboard shortcut that you can learn and even customize. Investing the time to learn keyboard shortcuts will increase your efficiency tremendously. If you are like me, and a lot of other developers, you prefer to keep your hands on the keyboard when you’re writing code and that is exactly what most key bindings are for.

If you don’t like the default keyboard shortcuts, then you’ll be happy to learn that you can customize Xcode’s default keyboard shortcuts in the **Preferences** window under the **Key Bindings** tab.

<a href="http://www.flickr.com/photos/105999540@N03/11006344093/" title="2013-11-23-1 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3817/11006344093_814a28a77f.jpg" width="500" height="367" alt="2013-11-23-1"></a>
iOS Quick Tip: 5 Tips to Speed Up Your Development - Managing Key Bindings in Xcode's Preferences Window 

If you don’t want to scroll through the long list of keyboard shortcuts to learn the most important ones, then I recommend browsing through this [question on Stack Overflow](http://stackoverflow.com/questions/146297/hidden-features-of-xcode). It lists the most important key bindings in Xcode and also includes a number of other neat tips and tricks.

###2. Cocoapods

Cocoapods, a great project started by Eloy Durán, has gained significant traction in the Cocoa community. What is Cocoapods? Cocoapods is a tool for managing dependencies in Xcode projects. Due to Cocoapods’ popularity, hundreds of third party libraries have been updated to support Cocoapods. Even though Cocoapods is distributed as a Ruby gem, you don’t need to understand Ruby to benefit from Cocoapods.

If you are tired of manually managing third party libraries in your iOS or OS X projects, or you’re looking for a solution that makes updating third party libraries easier, then Cocoapods is the best solution available. If you want to learn about Cocoapods, then I recommend reading [a post about Cocoapods](http://mobile.tutsplus.com/tutorials/iphone/streamlining-cocoa-development-with-cocoapods/) that I wrote earlier this year. It will get you up and running in minutes.

###3. Code Snippets

Chances are that you use a text or code snippet manager, such as **TextExpander** or **CodeBox**. I use TextExpander all the time and it has saved me countless keystrokes over the years. *I use Dash to manage my document sets and code snippets.* However, Xcode also has a snippet manager. It lives in Xcode’s right sidebar next to the Object Library.

Why should you use Xcode’s snippet manager instead of the alternatives I mentioned earlier? In Xcode, each snippet has a number of additional attributes that really make snippets powerful and flexible. Each snippet has a Platform and Language attribute as well as a completion scope. A snippet’s completion scope is especially useful and integrates neatly with Xcode’s editor. Did I mention that each snippet can have multiple placeholders?

<a href="http://www.flickr.com/photos/105999540@N03/11006222706/" title="2013-11-23-2 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7364/11006222706_6e4d20315d.jpg" width="500" height="212" alt="2013-11-23-2"></a>

iOS Quick Tip: 5 Tips to Speed Up Your Development - Creating and Managing Code Snippets is Very Easy in Xcode 

###4. Learn Git

If you’ve just started to learn how to program and the concept source control is new to you, then I strongly recommend that you take some time to learn the basics of Git. Git is [a distributed version control and source code management system](http://en.wikipedia.org/wiki/Git_(software)). Euh … what? In short, Git helps you manage your source code easily and efficiently. Combine Git with GitHub or BitBucket and you are starting to look like a professional programmer. The added benefit is that Xcode integrates nicely with Git and this integration will only improve over time.

Once you’ve learned the basics of Git, it is recommended to include a proper .gitignore file in your Xcode project. A good place to start is this question on Stack Overflow.

 
Even though Git is commonly used through the command line, there are a number of applications that provide a GUI (Graphical User Interface). My favorite is [Tower](http://www.git-tower.com/), developed by Fournova, which recently reached version 1.5. Atlassian‘s [SourceTree](https://www.atlassian.com/software/sourcetree/overview) is another great and free alternative.

###5. Xcode Behaviors

Xcode is an incredibly powerful editor and it only gets better over the years. One of the aspects that I use frequently is Xcode’s **Behaviors** feature. By defining behaviors, you can tell Xcode what it should do when a particular event occurs, such as when a crash occurs while debugging or when you’ve successfully archived an application.

You can configure these behaviors in Xcode’s Preferences Window. If you don’t like it when Xcode shows the console when output is generated, you can disable this behavior in the **Behaviors** panel. Do you want Xcode to show the **Issues Navigator** when one or more tests fail? This is all possible with Xcode Behaviors.

<a href="http://www.flickr.com/photos/105999540@N03/11006291054/" title="2013-11-23-3 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3812/11006291054_0794190295.jpg" width="500" height="367" alt="2013-11-23-3"></a>

iOS Quick Tip: 5 Tips to Speed Up Your Development - Managing Behaviors in Xcode's Preferences Window 

Xcode predefines a number of common behaviors for you, such as Build, Testing, and Running. However, you can also define custom behaviors and assign a keyboard shortcut to it. This is a convenient way to set up Xcode the way you want with a single key binding.

###6. Tabs and Windows

I really like the unified look of Xcode 4 and the integration of Interface Builder. However, it is sometimes useful to have more than one window at hand, for example, when you are working in the console and the editor at the same time or you use multiple monitors. In Xcode 4, it is still possible to work with multiple windows. You can create a new window using the keyboard shortcut `Shift + Cmd + T` or use the menu, **File > New > Window**.

Even more useful are tabs. Almost every code editor has tabs these days and Xcode is no exception. I rarely find myself in an Xcode window that has no, or one, tab. What few people know is that Xcode tabs can be named. You can even tie named tabs and Xcode behaviors together, which is especially useful during debugging.

###7. Documentation

Even if you’ve been developing Cocoa applications for years, the documentation should never be more than a click away. There are a number of ways to quickly access Xcode’s documentation browser. The solution I use most often is holding the option key and clicking a method in Xcode’s code editor. This presents you with a summary of the particular method. If you want to read more or open the documentation browser, you can click one of the links in the pop up window.

<a href="http://www.flickr.com/photos/105999540@N03/11006118705/" title="2013-11-23-4 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7341/11006118705_ee2259bfdf.jpg" width="500" height="309" alt="2013-11-23-4"></a>

iOS Quick Tip: 5 Tips to Speed Up Your Development - The Documentation is Your Friend 

You can achieve the same by opening Xcode’s right sidebar and open the Quick Help Inspector tab. The help inspector updates as the cursor moves. As with the pop up window, clicking on one of the links in the help inspector takes you to the respective section in the documentation in the documentation browser.


<a href="http://www.flickr.com/photos/105999540@N03/11006118685/" title="2013-11-23-5 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3782/11006118685_ec1975ac10.jpg" width="500" height="422" alt="2013-11-23-5"></a>

iOS Quick Tip: 5 Tips to Speed Up Your Development - The Quick Help Inspector 

One of the downsides of Xcode’s documentation browser is that it can be slow at times. For this reason, I frequently use Dash, a dedicated documentation and snippet manager. Dash is very fast and provides support for more than eighty languages, libraries, frameworks, and APIs. It is well worth checking out.