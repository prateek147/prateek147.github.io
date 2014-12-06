---
layout: post
title: "iOS Appllication Security Part 4 – Runtime Analysis Using Cycript (Yahoo Weather App)"
date: 2013-07-02 17:20
comments: true
categories: [security]
---

<p>In the previous article, we learnt about the runtime capabilities of an iOS App written in Objective-C which uses the Cocoa framework. In this article,  we will look at how we can use a very essential tool named Cycript to analyze and modify the runtime of an iOS application. In this article, we will be performing our analysis on the Yahoo Weather iOS app.</p>

<h2>Cycript</h2>
	
<p>Cycript is a javascript interpreter which also understands Objective-C syntax, meaning we can write either Objective-C or javascript or even both in a particular command. It can also hook into a running process and help us to modify a lot of the things in the application during runtime. As far as its application to iOS application is concerned, here are some of the advantages of using Cycript.</p>
 
<p>
<ol>
	<li>We can hook into a running process and find the names of all classes being used, i.e the view controllers, the internal and third party libraries being used and even the name of the Application delegate.</li>
	<!-- more -->
	<li>For a particular class, i.e View Controller, App delegate or any other class, we can also find the names of all the methods being used.</li>
	<li>We can also find the names of all the instance variable and their values at any particular time during the runtime of an application.</li>
	<li>We can modify the values of the instance variable during runtime.</li>
	<li>We can perform Method Swizzling, i.e replace the code of a particular method with some other implementation.</li>
	<li>We can call any method in the application during runtime without it being in the actual code of the application .</li>
</ol>
</p>


<h2>Installing Cycript</h2>

<p>To install Cycript on your device, you must download the latest version from their official download <a href="http://www.cycript.org/debs/">page</a>. Make sure to download the latest version. </p>

<img src="/images/posts/ios4/1.png" width="1070" height="92" alt="1">

</p>Once they have been downloaded on your system, upload them to your device using sftp.</p>

<img src="/images/posts/ios4/2.png" width="1248" height="278" alt="2">

<p>Then use the dpkg command to install cycript on your device.</p>

<img src="/images/posts/ios4/3.png" width="902" height="254" alt="3">

<p>Then use the cycript command to check and see if everything is working.</p>

<img src="/images/posts/ios4/4.png" width="816" height="65" alt="4">

<h2>Runtime modification with Cycript</h2>

<p>In this article, we will be performing all our tests on the <i>Yahoo Weather</i> app. It's a very neat app with an elegant UI that provides information about the weather of different places.</p>

<p>Once the <i>Yahoo Weather</i> has been installed, run it and make sure the app is the foreground. This is because once the app is in the background , it is in the paused state and you can't do much with it. Once the app is run, you can directly hook into the running process by finding the PID of the weather app and then using the <i> cycript -p <pid> </i> command to hook into the process.</p>
	

<img src="/images/posts/ios4/5x.png" width="1390" height="204" alt="5x">

<p>If the hooking is successful, you will be provided with a Cycript interpreter. Now we are all set.</p>

<p>You can get the instance of the application by using the Objective-C syntax <i>[UIApplication sharedApplication]</i>.</p>

<img src="/images/posts/ios4/5.png" width="712" height="50" alt="5">

<p>You can also define variables using the Cycript interpreter as shown in the figure below. In this case, i am defining a variable <i>a</i> for <i>[UIApplication sharedApplication]</i>. Note that the L.H.S of this command is Javascript whereas the R.H.S is Objective-C syntax. This is the beauty of Cycript.</p>

<img src="/images/posts/ios4/6.png" width="646" height="90" alt="6">

<p>Cycript, by default has this variable which makes it easy to refer to the application instance.</p>

<img src="/images/posts/ios4/7.png" width="531" height="51" alt="7">

<p>To find the delegate class of this application, we can use the command <i>[UIApplication sharedApplication].delegate</i>. But since we already defined a variable <i>a</i> as the application instance, we can use the following command as shown in the figure below.</i>
	
<img src="/images/posts/ios4/8.png" width="629" height="179" alt="8">

