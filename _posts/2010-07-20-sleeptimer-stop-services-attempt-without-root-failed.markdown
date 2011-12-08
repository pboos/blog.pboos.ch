---
author: admin
date: '2010-07-20 15:53:22'
layout: post
slug: sleeptimer-stop-services-attempt-without-root-failed
status: publish
title: 'SleepTimer: Stop Services attempt without root [failed]'
wordpress_id: '571'
categories:
- Google Android
tags:
- android
- forcestoppackage
- froyo
- java
- permission
- restartpackage
- sleeptimer
---

Problem: With Android 2.2 (Froyo) packages can't be force closed through
restartPackage() anymore. Exactly this is what SleepTimer relied on to turn
off the different Services which are running music.

Thanks to Corsin Camichel I found an example on how Google force closes
packages in Android 2.2 (Froyo). This happens over an internal API. Method
name is: forceStopPackage(String pkgName).

So I tried my luck with reflection.

{% highlight java %}

Class c = Class.forName("android.app.ActivityManagerNative");
Method getDefaultMethod = c.getMethod("getDefault");
getDefaultMethod.setAccessible(true);
Object nativeManager = getDefaultMethod.invoke(null);
c = nativeManager.getClass();
Method forceStopPackageMethod = c.getMethod("forceStopPackage", String.class);
forceStopPackageMethod.setAccessible(true);
forceStopPackageMethod.invoke(nativeManager, pkgName);

{% endhighlight %}

But I get an exception when calling the last line.

> java.lang.SecurityException: Permission Denial: forceStopPackage() from
pid=1965, uid=10075 requires android.permission.FORCE_STOP_PACKAGES

I tried adding it to the AndroidManifest.xml. But without luck.

If somebody figures out how to get this working, I would be happy for a
message! :) Or an e-mail from Google how to get this working would be nice.

_Update:_

According to velazcod and rac2030 it is not possible to get the required
permissions for this :(

> The only problem is that in order to use this method, you need the
“android.permission.FORCE_STOP_PACKAGES” permission, and that permission
requires a “signature” protectionLevel, so the system will not grant it unless
the app is signed with the same signature as the system
![:(](http://pboos.ch/wordpress/wp-includes/images/smilies/icon_sad.gif)

[http://pastebin.com/XYc299Uz](http://pastebin.com/XYc299Uz)

