---
layout: post
title: "iOS应用内置AppStore以及iOS Safari内置Banner进行推广"
date: 2012-12-17 18:10
comments: true
categories: iOS
tags: iOS Obj-C
---

今天才发现在iOS6中，可以不跳转到AppStore那个应用，可以在当前应用中进行推广。
本地Document地址：
```
file:///Users/shjborage/Library/Developer/Shared/Documentation/DocSets/com.apple.adc.documentation.AppleiOS6.0.iOSLibrary.docset/Contents/Resources/Documents/index.html#documentation/StoreKit/Reference/SKITunesProductViewController_Ref/Introduction/Introduction.html#//apple_ref/doc/constant_group/Product_Dictionary_Keys
```

使用这个组件（*SKStoreProductViewController*），API相当简单。就像发短信的组件类似，可以传个ID过去，就可以在应用中使用AppStore了，不用再跳转过去。

<!--more-->

具体如下： Apple Documents

####SKStoreProductViewController Class Reference

Inherits from	

**UIViewController : UIResponder : NSObject**

Conforms to	

**NSCoding (UIViewController)**
**UIAppearanceContainer (UIViewController)**
**NSObject (NSObject)**

Framework

**/System/Library/Frameworks/StoreKit.framework**

Availability	

Available in iOS **6.0** and later.

Companion guide	

In-App Purchase Programming Guide
Declared in	**SKStoreProductViewController.h**

#####Overview

A SKStoreProductViewController object presents a store that allows the user to purchase other media from the App Store. For example, your app might display the store to allow the user to purchase another app.

To display a store, create a new SKStoreProductViewController object and set its delegate. Then, present the view controller modally from another view controller in your app. Your delegate dismisses the view controller when the user completes the purchase.

To choose a specific product, call the loadProductWithParameters:completionBlock: method, passing the iTunes item identifier for the item you want to sell.

#####Tasks

Setting a Delegate
  delegate  property
Loading a New Product Screen
```
– loadProductWithParameters:completionBlock:
```

#####Properties

delegate

The store view controller’s delegate.

```
@property(nonatomic, assign) id<SKStoreProductViewControllerDelegate> delegate
```

#####Discussion

Your application must set the delegate before presenting the store view controller.

#####Availability
Available in iOS 6.0 and later.

#####Declared In
SKStoreProductViewController.h

#####Instance Methods
*loadProductWithParameters:completionBlock:*

Loads a new product screen to display.

```
- (void)loadProductWithParameters:(NSDictionary *)parameters completionBlock:(void (^)(BOOL result, NSError *error))block
```

######Parameters

parameters

A dictionary describing the content you want the view controller to display. See “Product Dictionary Keys.”

block

A block to be called when the product information has been loaded from the App Store. The completion block is called on the main thread and receives the following 
parameters:

*result*
YES if the product information was successfully loaded, otherwise NO.
*error*
If an error occurred, this object describes the error. If the product information was successfully loaded, this value is nil.
#####Discussion
In most cases, you should load the product information and then present the view controller. However, if you load new product information while the view controller is presented, the contents of the view controller are replaced after the new product data is loaded.

#####Availability
Available in iOS 6.0 and later.
#####Declared In
SKStoreProductViewController.h
#####Constants
Product Dictionary Keys
Keys supported in the product dictionary.

```
NSString * const SKStoreProductParameterITunesItemIdentifier;
```

Constants

SKStoreProductParameterITunesItemIdentifier
The value associated with this key should be a string representing the iTunes identifier for the item you want the store to display when the view controller is presented.
Available in iOS 6.0 and later.
Declared in SKStoreProductViewController.h.
Next



另外也可以在自己的网站中，使用
具体如下就是，在Html中写Meta即可，但仅支持iOS系统，而且不能内置在应用内部。

本地Document地址：
```
file:///Users/shjborage/Library/Developer/Shared/Documentation/DocSets/com.apple.adc.documentation.AppleiOS6.0.iOSLibrary.docset/Contents/Resources/Documents/index.html#documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html
```

####Promoting Apps with Smart App Banners

