---
author: admin
date: '2009-04-30 08:38:06'
layout: post
slug: android-get-root-in-15-image-for-adp1
status: publish
title: 'Android: Get root in 1.5 image for ADP1'
wordpress_id: '95'
categories:
- Google Android
tags:
- adp1
- android
---

One of the things that
shocked me in 1.5 is that root is not possible on the phone anymore. Even with
the ADP1 image! Following [this post](http://groups.google.com/group/Android-
DevPhone-Updating/browse_thread/thread/ad2c019d3c4d3442) on Google Groups -
Android-DevPhone-Updating the following comment was made:

> According to the Android Developers, in 1.5, root access is no longer
available from the phone itself, presumably for security reasons.

Rooted phones can still access root from adb shell.

Because of this applications which need root access won't work anymore. For
example tethering which I use very often. Luckily there is a way to get root
again in 1.5.  I read in a [blog post on
phandroid](http://phandroid.com/2009/04/29/adp-15-initial-thoughts/) that
there is actually a pretty easy way:

run adbd in root mode with: `adb.exe root`

and then do the following in "adb.exe shell":` mount -o remount,rw -t yaffs2
/dev/block/mtdblock3 /system cd /system/bin cat sh > su chmod 4755 su ` Reboot
and you can use applications again that need root. Below for example a
tethering app (aNetShare) which at least starts again without saying it needs
root. I can connect as well but somehow it doesn't let me use the internet. If
anyone knows how to get it work properly, let me know.

![anetshare](http://pboos.ch/wordpress/wp-
content/uploads/2009/04/anetshare.png)

