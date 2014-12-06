---
layout: post
title: "iOS Application Security Part 26 – Patching iOS Applications using IDA Pro and Hex Fiend"
date: 2013-12-17 13:08
comments: true
categories: [security]
---

<p>In the <a href="http://highaltitudehacks.com/security">previous</a> applications we have looked at how we can hijack method implementations during runtime using Cycript, and even change the logic of the code rather than changing the complete implementation using GDB. All of these things have been done to serve a purpose, which is to make the application do what we want. However, using Cycript or GDB is a bit of a pain as one has to do repeat the same process everytime after you restart the application. This is where patching the application is useful. Once a change has been made in the application's binary, its permanent. So you don't have to repeat the same process over and over again. Once the binary is patched, you can then run it on a jailbroken device with the changed logic.</p>

<p>In this article, we will be using the same application <a href="https://github.com/prateek147/gdb-demo">GDB-Demo</a> that we had used in <a href="http://resources.infosecinstitute.com/ios-application-security-part-22-runtime-analysis-manipulation-using-gdb/">Part 22</a> of this series. If you remember, we had found a way to change the logic of the method that gets called when Login was tapped and hence bypassed the login authentication check. In this article, we are going to permanently patch this check so we are always authenticated.</p>

<!-- more -->

<p>The first thing you need to do is install the demo version of IDA Pro from their <a href="https://hex-rays.com/products/ida/index.shtml">website</a>. IDA Pro is a pretty awesome multi-processor disassembler and debugger. Once it is downloaded, open it up and choose the option <i>Go</i> which just opens up IDA without any preselected binary.  Please note that when you run an application on the simulator using Xcode, the code is compiled for the i386 architecture, whereas when you run the application on a device using Xcode, it is compiled for the ARM architecture. The demo version of IDA Pro supports both these architectures, however in this tutorial we are going to compile the application on i386 architecture (i.e on a simulator) to save the effort of copying the application binary from the device to our computer.</p>

<img src="/images/posts/ios26/1.png" style="width: 90%; height: 90%"/ >

<p>Now open Xcode and run the <a href="https://github.com/prateek147/gdb-demo">GDB-Demo</a> application that you had just downloaded using simulator.  ake sure the application builds successfully and that it installs propery on the simulator. This will generate an application directory inside the folder <i>/Users/$username/Library/Application Support/iPhone Simulator/$ios version of simulator/Applications/</i>. In my case, the location is <i>/Users/Prateek/Library/Application Support/iPhone Simulator 6.1/Applications/</i>. Once you are in this directory, you have to find your application folder. Using the command <i>ls -al</i> will give you the last modified date of these folders. The latest one would be our application.</p>

<img src="/images/posts/ios26/2.png" style="width: 90%; height: 90%"/>
	
<p>Use the command <i>open DirectoryName</i> and this will open the directory in Finder.</p>
	
<img src="/images/posts/ios26/3.png" style="width: 50%; height: 50%"/>

<p>Go inside the folder GDB-Demo.app (this is the application bundle) by right clicking on it and choosing the option <i>Show Package contents</i>. Inside this folder, you will find the application binary with the name GDB-Demo. This is the binary that we will provide to IDA Pro.</p>

<img src="/images/posts/ios26/4.png" style="width: 50%; height: 50%"/>

<p>Now drag and drop the application binary on the IDA pro icon. Click on Ok and proceed.</p>

<p>You will see the disassembled code like this.</p>

<img src="/images/posts/ios26/6.png" style="width: 80%; height: 80%"/>

<p>Coming back to the application, we know that the application has a login page like the one shown below. We had already modified the logic of this application using GDB in <a href="http://resources.infosecinstitute.com/ios-application-security-part-22-runtime-analysis-manipulation-using-gdb/">Part 22</a>. We also know that the method whose logic was changed was <i>-(IBAction)loginButtonTapped:(id)sender</i></p>

<img src="/images/posts/ios26/7.png" style="width: 20%; height: 20%"/>

<p>In IDA Pro, you can see a functions window on the left. Choose any function from the list of functions and press <i>Ctrl+F</i>. Now search for the function loginButtonTapped. Once you have found it, double click on it. You will be shown the dissassembly for this function on the right side.</p>

<img src="/images/posts/ios26/8.png" style="width: 80%; height: 80%"/>

<p>To view this in sort of a graphical format, double click on the function name in the functions window and press <i>Space</i>. The view will change to something like this. This is a better way of examining the disassembly as it also helps us in understanding the flow of the function. If you want to switch to the previous view, you can press <i>Space</i> again.</p>

<img src="/images/posts/ios26/9.png" style="width: 80%; height: 80%"/>

