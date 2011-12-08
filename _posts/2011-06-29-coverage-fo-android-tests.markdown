---
author: admin
date: '2011-06-29 08:26:17'
layout: post
slug: coverage-fo-android-tests
status: publish
title: Coverage report for Android unit tests
wordpress_id: '717'
categories:
- Google Android
tags:
- android
- ant
- build.xml
- coverage
- emma
- report
---

You want to have a nice report how much you cover with your Android unit
tests? Here a quick walkthrough how to get such a report (see screenshots of
report below).

In this example I expect that your test project is in a folder "tests" in your
project folder which gets tested.

1. Create a build.xml for your project (if it does not yet exist) `cd <main project folder> android update project --path .`
2. Create the build.xml for the test project (that will allow us to do the coverage) `android update test-project -m <full path to main project> -p tests/ `
 
Somehow "." does not work and you have to use the full path like "/home/pboos/develop/workspace/project"

4. Run coverage
    `cd tests/ ant coverage `

This will print out the path to the html file where you can see a nice report. Should be in "tests/coverage/coverage.html".

**Note:**

If you have libraries that you use and you get an error. Make sure you put them into a "libs" directory. ant automatically adds the libraries in "libs" to the build path.

Example for coverage report:
[![](http://blog.pboos.ch/wp-content/uploads/2011/06/source_file-262x300.png)](http://blog.pboos.ch/wp-content/uploads/2011/06/source_file.png)
[![](http://blog.pboos.ch/wp-content/uploads/2011/06/overall-300x130.png)](http://blog.pboos.ch/wp-content/uploads/2011/06/overall.png)
[![](http://blog.pboos.ch/wp-content/uploads/2011/06/package-300x101.png)](http://blog.pboos.ch/wp-content/uploads/2011/06/package.png)

**[UPDATE]**

If you get an error, that emma.jar (or android) is not in your classpath, you might have forgotten to add the tools directory to your classpath. Follow the instructions below "How to update your PATH" on [http://developer.android.com/sdk/installing.html#sdkContents](http://developer.android.com/sdk/installing.html#sdkContents). As well make sure that you have updated the Android SDK Tools (I used revision 11, when I wrote this). How to do this is here: [http://developer.android.com/sdk/adding-components.html](http://developer.android.com/sdk/adding-components.html)

