---
layout: post
title: "iOS Application Security Part 21 – ARM and GDB Basics"
date: 2013-11-08 14:50
comments: true
categories: [security]
---

<p>All the iOS devices released uptil now are based on the ARM architecture. All the Objective-C code that we write while developing iOS applications is first converted into ARM assembly before being converted to machine code (1s and 0s). With good understanding of the ARM assembly language and with good knowledge of debugging using GDB, it is possible for a hacker to decipher the Objective-C code during runtime and even modify it.</p>

<p>For this article, we will be using a sample application <a href="https://github.com/prateek147/gdb-demo">GDB-Demo</a> from my github account. Make sure to install and run it on your iOS device. If you don't have a registered developer account to run this on your device, you can follow the instructions mentioned <a href="http://highaltitudehacks.com/2013/07/25/ios-application-security-part-7-installing-and-running-custom-applications-on-device-without-a-registered-developer-account">here</a></p>.

<p>Now let's SSH into the device.</p>
	
<img src="/images/posts/ios21/1.png" width="465" height="108" alt="1">

<!-- more -->
	
<p>Now lets start GDB and ask GDB to hook into our application as soon as it is started. This is done by using the command <i>attach --waitfor Appname</i>. Optionally, you can also start the application on your device and hook into the running process using the <i>attach</i> command as shown below.</p>.
	
<img src="/images/posts/ios21/2.png" width="1059" height="422" alt="2">
	
<p>Now once GDB has hooked into the application, you will notice that the application is in a paused state. You can resume the application by just using the <i>c</i> command. But let's do some investigation before that. Just like any other architecture, the memory in ARM is divided into registers. All these registers are 32 bit in size (except in iOS 7 which is 64 bit) and their purpose is to hold and move data between each other. You can find information about these registers  by just using the <i>info registers</i> command.</p>
	
<img src="/images/posts/ios21/3.png" width="1089" height="358" alt="3">
	
<p>Please note that this command does not print out all the registers in ARM. To print all the registers, use the command <i>info all-registers</i></p>
	
	
<img src="/images/posts/ios21/4.png" width="882" height="590" alt="4">
	<p>To dump the diassembly, just use the <i>disassemble</i> or <i>disas</i> command. This will give us the assembly dump for the next few instructions. We can also dump the assembly of a particular function by specifying the function name after the <i>disas</i> command. For e.g, to dump the assembly for the main function, use the <i>disas main</i> command.</p>
	
<img src="/images/posts/ios21/5.png" width="642" height="364" alt="5">
	
<p>If we look at the application that we just installed on our device, we will see that it is just a simple application that prompts for a username and password.</p>
	
<img src="/images/posts/ios21/IMG_0099.PNG" width="320" height="480" alt="IMG 0099">
	
<p>We can also see from the class-dump-z output of the application that there is a class named <i>ViewController</i> and a method named <i>-(void)loginButtonTapped:(id)tapped;</i></p>
	
<img src="/images/posts/ios21/6.png" width="724" height="237" alt="6">
	
<p>With GDB, we can also set breakpoints in the application. You can type the method names to set breakpoints to. Just use the command <i>b functionName</i>. You can also specify the method signature without the class if you are unsure and GDB will ask you which class you want to set the breakpoint on.</p>

<img src="/images/posts/ios21/7.png" width="546" height="228" alt="7">
	
<p>Please note that instance methods are prefixed with a minus sign <i>-</i> whereas class methods are prefixed with a plus sign <i>+</i> as shown below. For e.g, <i>sharedInstance</i> is a class method that returns the shared instance of a singleton class.</p>
	
	
<img src="/images/posts/ios21/8.png" width="622" height="564" alt="8">
	<p>You can see all your breakpoints using the command <i>info breakpoints</i>.</p>
	
<img src="/images/posts/ios21/9.png" width="774" height="89" alt="9">
	
<p>You can remove any breakpoint by using the command <i>delete</i> followed by the ID of the breakpoint.</p>
<img src="/images/posts/ios21/10.png" width="767" height="89" alt="10">

	
<p>Anyways, lets set a breakpoint for the method <i>loginButtonTapped:</i>.</p>
	
<img src="/images/posts/ios21/11.png" width="314" height="44" alt="11">
	
<p>We can then resume the app by using the <i>continue</i> or the <i>c</i> command.</p>
	
<img src="/images/posts/ios21/12.png" width="213" height="38" alt="12">
	
<p>Lets tap on the login button in the app. This will trigger our breakpoint.</p>
	
	
<img src="/images/posts/ios21/13.png" width="648" height="96" alt="13">
	<p>We can then find some of the next instruction by using the disassemble command.</p>
	
<img src="/images/posts/ios21/14.png" width="712" height="468" alt="14">
	
<p>To set breakpoint any particular instruction, add the asterix sign before the address for that instruction.</p>

<img src="/images/posts/ios21/15.png" width="275" height="40" alt="15">
	
<p>In <a href="http://highaltitudehacks.com/2013/06/16/ios-application-security-part-3-understanding-the-objective-c-runtime/">part 3</a> of this article series, we also learnt about the function objc_msgSend. Objective-C is based on messaging and whenever a message is being sent, the objc_msgSend fucntion gets called. In the disassembly code for the function <i>loginButtonTapped:</i> that we printed out, there are a lot of objc_msgSend calls. A good way to find this out is to look for the <i>blx</i> instruction. Wherever you see a blx instruction, you can be sure that an objc_msgSend call is happening.</p>
	