<p>Scroll down in the function disassembly on the right side. You can see that the flow of the function can lead to different blocks of code depending on specific conditions. It is obvious that somewhere within this function there is a check for whether the username and password are correct or not and authenticate or disallow the user. After scrolling down for a bit, we arrive at this block. This looks very interesting. On the right side, i can see <i>UIAlertView</i> in the code section whereas the left section shows a string named <i>adminPage</i>. </p>

<img src="/images/posts/ios26/10.png" style="width: 80%; height: 80%"/>

<p>I would really like the flow to go to the left hand side, where it says <i>adminPage</i>. The instruction that decides which block of code the execution will jump to is just one instruction before.</p>

<img src="/images/posts/ios26/11.png" style="width: 50%; height: 50%"/>

<p>It says <i>jnz loc_2CBC</i>, where <i>loc_2CBC</i> is a label and jnz stands for <i>Jump if not zero</i> instruction. We can see that the code block on the left contains this label.</p>

<img src="/images/posts/ios26/12.png" style="width: 50%; height: 50%"/>

<p>This means execution will jump to left if the zero flag is not set. If i can modify the instruction <i>jnz</i> to <i>jz</i>, then my purpose would be solved as the logic would be reversed and i will be authenticated. So what do i need to convert this from jnz to jz.</p>

<p>MORE MONEY !</p>

<p>Well, that was a bit of humour. A licensed version of IDA Pro will get the job done for you and you can simply modify this instruction. However, we are going to do this the free way, even though it requires a bit of extra effort and calculation but its worth it and we will also learn a few new things on the way. In the next article, we will also discuss an alternative named Hopper that is not as expensive as IDA but very good in terms of functionality. For that first we need to find the address of the <i>jnz</i> instruction. To do that, double click on this instruction so that it gets yellow...</p> 
 	
<img src="/images/posts/ios26/13.png" style="width: 50%; height: 50%"/>

<p>Now press Space..</p>

<img src="/images/posts/ios26/14.png" style="width: 90%; height: 90%"/>

<p>As we can clearly see, the address of this instruction is <i>00002CB1</i>. However, we cannot just go ahead and change the address at this instruction, This is because this address is the absolute address of this instruction and it will be different every time the application is launched. What we need to find out is the offset of this instruction relative to the Mach-O binary. This instruction has to be modified in the code section of the assembly. Hence the offset of this instruction relative to the binary can be calculated as ..</p>

<p><b>(Offset of code section relative to binary) + (Absolute address of the instruction to be changed - Starting address of the code section)</b></p>

<p>Right now we just know the Absolute address of the instruction to be changed. We can find the other two things using otool. Browse to the application directory <i>/Users/Prateek/Library/Application Support/iPhone Simulator/6.1/Applications/1804F89F-AD44-4782-BB29-47F5C521D10D/GDB-Demo.app</i> and use the following command as shown in the image below.</p>

<img src="/images/posts/ios26/15.png" style="width: 80%; height: 80%"/>

<p>Look for the text section.</p>

<img src="/images/posts/ios26/16.png" style="width: 50%; height: 50%"/>

<p>As you can see, the starting address is 0x000026f0(Hex) and the offset is 5872(Decimal). Please note that these things may be different in your case.</p>

<p>Hence, using these values and the above equation we can find the offset as ..</p>

<p><b>5872(Decimal) + (0x00002CB1(Hex)- 0x000026f0(Hex)) =  0x1cb1</b></p>

<p>Now, as we discussed earlier, we need to replace the jnz instruction with a jz instruction. You can see from <a href="http://www.unixwiz.net/techtips/x86-jumps.html">this</a> link that the opcode for the JNZ instruction is <i>OF 85</i> whereas the opcode for the JZ instruction is <i>OF 84</i>.</p>

<p>Now download the application <i>Hex Fiend</i> and open it up. Drag and drop the application binary to it.</p>

<img src="/images/posts/ios26/17.png" style="width: 90%; height: 90%"/">

<p>Now click on Edit --> Jump to Offset and type the offset as 0x1cb1. This will take you to the line with the jnz instruction. </p>


<img src="/images/posts/ios26/18.png" style="width: 90%; height: 90%"/>

<p>Now look for the opcode OF 85. Change it to 0F 84 as shown below.</p>

<img src="/images/posts/ios26/19.png" style="width: 90%; height: 90%"/>

<p>Now save your changes and exit Hexfiend. As you remember, we had installed the app previously in the simulator. So fire up the iOS simulator, quit the GDB-Demo app if it is running and open it again. Now just tap on Login without entering anything in the username and password. It will direct you to the admin page.</p>
	
<img src="/images/posts/ios26/20.png" style="width: 20%; height: 20%"/>

<p>Perfect, we just patched a binary using old school techniques. In the next article, we will look at a tool named Hopper and learn how to patch iOS applications using it. </p>
