---
layout: post
title: "iOS Application Security Part 9 – Analyzing Security of iOS Applications using Snoop-it"
date: 2013-08-20 07:29
comments: true
categories: [security]
---

<p>In some of the previous articles, we have looked at how we can dump class information of iOS apps using class-dump-z, hook into the runtime using Cycript and perform runtime manipulation and method swizzling, analyze the flow of the app using gdb etc. However, there could be a much better way of doing these things. We shouldn't be using seperate tools for all these tasks. It would be great if a tool could perform all these tasks and at the same time display the information in a much more presentable way.</p>

<p>Snoop-it is a tool that solves these problems. It allows for runtime analysis and blackbox security assessment of iOS apps by retrofitting existing apps with debugging and runtime tracing capabilities. It also provides a very neat web interface. At the time of writing of this article, Snoop-it is not released yet but is a couple of weeks away from launch. I mailed the authors and they were nice enough to provide me with a beta version for testing. You can check out its official page <a href="https://code.google.com/p/snoop-it/">here</a> or you can follow the author on <a href="http://twitter.com/aykay/">Twitter</a></p>.

<!-- more -->

<p>A quick list of all the features provided by Snoop-it could be seen in the screenshot below taken from its official <a href="https://code.google.com/p/snoop-it/">page</a>.


	
<img src="/images/posts/ios9/1.png" width="640" height="519" alt="1">

<h2>Installation</h2>

<p>To install Snoop-it on your device, you will have to download the deb package file and then upload it on your device using sftp. Once this is done, use the command <i>dpkg -i [packageName]</i> to install Snoop-it on your device. Once this is done, respring or reboot your device.</p>
	
<img src="/images/posts/ios9/2.png" width="832" height="228" alt="2">

<p>Once this is done, you will see the Snoop-it app icon on your device. Open it up and you will see this user interface.</p>


<img src="/images/posts/ios9/IMG_0109.PNG" width="320" height="480" alt="IMG 0109">
<p>Go to Settings and configure the app according to your need. In this case, i have chosen the port number to be 12345 and i have also disabled the authentication. It might be a good idea however to have the authentication enabled if you are testing on a network with lots of users, or a network with a few naughty users.</p>

<img src="/images/posts/ios9/IMG_0110.PNG" width="320" height="480" alt="IMG 0110">


<p>Now, just open the Snoop-it web interface by browsing to the address provided on the Snoop-it application. In my case, the address is http://10.0.1.79:12345</p>

<img src="/images/posts/ios9/3.png" width="532" height="360" alt="3">

<p>You will see this web interface. If you read it up, its asking you to <i>select an application that needs to be analyzed from the Snoop-it application, open it up on the device, and then  refresh this web interface</i>. So let's go back to the Snoop-it application and select the applications that we need to analyze. In my case, i am going to select the <i>MethodSwizzlingDemo</i> app, the same app that we used in the <a href="http://resources.infosecinstitute.com/ios-application-security-part-8-method-swizzling-using-cycript/">previous</a> article.</p> 

<img src="/images/posts/ios9/IMG_0111.PNG" width="320" height="480" alt="IMG 0111">

<p>Now, make sure that the app is opened on your device and in foreground and now refresh the Snoop-it web interface.</p>

<img src="/images/posts/ios9/4.png" width="1440" height="720" alt="4">

<p>And now as you can see, you have a beautiful interface now that you can use to perform a full fledged security assessment of the application.</p>

<h2>Analysis</h2>

<p>On the left hand side, under <i>Analysis</i>, go to <i>Objective-C classes</i>. On the right hand side, you will see all the classes and info like properties and method names.</p>

<img src="/images/posts/ios9/5.png" width="1437" height="720" alt="5">

<p>The ones in orange represent the classes that have instances. For e.g if you hover your mouse over the class <i>View Controller</i>, you will see that it has an instance which is live presently.</p>

<img src="/images/posts/ios9/6.png" width="377" height="93" alt="6">

<p>Similarly, you can see the methods and properties for AppDelegate.</p>

<img src="/images/posts/ios9/7.png" width="1047" height="610" alt="7">

