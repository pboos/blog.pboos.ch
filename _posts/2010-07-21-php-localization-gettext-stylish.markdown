---
author: admin
date: '2010-07-21 06:45:57'
layout: post
slug: php-localization-gettext-stylish
status: publish
title: PHP Localization gettext-stylish
wordpress_id: '580'
categories:
- PHP
tags:
- gettext
- i18n
- internationalization
- localization
- php
- translate
- tutorial
- website
---

The last few days I worked on getting translations to work with gettext.
Somehow it didn't work the way I wanted it. Big problem there is: You need to
restart the server when you update translations. But I kind of liked the
gettext style and tools that already exist.

Because of that I wrote my own class in PHP which allows me to get
translations done on the future web site for an Android Project.

Here a quick example of code how it works:

{% highlight php %}

require_once "includes/Localization.class.php";
$loc = new Localization;
$loc->setDomain("test");
$loc->setLocale("de_DE");
echo $loc->_("This is a test!");  // translates "This is a test!"

{% endhighlight %}
  
Well. This is the website constructed. But now we need to create the .pot file
for this website. Well. No problem. Just run the following command after you
set the domain (setDomain). You can as well give an array of files if you have
more than one file in the same domain.

{% highlight php %}

$loc->createGettextPotFor(__FILE__);

{% endhighlight %}

This will create the locale/test.pot through running a shell command (xgettext
needs to be installed). You have to do this for every domain you create
through the website. Best is probably to make a .php file which has all the
domains with its files. Than you can run this once and get all the files done.

Now you can take that file and copy it to your language folder with the ending
.po. For example we place it into "locale/de/LC_MESSAGES/test.po". This is the
same way as you would do with gettext. And in this file you can do your
translations for German.

Gettext always compiles things. So I thought why should I not do the same?
Instead of always read through the entire .po file and filling the array I
could just do that once. So I did. When you compile the file will be read,
filled into an array and then serialized. The following command will do that
for all files in locale:

{% highlight php %}

$loc->compileAll();

{% endhighlight %}

Well that's it. And don't forget, that the whole locale folder needs to be
writable :).

Here I give you my example files to test it out.

[Download Zip](/downloads/localization.zip)

If you have any questions or improvements, let me know in the comments :).

