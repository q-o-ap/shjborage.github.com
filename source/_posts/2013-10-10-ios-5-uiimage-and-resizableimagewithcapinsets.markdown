---
layout: post
title: "iOS 5 : UIImage and resizableImageWithCapInsets"
date: 2013-10-10 23:41
comments: true
categories: iOS
---


I recently began writing a short example to learn more about the iOS 5 Appearance API and customizing UINavigationBar objects. The goal was to add a custom background, title and text to the navbar. Once I had this working, to keep a consist look across my application, I began tweaking the buttons on the navbar using the same Appearance API.
As I got further into the customization of the buttons, I ran into a method withinUIImage that was introduced in iOS 5, resizableImageWithCapInsets. I found myself getting side-tracked from the original idea of navbar look and feel, to understanding how cap insets work. This post delves into what I learned.

<!-- more -->

Cap Insets with UIButton
As the documentation describes, you use resizableImageWithCapInsets to add cap insets to an image, when the image is resized or scaled, cap areas are not affected. The best way to understand this is through an example.
Let’s assume I want all the buttons on my UI to have a similar look, a gradient with a white border. Below is the image used for the examples in this post (the button is shown on a gray backdrop so you can scxxx[
]]]ee the white border):

<a href="http://www.flickr.com/photos/105999540@N03/10374761354/" title="2013-10-10-1 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3766/10374761354_bdb27d747b_o.gif" width="121" height="60" alt="2013-10-10-1"></a>

Depending on the context of where the button appears, its size may vary. The code to create a button with the image and the corresponding output follow:
{% codeblock %}
UIButton *button = [[UIButton alloc] initWithFrame:CGRectMake(80, 130, 160, 44)];  
[button setTitle:@"Test Button" forState:UIControlStateNormal]; 

// Image with without cap insets
UIImage *buttonImage = [UIImage imageNamed:@"blueButton"];   

[button addTarget:self action:@selector(buttonPressed:) forControlEvents: UIControlEventTouchUpInside];
[button setBackgroundImage:buttonImage forState:UIControlStateNormal];
[[self view] addSubview:button];
{% endcodeblock %}

<a href="http://www.flickr.com/photos/105999540@N03/10374761294/" title="2013-10-10-2 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7447/10374761294_389a1d7a17_o.gif" width="238" height="76" alt="2013-10-10-2"></a>

As you can see, the button is stretched in all directions. Let’s change the code to include cap insets, however, before we do that, let’s look at the signature of the cap insets method:

```
- (UIImage *)resizableImageWithCapInsets:(UIEdgeInsets)capInsets
```

Looking on step further, UIEdgeInserts is defined as:
```
typedef struct {  
	 CGFloat top, left, bottom, right;
} UIEdgeInsets;
```

UIEdgeInsets is structure that specifies float values for each cap inset: top, left, bottom and right areas of an image. To apply this to the image for the button, here is all we need to do:
```
// Image with cap insets
UIImage *buttonImage = [[UIImage imageNamed:@"blueButton"]     resizableImageWithCapInsets:UIEdgeInsetsMake(0, 16, 0, 16)];
```

This requests that the left and right 16 pixels of the original image are not scaled or resized when stretching the image to accomodate the button size frame defined above. The end results is as shown below:

<a href="http://www.flickr.com/photos/105999540@N03/10374785416/" title="2013-10-10-3 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3834/10374785416_12ea31ec14_o.gif" width="239" height="76" alt="2013-10-10-3"></a>

Cap Insets with UIBarButtonItem
We can use the same image for a button on a navbar (I’ll show the specifics in the next post on customizing the navbar). Without specifying the cap insets, the button looks as follows:

<a href="http://www.flickr.com/photos/105999540@N03/10374968953/" title="2013-10-10-4 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7358/10374968953_23c450bb11_o.gif" width="340" height="80" alt="2013-10-10-4"></a>

The code below specifies an image where 12 pixels on the top, left, bottom and right be preserved when stretching/resizing the button:
```
UIImage *backButton = [[UIImage imageNamed:@"blueButton"]     resizableImageWithCapInsets:UIEdgeInsetsMake(12, 12, 12, 12)];
```
The output nows looks as follows:

<a href="http://www.flickr.com/photos/105999540@N03/10374785266/" title="2013-10-10-5 by EricShj, on Flickr"><img src="http://farm4.staticflickr.com/3816/10374785266_7b83f64381_o.gif" width="340" height="80" alt="2013-10-10-5"></a>
