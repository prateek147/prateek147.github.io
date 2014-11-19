---
layout: post
title: "iOS Application Security Part 35 – Auditing iOS Applications With iDB"
date: 2014-10-18 14:14
comments: true
categories: [security]
---


<p>In this article, we will look at another cool utility named iDB for pentesting iOS applications.</p>

<p>Before that, i would like to apologize for coming up late with this article. A lot of you have been requesting articles on different topics and i promise that i will write on them soon :). So since we are best friends again, let's dive into this tool.</p>

<p>iDB is open source and you can download it from its <a href="https://github.com/dmayer/idb">Github</a> page. You can then follow the tutorial <a href="https://github.com/dmayer/idb/wiki/Getting-started">here</a> to know how to install it. Installation might take some time as there might be some dependencies you will have to install, so have some patience. Also, please note that it works with ruby versions 1.9 and 2.1 so make sure you set that version of ruby. You can use <i>rvm list</i> to list the versions of ruby installed on your system and then use <i>rvm use</i> followed by the version of ruby that you want to use.</p>

<!-- more -->

<p>Once you are done installing iDB, browser to its root directory and open it using the command <i>ruby gidb.rb</i>.</p>

<img src="/images/posts/ios35/1.png" width="652" height="216" alt="1">

<p>This will launch iDB.</p>

<img src="/images/posts/ios35/2.png" width="1440" height="830" alt="2">

<p>Before you start using iDB, you must set up the connection settings. Go to Ruby -> Preferences.</p>

<img src="/images/posts/ios35/3.png" width="495" height="379" alt="3">


<p>Now under the <i>Device Config</i> tab, select either SSH directly or SSH over USB which uses usbmuxd internally. I prefer the latter one. Provide your username and password and the port number over which SSH is running.</p>

<img src="/images/posts/ios35/4.png" width="485" height="453" alt="4">

<p>Under the port forwarding section, you can set options for port forwarding if you want. This is generally done by the syntax <i>remote:8080 -> localhost:8080</i> which is quite self explanatory. Here, we are not going to set any port forwarding as we really don't need it for this tutorial, but we must set the option <i>Port for Manual SSH</i> which will be used by usbmuxd. What this will do is forward all the connections sent to port 2222 on localhost to the device on port 22 over USB.</p>

<img src="/images/posts/ios35/5.png" width="482" height="453" alt="5">

<p>So basically, you can now just SSH to localhost on port 2222 and it will connect you to your device.</p>
<img src="/images/posts/ios35/6.png" width="778" height="146" alt="6">

<p>Now make sure your device is connected to your system over USB. Go to Devices and select the option <i>USB Device</i> to connect to your device.</p>
<img src="/images/posts/ios35/7.png" width="656" height="125" alt="7"/>

<b>One of the unique functionalities of iDB is that it allows you to connect to iOS simulators as well. I have tried it and it works quite well. This is specially helpful for iOS developers who use Xcode and want to audit their apps quite frequently. I would like you to check that feature out as well.</b>

<p>As soon as iDB connects to your device, it will do a quick analysis and tell you what apps that it needs are already installed on your device and which one needs to be installed. You can just click on install next to the tool to install it. Make sure you install each of the required dependencies. In some cases you might feel that the application is already installed. Well, iDB checks for files in specific locations, for e.g /usr/bin, so it might not be able to detect it always. Its better just to reinstall the tool in case iDB is not able to detect it.</p>

<img src="/images/posts/ios35/8.png" width="936" height="591" alt="8">

<p>Once you have installed all the apps, click on <i>Select App</i> on the top left and select the application that you want to audit. In this case, i am going to select <a href="http://damnvulnerableiosapp.com">Damn Vulnerable iOS application</a>
	

<img src="/images/posts/ios35/9.png" width="885" height="250" alt="9">

<p>Next, click on <i>Launch App</i> to launch your app on the device.</p>

<img src="/images/posts/ios35/10.png" width="477" height="479" alt="10">

<p>On the left hand side, you can click on <i>Analyze Binary</i> to see whether certain security features like Encryption, PIE etc have been enabled in the app or not. Since i have installed this app manually on a jailbroken device, there is no encryption.</p>

<img src="/images/posts/ios35/11.png" width="485" height="250" alt="11">

<p>So let's go through each feature one by one. On the <i>Storage</i> tab, you can see all the files DVIA is writing to or has already written to along with their data protection levels. In case it doesn't show up, click Refresh.</p>


<img src="/images/posts/ios35/12.png" width="953" height="715" alt="12">
<p>The <i>URL Handlers</i> tab tells you which URL schemes the app registers to. You can then call any url starting with that url scheme to see how the app reacts to it.</p>
	
<img src="/images/posts/ios35/13.png" width="961" height="658" alt="13">
	
<p>The binary tab will show you all the Shared libraries the app is using, class information using <i>Weak Class Dump</i> and all the strings it could find in the binary.</p>
	
 
 <img src="/images/posts/ios35/14.png" width="976" height="692" alt="14">
 
 <p>The filesystem tab can be used to see all the files in the application sandbox folder along with their permisions. If you want to see the contents of any file, just double click on it.</p>
 

<img src="/images/posts/ios35/15.png" width="966" height="713" alt="15">
<img src="/images/posts/ios35/16.png" width="842" height="554" alt="16">
 
 <p>The Tools section will allow you to see the cached screenshot that is stored on the device whenever the application goes to the background.</p>
 
<img src="/images/posts/ios35/17.png" width="956" height="661" alt="17">

 <p>And you can also install the Burp CA certificate that is used for analyzing SSL connections directly on your device or simulator. For device, it will simply open the url http://burp (Make sure proxy is set on your device). This is again quite a useful feature and saves you a lot of time. You can also edit the hosts file on your system in case you want to redirect the request for a particular ip or hostname to some other location.</p>
  
 <p>The Log tab shows you the device logs. It can be used to test scenarios when the application is logging sensitive information to the console.</p>
 
 <img src="/images/posts/ios35/18.png" width="971" height="683" alt="18">
 
<p>The keychain tab will dump all the keychain information stored using this application using the keychain dumper utility.</p>

<img src="/images/posts/ios35/19.png" width="973" height="576" alt="19">

<p>The pasteboard tab will analyze the pasteboard buffer to see if there is some sensitive information stored in the buffer. As you can see, here it finds a string named <i>secret123</i> stored in the pasteboard buffer.</p>

<img src="/images/posts/ios35/20.png" width="914" height="506" alt="20">


<p>Overall, iDB is a great tool for iOS pentesting and helps us save a lot of time by doing most of the tasks with just one click. There are other features as well such as integration with Cycript and Snoop-it, but they are still in the development phase. I would recommend that you clone the repository from github if you want to check those features out.</p>
 
	</body>
</html>






 
 