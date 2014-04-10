---
layout: post
title: "Synergy on Mavericks (OSX 10.9) and enable assistive devices"
date: 2013-12-26 10:42
comments: true
categories: Mac
---

[Origin](http://superuser.com/questions/656912/synergy-on-mavericks-osx-10-9-and-enable-assistive-devices)

###Question

Mavericks handles accessibility on a per-application basis, so instead of enabling assistive devices (option does not even exist) carte-blanche, the app which makes the request shows up in a list in System Preferences > Security & Privacy > Privacy where you can enable the access it's looking for.

Synergy, however, makes a check for the old style of assistive devices setting and fails to start.

<!-- more -->

###Answer

From a terminal:

```
sudo open /Applications/Synergy.app/Contents/MacOS/Synergy
```

Then open OSX *System Preferences* and click **Security and Privacy**, then **Accessibility**

Check the checkbox on for **Terminal** and **Synergy**.

You can then close Synergy and start it again in the background through the terminal:

```
/Applications/Synergy.app/Contents/MacOS/Synergy &
```

I expect this will be fixed in the next release (1.4.16 Beta)

[Source](http://synergy-foss.org/osqa/questions/1999/error-1410-on-mac-1081)

