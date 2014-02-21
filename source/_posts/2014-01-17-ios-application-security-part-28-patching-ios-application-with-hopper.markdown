---
layout: post
title: "IOS Application Security Part 28 - Patching IOS Application with Hopper"
date: 2014-01-17 21:40
comments: true
categories: [security]
---

<p>In <a href="http://highaltitudehacks.com/2013/12/17/ios-application-security-part-26-patching-ios-applications-using-ida-pro-and-hex-fiend">Part 26</a> of this series, we looked at how we can use IDA Pro and Hex Fiend to patch an IOS application and modify its implementation. Patching an application has the specific advantage that once a change has been made, it is permanent. However, if you look back at the article on <a href="http://highaltitudehacks.com/2013/12/17/ios-application-security-part-26-patching-ios-applications-using-ida-pro-and-hex-fiend">IDA Pro</a>, you will realize that the process of patching the application was a bit tedious, mainly because we didn't have a licensed version of IDA Pro which costs a lot. In this article, we will look at a utility named Hopper which we can use as an alternative to IDA Pro. It is less costly than IDA Pro and also provides a sleek interface to work with.</p>

<p>According to Hopperapp.com ..</p>

<p><i>Hopper is a reverse engineering tool for OS X, Linux and Windows, that lets you disassemble, decompile and debug (OS X only) your 32/64bits Intel Mac, Windows and iOS (ARM) executables! Take a look at the feature list below! </i></p>

<p>And...</p>

<p><i>Even if Hopper can disassemble any kind of Intel executable, it does not forget its main platform. Hopper is specialized in retrieving Objective-C information in the files you analyze, like selectors, strings and messages sent.</i></p>

<!-- more -->


<p>In this article, i am using a paid version of Hopper which cost about $60. I think it is an incredible price given the things we can do with this application. I would recommend you check out the demo version which lets you perform some tasks to get a feel of Hopper. Anyways, once you download the Hopper app, this is the interface we are looking at.</p>

<img src="/images/posts/ios28/1.png" width="1440" height="846" alt="1">

<p>In this article also, we will use the same demo application that we used in <a href="http://highaltitudehacks.com/2013/12/17/ios-application-security-part-26-patching-ios-applications-using-ida-pro-and-hex-fiend">Part 26</a>, the <a href="https://github.com/prateek147/gdb-demo">GDB-Demo</a> application that you can download from my github profile. I highly recommend that you read Part 26 before you proceeed with this article. Just to quickly recap, the GDB-Demo had a login form like this.</p>


<img src="/images/posts/ios28/2.png" width="316" height="484" alt="2">


<p>It accepts a certain username/password combination in order to allow us to login. Our task is to patch this application in such a way that the application allows us to login even if the username/password combination is not correct. Please note that in this article, we will be debugging and patching the application which is x86 architecture on a laptop , however you can do the same patching with ARM executable as well by copying the binary from the device.</p>


<p>Once you have downloaded the <a href="https://github.com/prateek147/gdb-demo">GDB-Demo</a> application, run it using Xcode. This will install the application in the IOS simulator. Now our task is to find the location of the application binary on our system. If you run an application in Xcode, it will generate an application directory inside the folder <i>/Users/$username/Library/Application Support/iPhone Simulator/$ios version of simulator/Applications/</i>. In my case, the location is <i>/Users/Prateek/Library/Application Support/iPhone Simulator 6.1/Applications/</i>. Once you are in this directory, you have to find your application folder. Using the command <i>ls -al</i> will give you the created date of these folders. The latest one would be our application.</p>
	
	
<img src="/images/posts/ios28/4.png" width="1145" height="239" alt="4">
	

<p>Use the command <i>open DirectoryName</i> and this will open the directory in Finder.</p>
	 
<img src="/images/posts/ios28/5.png" width="992" height="360" alt="5">

<p>Go inside the folder GDB-Demo.app (this is the application bundle) by right clicking on it and choosing the option <i>Show Package contents</i>. Inside this folder, you will find the application binary with the name GDB-Demo. This is the binary that we will provide to Hopper.</p>

