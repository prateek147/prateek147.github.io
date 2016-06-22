---
layout: post
title: "iOS Application Security Part 37 - Adapting to iOS 8"
date: 2014-12-20 00:27
comments: true
categories: [security]
---

<p>In this article we will look at all the things you need to do to set up a pentesting platform on iOS 8 and be comfortable with using all the tools.</p>

<h3>Jailbreaking your device</h3>

<p>If your device is running iOS 8.1 or earlier, you can use <a href="http://en.pangu.io/">pangu</a> to jailbreak your device. The process is pretty straightforward. We will not be covering jailbreaking your device in this article but if you want to know how it is done, you can read <a href="http://www.redmondpie.com/how-to-jailbreak-ios-8.1-untethered-using-pangu8/">this</a> article.</p>

<!-- more -->

<p><i>Note: I was getting an error with Pangu saying that there was not enough space on my device even though there was enough space. I tried by putting my device into Airplane mode and by turning off Wifi on my laptop and it worked for me.</i></p>

<p>Once you jailbreak your device, you can install OpenSSH in your device using Cydia and get connectivity to your device.</p>

<h3>Location of applications</h3>

<p>One of the major changes within the file structure is that the user applications are no longer located at the location <i>/var/mobile/Applications</i>. The application bundle is stored in the location <i>/var/mobile/Containers/Bundle/Application</i> (Appname.app) whereas the application data (Documents, Library, tmp folder) is stored in the location <i>/var/mobile/Containers/Data/Application</i>. The name of the folder (a unique ID) will also be different for the same application. So while checking an application, it is recommended to look at both the locations.</p>

<img src="/images/posts/ios37//location1.png" width="848" height="236" alt="Location1">
<img src="/images/posts/ios37//location2.png" width="809" height="178" alt="Location2">

<p>However, the applications that are installed by default on your device with iOS are still located in the location <i>/Applications/</i>. For e.g, the App store application can be seen in the below image.</p>

<img src="/images/posts/ios37//appstore.png" width="593" height="297" alt="Appstore">

<h3>Decrypting applications</h3>

<p>You can download the latest version of clutch to decrypt iOS 8 applications from <a href="https://github.com/KJCracks/Clutch/releases">here</a>. You can download the binary for 1.4.7-git2 as of now. Once you download it on your device, just run it once and then run it again and it will show you the list of encrypted applications on your device.</p>

<img src="/images/posts/ios37//1.png" width="494" height="496" alt="1">

<p>Simply type Clutch followed by the app name to start decrypting it.</p>

<img src="/images/posts/ios37//2.png" width="683" height="428" alt="2">

<img src="/images/posts/ios37//3.png" width="622" height="295" alt="3">
<h3>File explorer utilities</h3>

<p>Because of the change in the location where applications store data, iExplorer now looks inside the folder <i>/var/mobile/Containers/Data/Application</i>. It however, doesn't look into the application bundle. The same goes for iFunbox, which is another good file explorer utility for iOS devices. It is however recommended that you also look inside the application bundle folder also while testing an iOS application.</p>

<img src="/images/posts/ios37//iexplorer.png" width="435" height="245" alt="Iexplorer">

<h3>Cycript & class-dump-z</h3>

<p>Cycript and class-dump-z both work well on iOS 8 devices. However, please make sure you are using the latest version of cycript (0.9.502 at the time of writing).</p>

<h3>Dumping keychain</h3>
	
<p><a href="https://github.com/ptoomey3/Keychain-Dumper">Keychain dumper</a> works just fine and can still dump all your keychain items in your device.</p>

<img src="/images/posts/ios37//keychain.png" width="436" height="102" alt="Keychain">

<h3>Swift</h3>

<p>According to Apple, Swift is a new programming language for iOS and OS X apps that builds on the best of C and Objective-C, without the constraints of C compatibility. Swift adopts safe programming patterns and adds modern features to make programming easier, more flexible, and more fun. Swift’s clean slate, backed by the mature and much-loved Cocoa and Cocoa Touch frameworks, is an opportunity to reimagine how software development works. In the future articles, we will learn how to use cycript to analyze applications written in swift. However, i recommend you to start learning swift. One of the best resources to learn it from apart from Apple's documentation is on <a href="http://www.raywenderlich.com/74438/swift-tutorial-a-quick-start">Ray Wenderlich's blog.</a> Also, i would like to point out that it might take some time for this language to catch up and be actually used for developing iOS applications. We will cover swift in later articles in this series.</p>