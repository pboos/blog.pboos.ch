---
author: admin
date: '2010-11-16 09:51:56'
layout: post
slug: useful-adb-commands
status: publish
title: Useful adb shell commands
wordpress_id: '648'
categories:
- Google Android
tags:
- activity
- adb
- android
- debugging
- intent
- shell
- stack
---

While I am developing on Android there are some commands the android shell
offers, that I just love. They are very helpful for debugging or testing some
things. Let me share my favorites with you. If you have other ones you use
often, please tell me those in the comments.

**Launch intents**
    
{% highlight bash %}

// starts the facebook app showing the inbox screen by using the Uri for that
adb shell am start -a android.intent.action.VIEW -d facebook://facebook.com/inbox
// open a vcard file from sdcard (will open contacts app :) )
adb shell am start -a android.intent.action.VIEW -d file:///sdcard/me.vcard -t text/x-vcard
// open an application to get content (in this case to get a jpeg picture)
adb shell am start -a android.intent.action.GET_CONTENT -t image/jpeg

{% endhighlight %}

Such commands in the adb shell are very usefull to launch intents. Just run
"adb shell am"  to see what other options you have and start playing around.

**Show info about running activities (I use it to see the activity stack of my app)**

{% highlight bash %}

adb shell dumpsys activity

{% endhighlight %}

This will dump out a lot of information like: Sticky broadcasts, Activity
stack, Running processes.

