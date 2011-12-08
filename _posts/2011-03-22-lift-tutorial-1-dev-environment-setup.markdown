---
author: admin
date: '2011-03-22 14:10:59'
layout: post
slug: lift-tutorial-1-dev-environment-setup
status: publish
title: 'Lift Tutorial 1: Development Environment Setup'
wordpress_id: '666'
categories:
- Scala
tags:
- eclipse
- intellij
- jetty
- lift
- mongodb
- plugin
- sbt
- scala
- simple build tool
---

**What this tutorial will cover**

  * Lift (Scala)
  * SBT (Simple Build Tool)
  * Jetty (Web Server - using it out of sbt)
  * Eclipse (Editor)
  * IntelliJ IDEA Community Edition (Editor)

**Operating System**  
I will write down instructions for Mac OS X. But for Windows or Linux the
instructions might only differ slightly.

**Create basic lift project with SBT (Simple Build Tool)**  
With [SBT](http://code.google.com/p/simple-build-tool/) we will be managing
our lift project. It helps us to do several tasks pretty easy. The easiest way
to start with Lift and SBT is to download a sample SBT Lift project from
[https://github.com/lift/lift_23_sbt](https://github.com/lift/lift_23_sbt) .
On that site click on "Downloads" on the right to download it as .tar.gz or
.zip. Then choose one of the folders depending on what you want to do. You can
choose lift_basic to see an example.

I prefer git and will handle my project with git so I do the following to
download the default example:

{% highlight bash %}

git clone https://github.com/lift/lift_23_sbt.git
cp -R lift_23_sbt/lift_basic/ myLiftProject
cd myLiftProject
git init

{% endhighlight %}

now add a .gitignore with the content as here: [https://github.com/lift/lift_2
1_sbt/blob/master/.gitignore](https://github.com/lift/lift_21_sbt/blob/master/
.gitignore) and then do

{% highlight bash %}

git add .
git commit -m "first commit"

{% endhighlight %}

**Run the basic project**  
Go into the folder myLiftProject and rund the following commands. Some of them
will take quite some time.

{% highlight bash %}

sbt update
sbt ~jetty-run

{% endhighlight %}

~jetty-run will start jetty to run your project. Go to
[http://127.0.0.1:8080/](http://127.0.0.1:8080/) and you will see the web site
up and running. If you change anything in the code it will automatically
notice that and compile it. A few seconds later you see the change reflected
on the site. Use CTRL+C to stop it from executing.

**.war file**  
If you want to package your project as a .war to put it on your web server,
run "sbt package".

**Working on the code**  
Well there are several ways to work on the lift (scala) code. You can use vim,
eclipse or IntelliJ IDEA. I will quickly explain how to work with eclipse and
IntelliJ IDEA community edition (both free tools)

**Eclipse**

  * Install Eclipse h[ttp://www.eclipse.org/downloads/packages/release/galileo/sr2](ttp://www.eclipse.org/downloads/packages/release/galileo/sr2) (galileo is currently recommended)
  * Install the ScalaIDE for Eclipse. Read more about it here: [http://www.scala-ide.org/ ](http://www.scala-ide.org/)
    * In short in eclipse helios: Help -> Install new Software: http://download.scala-ide.org/update-current-35 (using nightly for now)
Create a eclipse project out of the sbt project (full info
[here](https://github.com/musk/SbtEclipsify)):

  * Create a file called MySbtProjectPlugins.scala in project/plugins/ with the following content 

{% highlight scala %}

import sbt._  
 class MySbtProjectPlugins(info: ProjectInfo) extends PluginDefinition(info) {
       lazy val eclipse = "de.element34" % "sbt-eclipsify" % "0.7.0"
 }

{% endhighlight %}

  * Edit project/build/LiftProject.scala and adjust it like this (add import line and "with Eclipsify") 

{% highlight scala %}    

import sbt._
import de.element34.sbteclipsify._  
class LiftProject(info: ProjectInfo) extends DefaultWebProject(info) with Eclipsify {

{% endhighlight %}

  * Now run the following commands 

{% highlight bash %}

sbt reload
sbt eclipse

{% endhighlight %}

  * Done. Your project is now a valid eclipse project. Import it in eclipse.

**IntelliJ IDEA (Community Edition)**

  1. Download and install the community edition here: [http://www.jetbrains.com/idea/](http://www.jetbrains.com/idea/)
  2. Settings -> Plugins -> Available : Search for scala and install

**Create a IntelliJ IDEA project out of the sbt project (full info [here](https://github.com/mpeltonen/sbt-idea)):**

  1. Go to [https://github.com/mpeltonen/sbt-idea](https://github.com/mpeltonen/sbt-idea)
  2. Follow instructions below "Usage as processor" 
    * to start sbt just enter sbt in the shell or command line window and press enter
  
Read as well on [http://liftweb.net/](http://liftweb.net/) (specially the
getting started) to learn more about Lift.

You can use [RUN@cloud](http://www.cloudbees.com/run.cb) as a lift server. You
can currently get a basic account for free! Register and upload your .war file
there.

With the next tutorials I will try to cover how to set up a nicer server with
jetty and mongodb and then deploying the war file. Hopefully we can automate
that as much as possible.

