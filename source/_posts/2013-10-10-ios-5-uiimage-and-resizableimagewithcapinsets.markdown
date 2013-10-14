---
layout: post
title: "iOS 5 : UIImage and resizableImageWithCapInsets"
date: 2013-10-10 23:41
comments: true
categories: iOS
---


I recently began writing a short example to learn more about the iOS 5 Appearance API and customizing UINavigationBar objects. The goal was to add a custom background, title and text to the navbar. Once I had this working, to keep a consist look across my application, I began tweaking the buttons on the navbar using the same Appearance API.
As I got further into the customization of the buttons, I ran into a method withinUIImage that was introduced in iOS 5, resizableImageWithCapInsets. I found myself getting side-tracked from the original idea of navbar look and feel, to understanding how cap insets work. This post delves into what I learned.


Cap Insets with UIButton
As the documentation describes, you use resizableImageWithCapInsets to add cap insets to an image, when the image is resized or scaled, cap areas are not affected. The best way to understand this is through an example.
Let’s assume I want all the buttons on my UI to have a similar look, a gradient with a white border. Below is the image used for the examples in this post (the button is shown on a gray backdrop so you can see the white border):

{% img http://d.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=8cf958067d3e6709ba0045fa0bfcee00/c8177f3e6709c93d5bb0aad69d3df8dcd1005454.jpg?referer=1e8577dc79899e5121990f24944a&x=.jpg %}

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

{% img http://h.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=040338663b12b31bc36ccd2cb6234747/a8773912b31bb0518f971709347adab44aede064.jpg?referer=e60131fd0afa513d08bd59ee403a&x=.jpg %}

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

{% img http://g.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=5daa3c7fdfc451daf2f60cee86c6235b/ac6eddc451da81cb7fa427ec5066d01609243154.jpg?referer=f13ad2b88026cffc303d8a827f4b&x=.jpg %}

Cap Insets with UIBarButtonItem
We can use the same image for a button on a navbar (I’ll show the specifics in the next post on customizing the navbar). Without specifying the cap insets, the button looks as follows:

{% img http://c.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=98f5931979f40ad111e4c7e6671760e2/03087bf40ad162d9605dc63d13dfa9ec8a13cd64.jpg?referer=9ebd1655e9f81a4c7f25d9f92b3a&x=.jpg %}

The code below specifies an image where 12 pixels on the top, left, bottom and right be preserved when stretching/resizing the button:
```
UIImage *backButton = [[UIImage imageNamed:@"blueButton"]     resizableImageWithCapInsets:UIEdgeInsetsMake(12, 12, 12, 12)];
```
The output nows looks as follows:

{% img http://a.hiphotos.bdimg.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=9f523ed27b310a55c024def1877e3294/aec379310a55b31983a0265441a98226cffc17b1.jpg?referer=06628c84087b020855de0ad15150&x=.jpg %}
