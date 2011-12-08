---
author: admin
date: '2010-11-16 09:38:43'
layout: post
slug: android-reverse-engineering
status: publish
title: Android Reverse Engineering
wordpress_id: '639'
categories:
- Google Android
- Software Development
tags:
- android
- engineering
- hacking
- reverse
- skype
---

Why would you want to reverse engineer a android application? Well there are
different reasons:

  * To find out how the other apps work 
    * To see how they behave
    * How you can send Intents to those Apps to open certain Activities from your own app (did that with the Facebook app)
    * To know how you could do something in your own app.
  * To change other apps to your liking 
    * This is how they changed Skype to remove the 3G blocking in the US.
    * Add localization
    * Change images
    * ...

Please do not use the info found here for illegal purposes. Respect the work
of people who release good apps. Even if they cost! They need to get payed
too!

**What will we use?**  
We use [apktool](http://code.google.com/p/android-apktool/) to do the tasks of
getting the original (or nearly original) files and to rebuild the app after
we did changes. Here info about apktool form the original site.

> It is a tool for reengineering 3rd party, closed, binary Android apps. It
can decode resources to nearly original form and rebuild them after making
some modifications; it makes possible to debug smali code step by step. Also
it makes working with app easier because of project-like files structure and
automation of some repetitive tasks like building apk, etc.

It is NOT intended for piracy and other non-legal uses. It could be used for
localizing, adding some features or support for custom platforms and other
GOOD purposes. Just try to be fair with authors of an app, that you use and
probably like.

**1. Install apktool**  
Version 1.3.2 has currently a pretty bad bug! All xmls with @ will have
problems. But a fixed version can be found [here](http://code.google.com/p
/android-apktool/issues/detail?id=100&colspec=ID%20Stars%20Type%20Status%20Pri
ority%20Milestone%20Owner%20Summary#c7).

  1. Go to [http://code.google.com/p/android-apktool/](http://code.google.com/p/android-apktool/)
  2. Download apktool-install-<your system>-* file
  3. Download apktool-* file 
    1. Do not use Version 1.3.2! It has a bug. Use [this one here](http://code.google.com/p/android-apktool/issues/detail?id=100&colspec=ID%20Stars%20Type%20Status%20Priority%20Milestone%20Owner%20Summary#c7).
    2. contains the .jar on mac you will have to unpack it with untar xvfj <file>. If you doubleclick it will even unpack the jar :)
  4. Unpack both to /usr/local/bin (or windows folder on windows)
**2. See the source code of the app** (with source code I mean smali-code) 

  1. Get the .apk. 
    1. You might have to get it from the phone (adb pull /data/app/filename.apk ) For this find out the file name first (adb shell ....)
  2. apktool d myapp.apk out
The source is now in the "out" folder. You can change images and so on. You
will notice that code is not Java code. But it is readable. Just takes a
little more effort :). If you want to change code you can do so in the smali
way. Or if you want to write Java code, then note, that the whole function has
to contain either smali code or Java code.

**3. Rebuild the apk**  
Go into the source folder of the app. If you did it as above, than it will be
the "out" folder. (cd out)

You will need to sign the apk ([see more infos
here](http://developer.android.com/guide/publishing/app-signing.html)). You
can use your own or just quickly create a keystore like i did with this
command:

{% highlight bash %}

keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000

{% endhighlight %}

Note that you will have to refer to the correct files (keystore and .apk).

{% highlight bash %}

apktool b
jarsigner -verbose -keystore my-release-key.keystore dist/.apk alias_name
zipalign -v 4 dist/.apk dist/-aligned.apk
adb uninstall 
adb install dist/-aligned.apk

{% endhighlight %}

You need to uninstall because the signature will be different and it won't let
you overwrite the app.

**Some words in the end**  
Well that's was it! Not difficult right? If you have problems or questions,
please write comments.

Cheers to Brut.all who developed apktool. And of course all the others
involved!