<p>Coming back to view controller, it is possible for us to invoke a method using Snoop-it. Just check any particular method, and click on <i>Setup and Invoke</i> on the top right. As we saw in the  <a href="http://resources.infosecinstitute.com/ios-application-security-part-8-method-swizzling-using-cycript/">previous</a> article, with this technique we were able to bypass the authentication check for this application.</p>


<img src="/images/posts/ios9/8.png" width="812" height="626" alt="8">
<p>Select the instace (there is only one now, but there could be multiple instances if the view controller is being reused across the application), and click on <i>Invoke Method</i>. </p>

<img src="/images/posts/ios9/x.png" width="659" height="373" alt="X">

<p>This will invoke the method and will bypass the authentication.</p>

<img src="/images/posts/ios9/IMG_0112.PNG" width="320" height="480" alt="IMG 0112">

<p>Another awesome feature of Snoop-it is that we can switch to any View controller. For e.g, on the extreme left hand side, under <i>Analysis</i>, select <i>View Controller</i>, select the <i>View Controller</i> class on the right hand side and click on <i>Display Controller</i>. You will be switched to that view controller. You can also click on <i>Close/Hide View Controller</i> depending on whether the view controller is over another view controller or not.</p> 

<img src="/images/posts/ios9/9.png" width="1082" height="456" alt="9">

<p>You can then tap on <i>Reset display</i> to come back. As you can understand, this feature will really help us relate the view controller to its view in the app. So if i have a view controller in the Classes section, i can use this feature to see its visual representation. I just love this feature of Snoop-it.</p>

<h2>Runtime Manipulation</h2>

<p>Snoop-it also allows for many ways of runtime manipulation, including changing your hardware identifier attributes like Mac address, UDID, device model number etc.</p>

<img src="/images/posts/ios9/10.png" width="1310" height="570" alt="10">

<p>You can also spoof your location. This could be particular useful for apps that use GeoEncryption techniques to protect their data.</p>

<img src="/images/posts/ios9/11.png" width="1438" height="647" alt="11">

<p>And, you can trace methods and system calls on the flow. Please note that you will have to click on <i>Refresh</i> on the top to see the method calls being made after every few seconds. Also, FYI since we are testing on a beta release it is possible that the authors may change it so that we don't have to click on <i>Refresh</i> after every few seconds. This information might be a bit too much for some users, but if you have been developing iOS applications for a couple of years like me, then this information should be pretty much straightforward.</p>

<img src="/images/posts/ios9/12.png" width="1434" height="717" alt="12">

<h2>Monitoring</h2>

<p>Snoop-it also allows you to look at the various files and directories that are being accessed by the application. To do that, on the navigation menu on the left side, click on <i>Filesystem</i> under <i>Monitoring</i>.This feature can be particular useful when a particular application is writing to a database file and this interface helps you in figuring out that filename. You can also download these files just by double clicking on them and then analyze it on your machine.</p>

<img src="/images/posts/ios9/13.png" width="1225" height="380" alt="13">

<p> You can also see all the access made by the application using sensitive API. This could include looking for info on the Address book, accessing the camera, or just finding the UDID for the device. Here is the sensitive API accessed by the <i>App Store</i> application which comes preinstalled on all iOS devices.</p>


<img src="/images/posts/ios9/y.png" width="1220" height="401" alt="Y">


<p>We can also see all the information stored in the keychain by this application. Also, it is possible to see a list of all HTTP requests sent using NSURLConnection. Both of these features can be accessed under the navigation menu under <i>Monitoring</i>. I leave it up to you to try these features out. Also, we will be discussing how to dump information from keychain in a seperate article.</p>



<p>You will be happy to know that Snoop-it has a public API that we can make use of in order to automate tests or just to build our own graphical user interface. A documentation of the XML-RPC web service API can be found <a href="http://code.google.com/p/snoop-it/wiki/ ">here</a></p>

<h2>Conclusion</h2>

<p>In this article, we looked at how we can we can use Snoop-it to perform runtime analysis and black box security assessment of iOS Applications and how easy it makes our task. Snoop-it is still a few weeks away from release at the time of writing this article, though you can always mail the author for a beta version like i did. One of the new features that i would like added in Snoop-it is the ability to perform Method Swizzling.I am pretty sure it will be an awesome tool for anyone interested in performing security analysis of iOS Applications and it's only going to get better :)</p>
