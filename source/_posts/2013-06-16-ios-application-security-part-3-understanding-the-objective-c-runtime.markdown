---
layout: post
title: "iOS Application security Part 3 - Understanding the Objective-C Runtime"
date: 2013-06-16 02:59
comments: true
categories: [security] 
---

<p>Almost all the native iOS applications are written in Objective-C. All these apps use Cocoa which is a library that sits on top on Objc-C and provides high level APIs that make development for Mac and iOS much easier. Cocoa also adds a runtime environment for the applications. In this article, we will focus on understanding the Objective-C runtime and all the intricate details about how the language functions internally. This will help us get a much deeper understanding of its applications to iOS application security.</p>

<h2>Objective-C runtime</h2>

<p>Objective-C is a runtime oriented language. Now the questions that arises is, what is a runtime language ? Well, a runtime language is a language that decides what to implement in a function and other decisions during the runtime of the applications. <!-- more --> Is Objective-C a runtime language ? NO. It is a runtime oriented language, which means that whenever it is possible, it defers decisions from compile and link time to the time when the code in the application is actually being executed. As pointed out earlier, Cocoa provides the runtime environment needed by iOS applications. Here is a paragraph from the Apple documentation shown in the image down below which will make things absolutely clear. You can read the documentation <a href= "https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Introduction/Introduction.html">here</a>.</p>

<img src="/images/posts/ios3/1.png" width="1180" height="63" alt="1">

<p>Let's have a look now to check if the runtime library is imported in projects or not. Ideally, it should be with every iOS application. To check this, log in to your device and go to the applications directory.</p>

<img src="/images/posts/ios3/Screen Shot 2013-05-15 at 4.58.11 AM.png" width="943" height="651" alt="Screen Shot 2013 05 15 At 4.58.11 AM">

<p>Now type "ls *" to get a look at the entire listing of directories.</p>

<img src="/images/posts/ios3/2.png" width="791" height="561" alt="2">

<p> Let's look at the BADLAND iOS app. Just in case some of you don't know, BADLAND is a very popular game on iOS. Let's go inside the Badland directory. Once you are inside the folder, go inside the BADLAND.app folder and use the <i>otool</i> utility on the Badland binary</p>

<img src="/images/posts/ios3/3.png" width="1032" height="536" alt="3">

<p>We can now see that it imports quite a lot of frameworks as well as libraries. The objc-runtime library is the one shown in the figure below.</p>

<img src="/images/posts/ios3/5.png" width="753" height="20" alt="5">

<p>This is the library that makes runtime manipulation possible in Objective-C. By default, it is included in all iOS apps. Here are the otool results for the Google Maps iOS app. As you can see, it also includes the Objective-C runtime library.</i>

<img src="/images/posts/ios3/6.png" width="1033" height="513" alt="6">

<h2>Runtime Analysis with GDB</h2>

<p>In this section, we will look at how we can observe the flow of the app using GDB. The first and foremost thing is to install a proper version of gdb. The gdb version available from the Cydia store doesn't work properly, so make sure you get the binary from some other source. Once it is done, sftp into your device and upload the binary as shown in the figure below. </p>

<img src="/images/posts/ios3/7.png" width="1031" height="247" alt="7">

<p>Then make sure it has the appropriate permissions to run.</p>

<img src="/images/posts/ios3/8.png" width="848" height="128" alt="8">

<p>To hook into a running process, it is first important to make sure that the process is running. In this case, we will perform the tests on the Google Maps iOS app. So lets start the app on our device and fetch its process id. Also, make sure that the app is running in foreground. As we can see from the figure below, the process id for the Google maps app is 661. Note that the process id may be different for you.</p>

<img src="/images/posts/ios3/9.png" width="1152" height="143" alt="9">


<p>Now, lets use GDB to hook into this process.</p>

<img src="/images/posts/ios3/10.png" width="1193" height="591" alt="10">
<img src="/images/posts/ios3/11.png" width="1196" height="591" alt="11">


<p>As you can see, we have successfully hooked into the process. You can ignore the warnings for now.</p>

<p>Objective-C is based on messaging and whenever a message is being sent, the objc_msgSend() method gets called. In order to analyze the flow of the app, i am going to add a breakpoint for the most basic call, i.e objc_msgSend and print the values of $r0 and $r1. From $r0 we can find out the class on which the method is being called and $r1 can be used to find out the selector. Note that even though this may lead to too much of detail, as objc_msgSend is called everytime a message is being sent. In the coming articles, we will look at how we can use it more efficiently. So basically, whenever a breakpoint is hit, i am going to print the values of $r0 and $r1 and continue the app. Here is how it's done.</p>

<img src="/images/posts/ios3/12.png" width="486" height="161" alt="12">

<p>Let's continue the app by typing the <i>c</i> command.</i>

<img src="/images/posts/ios3/13.png" width="717" height="582" alt="13">

<p>As you can see, this helps us a lot in analyzing the flow of the app. Again, this is just too information and may not be useful in real time analysis of applications, but this is just to show how much we can figure out about the implementation of an app.</p>

<h2>Method Swizzling</h2>

<p>We already learnt that all the iOS apps use a runtime environment while execution which means that a lot of the decisions are taken at runtime. As the name suggests, method swizzling is just another weapon that we can use to our advantage for modifying the behaviour of an iOS application. It allows us to change the mapping from the selector to the implementation which we could use to our advantage and call our own methods instead. We will look into Method Swizzling in great detail in the next article.</p>

<b>References:</b>
<ul>
<li><p>The Dark art of iOS Application hacking</br><a href ="http://www.slideshare.net/daniel_bilar/blackhat2012-zdziarskidarkarti-osapplicationhacking">http://www.slideshare.net/daniel_bilar/blackhat2012-zdziarskidarkarti-osapplicationhacking/</a></p></li>
<li><p>The Objective-C runtime</br><a href="http://www.slideshare.net/mobiledatasolutions/objectivec-runtime">http://www.slideshare.net/mobiledatasolutions/objectivec-runtime</a></p></li>
	
</ul>

<p>This article was originally published on the <a href="http://resources.infosecinstitute.com/">resources</a> page at <a href="http://infosecinstitute.com/">Infosec Institute</a>. For more information, please visit my author <a href="http://resources.infosecinstitute.com/author/prateek/">page</a>.</p>
