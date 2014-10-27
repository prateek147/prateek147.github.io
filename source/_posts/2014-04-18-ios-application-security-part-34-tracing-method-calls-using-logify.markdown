---
layout: post
title: "iOS Application Security Part 34 - Tracing Method calls using Logify"
date: 2014-04-18 16:59
comments: true
categories: [security]
---


<p>In the previous articles, we have seen how applications like Snoop-it can trace method calls specific to the application at runtime. This is very important in deducing the flow of the application. The same process can be performed by using a perl script named Logify.pl that comes installed with Theos. The script takes input as a header file and generates the hooking code that we can add in our tweak. We can also specify the classes we want to check. Once the tweak is installed on the device, whenever a method for that particular class is called, the tweak logs out the method along with the arguments to syslog. The first step here is to get the header files for a particular application. You can get the header files by using the -H option in class-dump-z. Once the headers folder is generated, you can copy it to your system.</p>
		
<img src="/images/posts/ios34/1.png" width="1389" height="315" alt="1">

<!-- more -->

		
<p>Now we can use the Logify.pl script on these header files to generate our tweak. In this case, we are testing on <a href="http://damnvulnerableiosapp.com">Damn Vulnerable iOS application</a>. Let's add all the classes for which we want to log the method calls. In our case, we select three classes for which we want to trace the method calls, ClientSideInjectionVC, JailbreakDetectionVC and DamnVulnerableAppUtilities.</p>
		
<img src="/images/posts/ios34/2.png" width="773" height="65" alt="2">
		
<p>This is how our Tweak.xm file looks like.</p>
		
<img src="/images/posts/ios34/3.png" width="883" height="458" alt="3">
		
<p>Now let's create a tweak and replace its Tweak.xm file with our own. Also, give the filter as the bundle identifier for DVIA, as we only want to trace calls for DVIA. Have a look at the <a href="http://highaltitudehacks.com/2014/04/18/ios-application-security-part-33-writing-tweaks-using-theos-cydia-substrate">previous</a> article if you are new to writing tweaks.</p>
		
<img src="/images/posts/ios34/4.png" width="867" height="392" alt="4">
	
<p>Build the package.</p>
	
<img src="/images/posts/ios34/5.png" width="1251" height="103" alt="5">
	
<p>Now install it on your device and respring your device.</p>
	
<img src="/images/posts/ios34/6.png" width="1069" height="238" alt="6">

<p>Now check the folder <i>/Library/MobileSubstrate/DynamicLibraries</i> on your device to see whether the tweak was installed, and sure enough, we can see that it has been installed.</p>
	
<img src="/images/posts/ios34/7.png" width="309" height="388" alt="7">
	
<p>Now run the DVIA app. Make sure your device is connected to your computer and go to Xcode –> Window –> Organizer –> Devices & select your device and click on Console. You will see the DVIAMethodTracer tweak being loaded into your application.</p>
	
<img src="/images/posts/ios34/8.png" width="1146" height="250" alt="8">
	
<p>And now as you browse around in the application and invoke methods for the particular classes that we have set up Logify for, you will see that these methods are logged along with the value of their arguments.</p>
	
<img src="/images/posts/ios34/9.png" width="1263" height="358" alt="9">
	
<p>Logify can be a very useful tool in figuring out the order in which methods are called and hence deducing a lot about the flow of the application.</p>
	