<img src="/images/posts/ios21/16.png" width="818" height="193" alt="16">
	
<p>Whenever a new method is called or a property is accessed, the objc_msgSend function gets called. So if we can set a breakpoint for the objc_msgSend call and find a way to print out the method being called and the object that calls this method, then this will give us a very good insight into the flow of the app. We had already looked at how Snoop-it was able to find out all the traced calls in <a href="http://highaltitudehacks.com/2013/08/20/ios-application-security-part-9-analyzing-security-of-ios-applications-using-snoop-it">part 9</a> of this series. To find out the methods being called, first we need to look at calling convention for ARM. Here is a screenshot of the ARM calling convention from <a href="http://en.wikipedia.org/wiki/Calling_convention">Wikipedia</a>.</p>
	

<img src="/images/posts/ios21/17.png" width="1262" height="482" alt="17">
<p>The important line to note here is this.</p>
	
<img src="/images/posts/ios21/18.png" width="794" height="24" alt="18">
	
<p>So we can set a breakpoint for every objc_msgSend call and find the parameters passed to this function by using the values of r0-r3 registers. Let's have a look at the signature for objc_msgSend call. Here is a screenshot taken from Apple's <a href="https://developer.apple.com/library/mac/documentation/cocoa/reference/objcruntimeref/Reference/reference.html#//apple_ref/c/func/objc_msgSend">documentation</a>.</p>	
	
<img src="/images/posts/ios21/19.png" width="1212" height="459" alt="19">
	
<p>So the first two arguments to the function are <i>self - A pointer that points to the instance of the class that is to receive the message</i> and <i>op -
The selector of the method that handles the message.</i>. The selector is nothing but the signature for the message. For e.g if a method has the prototype <i>-(void)addOjectsToArray:(NSArray *)array</i> then the signature for it would be <i>addOjectsToArray:</i>. And we also know that r0 to r3 are used to hold argument values passed to a subroutine, hence we can conclude that <i>r0 will contain self</i> and <i>r1 will contain op</i>.</p>

<p>Let's understand this through an example. Set up a breakpoint for the objc_msgSend call and continue until the breakpoint is hit.</p>

<img src="/images/posts/ios21/20.png" width="429" height="122" alt="20">

<p>As we learnt, r0 should contain a pointer to the instance of the class that is to receive the message, r1 should contain the selector and after that starting from r2 should be the arguments for the method. But first we should learn about the <i>x</i> command. x stands for examine and helps us to examine memory in many different formats. So we can specify the format in which we want to examine the memory. To find out all the options available with this command, use the <i>help x</i> command.</p>

<img src="/images/posts/ios21/21.png" width="641" height="243" alt="21">

<p>Let's start by examining r0. We know it will contain a pointer to the instance of the class that will receive the message. Hence the format is address and we use x/a. We use $ before r0 because we want to examine the memory, and hence the $ sign.</p>

<img src="/images/posts/ios21/22.png" width="553" height="56" alt="22">

<p>We can see that the receiver is an instance of the UIRoundedRectButton class. Now lets try to examine the value in r1 register. We know that it contains the selector, i.e the signature of the method. This is a string and hence we use x/s. </p>

<img src="/images/posts/ios21/23.png" width="390" height="63" alt="23">

<p>Now, we have to find out the arguments to this method. This can be tricky as we don't know what format is r2. But looking at the selector which is <i>respondsToSelector:</i> and using some common sense, we can assume that the argument will be a selector, and hence we again use x/s to examine the memory in string format.</p>

<img src="/images/posts/ios21/24.png" width="369" height="61" alt="24">


<p>Alright, so the argument is <i>debugDescription</i>. From the method selector, we can see that there is only one argument to this function, hence we don't worry about examining further registers. So now, we can say that the method being called must be something like this.</p>

<p>-[UIRoundedRectButton respondsToSelector:@selector(debugDescription)];</p>

<p>But there are so many objc_msgSend calls and examining all of these calls one by one is such a pain. So lets try and automate this process. In <a href="http://highaltitudehacks.com/2013/06/16/ios-application-security-part-3-understanding-the-objective-c-runtime/">part 3</a> of this series, we learnt how to use the commands option in gdb to print something whenever a breakpint is hit. Let's use it here as well.</p>
<img src="/images/posts/ios21/25.png" width="543" height="137" alt="25">


<p>Now press <i>c</i> to continue. You will see all the methods being called. This tells us so much about what is happening in the application.</p>

<img src="/images/posts/ios21/26.png" width="762" height="665" alt="26">

<p>Now let's try and print this in Objective-C like syntax. We are going to use the function class_getName mentioned in Apple's <a href="https://developer.apple.com/library/mac/documentation/cocoa/reference/objcruntimeref/Reference/reference.html#//apple_ref/c/func/objc_msgSend">documentation</a>. As you can see, it requires an argument which is the class object, hence we will pass it r0.</p>

<img src="/images/posts/ios21/27.png" width="512" height="295" alt="27">

<p>Now rewrite the calls in commands like this.</p>

<img src="/images/posts/ios21/28.png" width="674" height="152" alt="28">

<p>Type c to continue and now as you can see, the information is in a much more readable format.</p>
<img src="/images/posts/ios21/29.png" width="825" height="531" alt="29">


<p>This tells us so much about what is happening inside an application. In the next article, we will take our knowledge gained from this article and learn how to perform runtime manipulation with GDB.</p>

