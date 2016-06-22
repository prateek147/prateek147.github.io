---
layout: post
title: "iOS Application Security Part 42 - LLDB Usage continued"
date: 2015-05-12 00:17
comments: true
categories: [security]
---

<p>In this article, we will look at some of the most important commands in LLDB to debug applications.</p>

<p>If you have been following this blog series, you would have noticed that we have been using GDB until now for debugging applications, but the support for GDB has been disabled by Apple. Apple has compiled a very useful list of GDB to LLDB commands to get you up to date with debugging via LLDB that can be found <a href="https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/lldb-command-examples.html">here</a>.</p>

<p>We will look at some of the most important commands after hooking into an application. In this case, lets start debugging the Twitter app. So make sure that the Twitter app is running in the foreground on the device and start a listener for the Twitter app.</p>

<img src="/images/posts/ios42/1.png" width="604" height="156" alt="1">

<!-- more -->

<p>On your system, do the usual process of connecting to the debugserver application on the device to perform remote debugging. You can also use usbmuxd if you feel that debugging over Wifi is slow.</p>

<img src="/images/posts/ios42/2.png" width="913" height="653" alt="2">

<p>Once the connection has been established, you can now run debbugger commands to analyze the application. Let's print out the AppDelegate object.</p>

<img src="/images/posts/ios42/3.png" width="485" height="62" alt="3">


<p>We can read registers using the <i>register read</i> command. To read all the registers, use the <i>register read --all</i> command.</p>

<img src="/images/posts/ios42/4.png" width="706" height="613" alt="4">

<p>Note the difference in the registers ? It's because of the new arm64 architecture for this application. Another important command is <i>image list</i> which will let you identify the location of the main executable and all the shared libraries.</p>


<img src="/images/posts/ios42/5.png" width="963" height="534" alt="5">

<p>The <i>image dump sections</i> command will dump all the sections of the main executable and the shared libraries. You can later use this to dump information from the memory.</p>

<img src="/images/posts/ios42/6.png" width="1107" height="534" alt="6">

<p>Setting a breakpoint is very similar to GDB. First, see if there are any breakpoints set using the <i>br l</i>. Then set a breakpoint for the objc_msgSend function using the <i>b objc_msgSend</i> command. Then, resume the application using the <i>process continue</i> command.</p>


<img src="/images/posts/ios42/7.png" width="716" height="80" alt="7">

<img src="/images/posts/ios42/8.png" width="441" height="222" alt="8">

<p>Once a breakpoint is hit, you can use the command <i>di -f</i> to see the disassembled code.</p>

<img src="/images/posts/ios42/9.png" width="702" height="331" alt="9">

<p>We can also configure LLDB to execute a command once every breakpoint is hit. This could be very handy in tracing method calls in the application. To do that, use the command <i>target stop-hook add</i> and enter the commands that you want to enter once the breakpoint is hit. In this case, i have asked LLDB to print out all the registers and continue the program execution. </p>

<img src="/images/posts/ios42/10.png" width="961" height="484" alt="10">

<p>If you don't understand the purpose of these registers right now, don't worry. I will cover arm64 architecture in a later article.</p>

<p>It is usually a good idea to strip the debug symbols from the application binary before submitting to the App store. You can do this by going to Build Settings and set the option <i>Strip Debug Symbols During Copy</i> to Yes.</p>

<img src="/images/posts/ios42/11.png" width="937" height="509" alt="11">

<p>Hope you enjoyed this article. We have just scratched the surface of LLDB right now. In the next article, we will look at importing symbols from binaries and settings breakpoints on application specific methods.</p>