Safari has a new Smart App Banner feature in iOS 6 and later that provides a standardized method of promoting apps on the App Store from a website, as shown in Figure 7-1.

Figure 7-1  A Smart App Banner of the Apple Store app

<a href="http://www.flickr.com/photos/105999540@N03/10709021953/" title="2012-12-17-1 by EricShj, on Flickr"><img src="http://farm8.staticflickr.com/7300/10709021953_e4c01baec5.jpg" width="490" height="114" alt="2012-12-17-1"></a>

Note: Smart App Banners only show on iOS, not OS X.
 
Smart App Banners vastly improve users’ browsing experience compared to other promotional methods. As banners are implemented in iOS 6, they will provide a consistent look and feel across the web that users will come to recognize. Users will trust that tapping the banner will take them to the App Store and not a third-party advertisement. They will appreciate that banners are presented unobtrusively at the top of a webpage, instead of as a full-screen ad interrupting the web content. And with a large and prominent close button, a banner is easy for users to dismiss.

If the app is already installed on a user's device, the banner intelligently changes its action, and tapping the banner will simply open the app. If the user doesn’t have your app on his device, tapping on the banner will take him to the app’s entry in the App Store. When he returns to your website, a progress bar appears in the banner, indicating how much longer the download will take to complete. When the app finishes downloading, the View button changes to an Open button, and tapping the banner will open the app while preserving the user’s context from your website.

Smart App Banners automatically determine whether the app is supported on the user’s device. If the device loading the banner does not support your app, or if your app is not available in the user's location, the banner will not display.

Implementing a Smart App Banner on Your Website
To add a Smart App Banner to your website, include the following meta tag in the head of each page where you’d like the banner to appear:


```
<meta name="apple-itunes-app" content="app-id=myAppStoreID, affiliate-data=myAffiliateData, app-argument=myURL">
```

You can include three comma-separated parameters in the content attribute:

app-id: (Required.) Your app's unique identifier. To find your app ID from the iTunes Link Maker, type the name of your app in the Search field, and select the appropriate country and media type. In the results, find your app and select iPhone App Link in the column on the right. Your app ID is the nine-digit number in between id and ?mt.

affiliate-data: (Optional.) Your iTunes affiliate string, if you are an iTunes affiliate. If you are not, find out more about becoming an iTunes affiliate at http://www.apple.com/itunes/affiliates/.

app-argument: (Optional.) A URL that provides context to your native app. If you include this, and the user has your app installed, she can jump from your website to the corresponding position in your iOS app. Typically, it is beneficial to retain navigational context because:
If the user is deep within the navigational hierarchy of your website, you can pass the document’s entire URL, and then parse it in your app to reroute her to the correct location in your app.

If the user performs a search on your website, you can pass the query string so that she can seamlessly continue the search in your app without having to retype her query.

If the user is in the midst of creating content, you can pass the session ID to download the web session state in your app so she can nondestructively resume her work.

You can generate the app-argument of each page dynamically with a server-side script. You can format it however you'd like, as long as it is a valid URL.
Note: You cannot display Smart App Banners inside of a frame.
 
###Providing Navigational Context to Your App

In your app, implement the application:openURL:sourceApplication:annotation: method in your app delegate, which fires when your app is launched from a URL. Then provide logic that can interpret the URL that you pass. The value you set to the app-argumentparameter is available as the NSURL url object.

The example in Listing 7-1 illustrates a website that passes data to a native iOS app. To accomplish this, detect if the URL contains the string/profile. If it does, then open the profile view controller and pass the profile ID number that is in the query string.

Listing 7-1  Routing the user to the correct view controller

{% codeblock %}

- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{

    // in this example, the URL from which the user came is http://example.com/profile/?12345

    // determine if the user was viewing a profile

    if ([[url path] isEqualToString:@"/profile"]) {
        // switch to profile view controller
        [self.tabBarController setSelectedViewController:profileViewController];

        // pull the profile id number found in the query string
        NSString *profileID = [url query];

        // pass profileID to profile view controller
        [profileViewController loadProfile:profileID];
    }

    return YES; }

{% endcodeblock %}