<p>Hence, we now now that the delegate class name is <i>YWAppDelegate</i>. So the delegate files are defined as YWAppDelegate.h and YWAppDelegate.m.</p>

<p>Now, let's try and call some methods in the app while it is running. A quick look at the application gives this kind of a picture.</p>

<img src="/images/posts/ios4/IMG_0091.PNG" width="640" height="960" alt="IMG 0091">

<p>As you can see, the status bar of the app is hidden. We can call a method in the application to unhide the status bar. Also, please make sure always that the app is in the foreground while you are performing runtime analysis on the application.</p>

<img src="/images/posts/ios4/9.png" width="932" height="59" alt="9">

<p>And here is how the app looks now </p>

<img src="/images/posts/ios4/IMG_0092.PNG" width="640" height="960" alt="IMG 0092">

<p>As you can see, the status bar is now visible. Let's try and see if we can modify the badge count of this particular application. A badge count is the number shown on the top-right of an application icon image. It usually refers to the amount of push notification received for the application. In mail apps, it can also refer to the amount of unread mails. In Yahoo Weather app, there is no concept of push notifications and hence there is no count shown on the top-right of its app icon. The thing is that the application badge number can be set locally in the app through a simple function call as well as remotely through a push notification from the server. Let's try and see if we can set the badge count for the app. Here is the Objective-C method that we can need to call. In this case i am setting the badge count to 999</p>

<img src="/images/posts/ios4/9x.png" width="877" height="27" alt="9x">

<p>And now if we go back to the home screen, we see the badge count in the app.</p>

<img src="/images/posts/ios4/IMG_0093.PNG" width="640" height="960" alt="IMG 0093">

<p>Perfect !! </p>

<p>Let's now investigate more and see what more we can find out about the app. In order to find out the current view controller of the app, we must first find out the keyWindow property. A keyWindow is the window which is currently accepting user interaction (touch events) from the user. If you want to find out all the windows in an app, here is how you do it. Note that a window is of the class type UIWindow.</p>

<img src="/images/posts/ios4/10.png" width="1385" height="184" alt="10">

<p>Now, in order to find out the keyWindow at a particular time in the app, here is how you do it.</p>

<img src="/images/posts/ios4/11.png" width="1136" height="103" alt="11">

<p>Now, the root view controller for this window is found out by using the rootViewController property of the keyWindow. The root view controller property for this window is responsible for displaying the content in the window.</p>

<img src="/images/posts/ios4/14.png" width="779" height="47" alt="14">

<p>As you can see, the name of the rootViewController class is YahooSlidingViewController. From the name, it is pretty clear that this class is the slider used in the app as shown in the figure below.</p>

<img src="/images/posts/ios4/IMG_0095.PNG" width="640" height="960" alt="IMG 0095">

<p>Hence, this class basically acts as a facade over all the other view controllers. This means that whenever a menu item is chosen in the menu as shown in the figure above, the YahooSlidingViewController is the class responsible for displaying the appropriate view controller.</p>

<h2>Conclusion</h2>

<p>In this article, we looked at how we can install Cycript into a jailbroken device, hook into a running process and find out the information about the properties of the application. We also looked at how we can call functions ourselves which are executed inside the sandbox of the application. In the next article, we will look at how we can find out all the methods of a given class and modify its implementation. We will also look at how we can modify the values of instance variables of a particular class.</p>

<b>References:</b>

 <ul>
 <li><p>Cycript</br><a href ="http://www.cycript.org/">http://www.cycript.org/</a></p>
 </li>
 <li><p>Cycript tricks</br><a href="http://iphonedevwiki.net/index.php/Cycript_Tricks">http://iphonedevwiki.net/index.php/Cycript_Tricks</a></p>
 </li>
 </ul>


 <p>This article was originally published on the <a href="http://resources.infosecinstitute.com/">resources</a> page at <a href="http://infosecinstitute.com/">Infosec Institute</a>. For more information, please visit my author <a href="http://resources.infosecinstitute.com/author/prateek/">page</a>.</p>
