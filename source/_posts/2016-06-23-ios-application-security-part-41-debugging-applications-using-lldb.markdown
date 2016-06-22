---
layout: post
title: "iOS Application Security Part 41 - Debugging applications using LLDB"
date: 2015-04-28 00:17
comments: true
categories: [security]
---

<p>In this article we will learn about the LLDB debugger used by Apple's Xcode for debugging applications, understand why it is better for security testing, and then learn how to use it to debug iOS applications.</p>

<p>The following excerpt is from Apple's documentation.</p>

<p>LLDB is Apple's <i>from the ground up</i> replacement for GDB, developed in close coordination with the LLVM compilers to bring you state-of-the-art debugging with extensive capabilities in flow control and data inspection. Starting with Xcode 5, all new and preexisting development projects are automatically reconfigured to use LLDB. The standard LLDB installation provides you with an extensive set of commands designed to be compatible with familiar GDB commands. In addition to using the standard configuration, you can easily customize LLDB to suit your needs</p>

<p>A good way to start leaning about the LLDB debugger is to watch this video from WWDC 2014.</p>


<!-- more -->

<iframe width="420" height="315" src="https://www.youtube.com/embed/IPhgcbuDk_k" frameborder="0" allowfullscreen></iframe>

<p>But the question that remains is, why is LLDB better than GDB ?</p>

<p>To sum it up, there are a few reasons for that.</p>

<ul>
	<li>It works well for iOS 8</li>
	<li>LLDB is much faster, has support for multithread programming, and is scriptable.</li>
	<li>LLDB understands the dot syntax. You can use something like <i>po self.propertyName</i> or even <i>po instanceVariable</i> to find any value of a property or instance variable while debugging applications.</li>
	<li>It has plug-in support for functionality and extensions.</li>
</ul>

<p>To debug applications on an iOS device, we will use the command line utility debugserver which is used by Apple's Xcode to debug applications on the device. Usually, it gets installed on the device automatically when a device is marked for development, which requires a valid provisioning profile, but it can only debug applications that were signed by that particular provisioning profile. This is because of lack of entitlement to allow task_for_pid(). Hence, in order to debug any application, we must create a new entitlement file with the mentioned flag set to true, sign the debug server application with that entitlement file, and then deploy the console app to the device. Please note that the current location of debugserver application on the Mac is a read-only ramdisk, hence the application must be copied to a different location before signing with the new entitlement file.</p>

<p>Hence, the first step to do is to mount Xcode's developer disk image on your Mac and copy the debugserver binary to a seperate location where you can resign it. Use the following command.</p>

<p><i>hdiutil attach /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport/[ios version]\ \(12B411\)/DeveloperDiskImage.dmg</i></p>

<img src="/images/posts/ios41/1.png" width="648" height="343" alt="1">

<img src="/images/posts/ios41/2.png" width="767" height="225" alt="2">

<p>Once this is done, create a new file with the name entitlements.plist and enter the following data in it.</p>

<pre>
&lt?xml version="1.0" encoding="UTF-8"?&gt
&lt!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/ PropertyList-1.0.dtd"&gt
<plist version="1.0">
<dict>
	&ltkey&gtcom.apple.springboard.debugapplications</key>
	&lttrue/&gt
	&ltkey&gtrun-unsigned-code</key>
	&lttrue/&gt
	&ltkey&gtget-task-allow</key>
	&lttrue/&gt
	&ltkey&gttask_for_pid-allow</key>
	&lttrue/&gt
</dict> 
</plist>
</pre>

<p>Now, you have to resign the debugserver binary with the new entitlements file.</p>

<i>codesign -s - --entitlements entitlements.plist -f debugserver</i>

<img src="/images/posts/ios41/3.png" width="806" height="42" alt="3">


<p>Once this is done, deploy the binary to the device. To start debugserver on the device, use the command shown below, and also specify the name of the application you want to hook to, which in this case is Twitter.</p>
	
<img src="/images/posts/ios41/4.png" width="624" height="163" alt="4">

<p>Now on your computer, start lldb to enter the lldb interpreter. Now use the following commands as shown below to to connect to the device. Make sure to replace the IP address here with the IP address of your device.</p>


<img src="/images/posts/ios41/5.png" width="590" height="409" alt="5">

<p>Once this is done, you will see that you are successfully able to debug the application.</p>


<img src="/images/posts/ios41/6.png" width="715" height="380" alt="6">

<p>Now you can use the po command to print out specific objects instantiated in the application.</p>

<img src="/images/posts/ios41/7.png" width="451" height="84" alt="7">

<img src="/images/posts/ios41/8.png" width="587" height="95" alt="8">

<p>The syntax for LLDB is a bit different. For e.g, here is the syntax for printing all the register variables.</p>

<img src="/images/posts/ios41/9.png" width="705" height="500" alt="9">

	
<p>That's it for this article. In this article, we learnt how to deploy LLDB to the device and use it to run some basic commands. In the next article, we will look at some advanced usage of LLDB and how it can be used to find  sensitive information in memory.</p>
	