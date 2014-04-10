---
layout: post
title: "Auto Layout by Example"
date: 2014-01-18 16:21
comments: true
categories: iOS
---

Autolayout 博大精深，以前转过一篇，可是用的时候还是不会用。没办法只能看苹果官方的文档，发现除了讲基本的使用（XCode5中的一些操作已不太一样）外，还有这么个例子，主要解决了屏幕大小不同适配的问题，真是不错。摘录下来，希望对大家有用。 
 
如果某个地方看不明白可以留言给我，我翻译并给大家讲。  

ScrollView这块我暂时没有用，没细看，下面Size适配那个我试了，并且写了Demo，大家感兴趣下文下载研究。


Auto Layout makes it easy to solve many complex layout problems automatically, without the need for manual view manipulation. By creating the right combination of constraints, you can create layouts that are traditionally difficult to manage in code, such as equally spaced views that adjust to changes in orientation or size, elements inside scroll views that affect the size of the scrolling content, or elements inside scroll views that don’t scroll with the rest of the contents.

<!-- more -->

###Using Scroll Views with Auto Layout

When you are creating an app using Auto Layout, scroll views can present a unique challenge. The size of the scrolling content must be set correctly so that the user can scroll through all of the available content, for example, and if you need to lock a contextual view in place on top of a scroll view, such as the scale and legend for a map, it’s difficult to ensure that the element doesn’t scroll with the rest of the content.

####Controlling Scroll View Content Size
The size of the content inside of a scroll view is determined by the constraints of its descendants.

*To set the size of a scroll view*

*	Create the scroll view.
*	Place the UI element inside it.
*	Create constraints that fully define the width and height of the scroll view content.

You must make sure you create constraints for all the subviews inside a scroll view. For example, when defining the constraints for a view that doesn’t have an intrinsic content size, you’ll need more than just a leading edge constraint—you must also create trailing edge, width, and height constraints. There cannot be any missing constraints, starting from one edge of the scroll view to the other.

####Creating Anchored Views Inside a Scroll View

You may find you want to create an area inside a scroll view that doesn’t move when a user scrolls the contents of the scroll view. You accomplish this by using a separate **container view**.

*To lock a view inside a scroll view*
*	Create a container view to hold the scroll view.
*	Create the scroll view and place it in the container view with all edges equal to zero points.
*	Create and place a subview inside of the scroll view.
*	Create constraints from the subview to the container view.

The following example uses the steps in the above task to show how to position a text view inside of a scroll view. In this example, the text view stays at the bottom of the scroll view and doesn’t move when the scroll view contents are moved.

First, create the container view that will contain the scroll view. Set the size of the container view equal to the desired size of the scroll view.

<iframe src="https://www.flickr.com/photos/shjborage/12008276023/player/b1ddbeff09" height="500" width="291"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

After the container view is created, create a scroll view and place it inside of the container view. Resize the scroll view so that all of the edges are flush with the container view’s edges, by setting the distance to 0.

<iframe src="https://www.flickr.com/photos/shjborage/12008810786/player/1202126aa8" height="500" width="290"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

Create another view and place it inside of the scroll view. In this example, a text view is placed inside of the scroll view.

<iframe src="https://www.flickr.com/photos/shjborage/12007999315/player/ddc38c3ea6" height="500" width="288"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

After placing the text view, create constraints from the text view to the container view. Creating constraints that anchor the text view to the container view (skipping the scroll view) anchors the text view relative to the container view, which ensures that the scroll view won’t scroll the text view.

To create a constraint that crosses multiple views in the view hierarchy, it is generally easier to Control-drag from the view to the container view in the Interface Builder outline view.

<iframe src="https://www.flickr.com/photos/shjborage/12008810736/player/e32e620ff4" height="298" width="270"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

In the constraint overlay that appears, set the required constraints for the view.

<iframe src="https://www.flickr.com/photos/shjborage/12008275923/player/194c48615c" height="273" width="158"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

In this example, constraints are created from the leading, trailing, and bottom edges of the text view to the container view. The height of the text view is also constrained.

<iframe src="https://www.flickr.com/photos/shjborage/12008810686/player/bdc0cae834" height="500" width="287"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

The following two figures show the app in iOS Simulator, both in normal and landscape positions. The text view is constrained at the bottom of the scroll view and doesn’t move as the scroll view is moved.

<iframe src="https://www.flickr.com/photos/shjborage/12007999185/player/a051895080" height="296" width="495"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

<iframe src="https://www.flickr.com/photos/shjborage/12007999135/player/4f87b36fa7" height="495" width="296"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>


###Spacing and Wrapping