<img src="/images/posts/ios28/6.png" width="770" height="303" alt="6">
<p>Now open Hopper app and go to File->Read Executable To Disassemble. Give the location of the GDB-Demo binary. Also make sure to quit Xcode but keep the simulator open.</p>


<img src="/images/posts/ios28/2.png" width="316" height="484" alt="2">


<p>Hopper will now start dissasembling the application.</p>

<img src="/images/posts/ios28/7.png" width="1437" height="805" alt="7">

<p>On the left hand side, if you select the <i>Strings</i> section, you will see all the constant strings that Hopper was able to dump from the binary. If you have read part 26 on this series, you will note that the password is also present in this list ;-).</p>

<img src="/images/posts/ios28/8.png" width="381" height="576" alt="8">

<p>If you select the labels section on the left, it will give you all the labels it was able to dump from the application. This includes labels to method implementations, constant strings, classes etc..</p>

<img src="/images/posts/ios28/9.png" width="389" height="582" alt="9">

<p>We know that the method that is important is loginButtonTapped. So lets search for it in the labels section. Once we find the method, tap on it and it will take you to its disassembly.</p>


<img src="/images/posts/ios28/10.png" width="1437" height="653" alt="10">

<p>One kickass feature of Hopper is that it can provide Pseudo code for a function. To check out the Pseudo code for this function, click on Pseudo Code on the top right.</p>

<img src="/images/posts/ios28/11.png" width="336" height="94" alt="11">

<p>As you can see, Hopper provides you with a Pseudo code for this function.</p>


<img src="/images/posts/ios28/12.png" width="868" height="748" alt="12">

<p>This is such an amazing feature. It helps us so much in figuring out what this method is supposed to do. In this case, it just gives away the password. Another awesome feature of this application is Show CFG which helps you find the flow of the application. Just click on <i>Show CFG</i> next to the Pseudo code button.</p>

<img src="/images/posts/ios28/13.png" width="737" height="813" alt="13">

<p>If we scroll down a bit in this function, we get to the point shown in the image below. We can see that the left flow takes us to a point where we see the text <i>Incorrect Username or Password</i>, whereas the right flow has some text <i>admin page</i>. Obviously, i would like the flow to go to the right side.</p>

<img src="/images/posts/ios28/14.png" width="1434" height="819" alt="14">

<p>Now lets check the condition that decides which way the flow goes. As we can see from the image below, the assembly instruction is <i>jne 0xcbc</i></p>

<img src="/images/posts/ios28/15.png" width="527" height="384" alt="15">

<p>0x2cbc is a label that corresponds to the right hand side. So if we can modify the instruction in such a way that the flow is always taken towards the right hand side, then our task will be accomplished.</p>

<img src="/images/posts/ios28/16.png" width="429" height="145" alt="16">

<p>Well, its pretty simple to do this. Just replace <i>jne 0xcbc</i> by <i>jmp 0xcbc</i>. To do this, click on this specific instruction in the dissassembly and click on Modify->Assemble instruction</p>


<img src="/images/posts/ios28/17.png" width="1217" height="425" alt="17">
<p>Then we write down the instruction that we want and click on Assemble and Go Next. That's it, this is the only change we want to do.</p>

<img src="/images/posts/ios28/18.png" width="720" height="250" alt="18">

<p>Now lets save this executable and overwrite the previous one. Go to File -> Produce New Executable and overwrite the original executable.</p>

<img src="/images/posts/ios28/19.png" width="608" height="396" alt="19">

<p>Now go the simulator application, quit any running instance of GDB-Demo application and restart the application. Tap on Login and you will see that the login has been bypassed.</p>

<img src="/images/posts/ios28/20.png" width="336" height="509" alt="20">

<p>Congratulations, we just patched an application using Hopper. This was just a small feature of Hopper. Hopper lets us do many more things. I would recommend you check them out and no, i am not associated with Hopper nor do i know the author personally. I just think its a cool app and for $60, its an extremely good deal !</p>

<p>In the next article, we will learn about Insecure or Broken Cryptography.</p>