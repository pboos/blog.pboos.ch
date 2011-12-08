---
author: admin
date: '2011-04-04 09:09:05'
layout: post
slug: lift-tutorial-2-nice-server-with-jetty-and-mongodb
status: publish
title: 'Lift Tutorial 2: Nice Server with Jetty and MongoDB'
wordpress_id: '693'
categories:
- Scala
---

**What this tutorial will cover**

  * EC2 instance
  * Jetty
  * MongoDB
  * Screen (to have both Jetty and MongoDB running in the background and see the output)
  * Easy deployment script (SCP)

**EC2 instance**

I just got a free micro EC2 instance at [AWS](http://aws.amazon.com) (Amazon Web Services) for 1 year for free ([see here](http://aws.amazon.com/free/)) and thought I could try out Scala and Lift on it. I did that once already to test, but this time I wanted a nicer installation. I wanted to use Jetty as Server that is always running on the ec2 instance. Then I want to be able to easily deploy .war files to it.

If you are on windows and you want to connect to your EC2 with Putty follow the instructions [here](http://www.keywordintellect.com/amazon-web-services/managing-an-amazon-ec2-instance-using-putty-ssh/).

**Jetty**

To get a basic jetty version running on your server, do the following: 

{% highlight bash %}

JETTY_VERSION=7.3.1.v20110307
wget http://download.eclipse.org/jetty/$JETTY_VERSION/dist/jetty-distribution-$JETTY_VERSION.tar.gz
tar xfz jetty-distribution-$JETTY_VERSION.tar.gz
cd jetty-distribution-$JETTY_VERSION
java -jar start.jar

{% endhighlight %}

There you go. Your basic jetty installation is running already! Put a .war file into the webapps folder and access it by adding the name of the war file to the url (.war => http://server.com// )

More information under [http://wiki.eclipse.org/Jetty/](http://wiki.eclipse.org/Jetty/)

**MongoDB**

To install and run MongoDB on your own computer follow the instructions here: [http://www.mongodb.org/display/DOCS/Quickstart](http://www.mongodb.org/display/DOCS/Quickstart) (their explanation is perfect :) ) To install and run MongoDB on EC2 follow those instructions: [http://www.mongodb.org/display/DOCS/Amazon+EC2](http://www.mongodb.org/display/DOCS/Amazon+EC2) Here some links for starting with MongoDB on Scala: 

  * Video showing how to use MongoDB in Scala: [http://ontwik.com/nosql/mongodb-with-scala/](http://ontwik.com/nosql/mongodb-with-scala/)
  * Links by MongoDB themselves: [http://www.mongodb.org/display/DOCS/Java+Language+Center#JavaLanguageCenter-Scala](http://www.mongodb.org/display/DOCS/Java+Language+Center#JavaLanguageCenter-Scala)


**Screen**

Try "screen -S jetty" and then start Jetty there. To leave that "screen" press CTRL A + D. To see that screen again enter "screen -r jetty". You can do the same with another name for MongoDB, just use "mongodb" instead of "jetty" in the command.  

**Easy deployment script (SCP)**

Sometimes typing "sbt package" and "scp ........" can be too much. :D So I wrote a small script that does the work for you. Create a file called deploy in the root of the sbt project, adjust the values below SETTINGS and make "chmod +x deploy". After that you can run it with "./deploy". Have fun :) 

{% highlight scala %}

#!/usr/bin/env scala
!#  
import java.io._
import scala.runtime.RichChar
import scala.io.Source
import scala.util.matching.Regex
import scala.collection.immutable.Stream  
// SETTINGS
val FILE_NAME_ON_SERVER = "1.war"
val SCP_SERVER_NAME = "ec2.my_server.ch"
val SCP_SERVER_USER = "ec2-user"
val SCP_SERVER_ID_LOCATION = "/Users/patrickboos85/.ssh/my_server.id"
val SCP_SERVER_FOLDER = "/home/ec2-user/jetty/webapps"  
println("### PACKAGING as .war (sbt package)")
def p = Runtime.getRuntime().exec("sbt package")
val WarFileExp = """.*Packaging \.(.*\.war) \.\.\..*""".r
var warLocation : String = ""
for (
    line <- Source.fromInputStream(p.getInputStream()).getLines
) {
    println(line)
    if ((line contains "Packaging") && (line contains ".war")) {
        val WarFileExp(warFile) = line
        warLocation = System.getProperty("user.dir") + warFile
    }
}
if (warLocation.length() > 0) {
    val warFile = new File(warLocation)
    println("### RENAMING TO " + FILE_NAME_ON_SERVER)
    val renamedWarFile = new File(warFile.getParent + "/" + FILE_NAME_ON_SERVER)
    warFile.renameTo(renamedWarFile)  
    println("### UPLOADING (this will take quite some time)")  
    val scpCommand = "scp -i " + SCP_SERVER_ID_LOCATION + " " + renamedWarFile.getAbsolutePath + " " + SCP_SERVER_USER + "@" + SCP_SERVER_NAME + ":" + SCP_SERVER_FOLDER  
    def p = Runtime.getRuntime().exec(scpCommand)  
    Source.fromInputStream(p.getInputStream()).foreach(print(_))
    Source.fromInputStream(p.getErrorStream()).foreach(print(_))  
    println("### DONE")
} else {
    println("### .war file was not created. See output. Maybe deployed already?")
}

{% endhighlight %}

Sadly it does not show the SCP output :(. If you figure out how to do it,
please let me know in the comments.

  
Maybe later we will go into writing a WebService: [http://www.assembla.com/wiki/show/liftweb/REST_Web_Services](http://www.assembla.com/wiki/show/liftweb/REST_Web_Services)