Auto Layout provides several techniques for automatically spacing views and resizing items based on their content. The following sections describe how to create constraints that keep visible views proportionally spaced based on the orientation of the device.

####Creating Equal Spacing Between Views

To lay out several views that are proportionally spaced based on the orientation of a device, create spacer views between the visible views. Set the constraints of these spacer views correctly to ensure that the visible views are able to stay spaced apart based on the orientation of the device.

*To space views proportionally*
*	Create the visible views.
*	Create the spacer views equal to the number of visible views plus one.
*	Alternate placing your views, starting with a spacer view.
	To space two visible views, place all of the views in the following pattern, starting from the left side of the screen and moving right:

`spacer1 | view1 | spacer2 | view2 | spacer3.`

*	Constrain the spacer views so that their lengths are equal to each other.

`
Note: The height of the spacer views can be any value, including 0. However, you must create constraints for the height of the views—don’t leave the height ambiguous.
Create a leading constraint from the first spacer view to the container view.
`

*	Create a trailing constraint from the last spacer view to the container view.
*	Create constraints between the spacer views and the visible views.

`
Note: When spacing views vertically, start from the top of the screen and place each view below the previous view. Set the heights of the spacer views equal to each other.
The following example uses the steps in the above task to show how to position two views proportionally spaced. The spacer views are annotated for the example, but are normally left empty with no background. First, create the two views and place them in the storyboard.
`

<iframe src="https://www.flickr.com/photos/shjborage/12008351964/player/6cff17637e" height="310" width="236"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

Add the three spacer views—one to the left of the leftmost view, one between the two views, and one to the right of the rightmost view. The spacer views don’t have to be the same size at this time because their size will be set through constraints.

<iframe src="https://www.flickr.com/photos/shjborage/12008275823/player/bdda53d7d0" height="310" width="235"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

Create the following constraints for the spacer views:

*	Constrain the width of spacer view 2 and spacer view 3 to be equal to the width of spacer view 1.
*	Constrain the width of spacer view 1 to be greater than or equal to the minimum desired width.
*	Create a Leading Space to Container constraint from spacer view 1 to the container.
*	Create a Horizontal Spacing constraint from spacer view 1 to view 1. Set this constraint to be a less-than-or-equal-to constraint with a priority of 1000.
*	Create Horizontal Spacing constraints from spacer view 2 to view 1 and view 2. Set these constraints to be a less-than-or-equal-to constraint with a priority of 999.
*	Create a Horizontal Spacing constraint from spacer view 3 to view 2. Set this constraint to be a less-than-or-equal-to constraint with a priority of 1000.
*	Create a Trailing Space to Container constraint from spacer view 3 to the container.

These constraints create two visible views and three invisible views (spacer views). These spacer views automatically resize as the orientation of the device changes, keeping the visible views proportionally spaced, as shown in the following two figures:

<iframe src="https://www.flickr.com/photos/shjborage/12008275833/player/7290ca4711" height="337" width="261"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

<iframe src="https://www.flickr.com/photos/shjborage/12008351904/player/a2b7a82a10" height="261" width="337"  frameborder="0" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

[Demo](https://www.dropbox.com/s/23vrbep9khfyvb6/AutoLayoutDemo.zip)

###Animating Changes Made by Auto Layout

If you need full control over animating changes made by Auto Layout, you must make your constraint changes programmatically. The basic concept is the same for both iOS and OS X, but there are a few minor differences.

In an iOS app, your code would look something like the following:

```
[containerView layoutSubtreeIfNeeded]; // Ensures that all pending layout operations have been completed
[UIView animateWithDuration:1.0 animations:^{
     // Make all constraint changes here
     [containerView layoutSubtreeIfNeeded]; // Forces the layout of the subtree animation block and then captures all of the frame changes
}];
```

In OS X, use the following code when using layer-backed animations:

```
[containterView layoutIfNeeded];
NSAnimationContext runAnimationGroup:^(NSAnimationContext *context) {
     [context setAllowsImplicitAnimation: YES];
     // Make all constraint changes here
     [containerView layoutIfNeeded];
}];
```

When you aren’t using layer-backed animations, you must animate the constant using the constraint’s animator:

```
[[constraint animator] setConstant:42];
```


###个人总结

1. 所有相关Contraints都加好后再自动让XCode补全，或者干脆在没问题的时候忽略警告
2. `Constrain the width of spacer view 2 and spacer view 3 to be equal to the width of spacer view 1`. 这意思是让三个视频的width相等，并不是给他们三个都加上width的Contraints。
3. 最后是在3.5寸屏幕下开发，再适应4寸，这样直接拉开。（在开发时，点那个大小切换那个可能会带来Contraints警告，要小心。不行就Commond+Z）