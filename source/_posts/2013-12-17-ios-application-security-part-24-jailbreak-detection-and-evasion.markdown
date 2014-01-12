---
layout: post
title: "IOS Application Security Part 24 – Jailbreak Detection and Evasion"
date: 2013-12-17 12:33
comments: true
categories: [security]
---

<p>In this article, we will look at the checks a developer can incorporate in his application to check whether the device on which the application is running is jailbroken or not. Checking whether a device is jailbroken or not can have many advantages for your application. As we have already seen, an attacker can run tools like Cycript, GDB, Snoop-it etc to perform runtime analysis and steal sensitive data from within your application. If you are really looking to add an extra layer of security for your application, you should not allow your application to be run on a jailbroken device. Please note that millions of users jailbreak their devices and hence not allowing an application to be run on a jailbroken device could have a significant impact on your user base. Another thing you can do is instead block some of the features in your application rather than disabing it entirely. We will also look at how hackers can bypass the check for jailbreak detection in your application using Cycript.</p> 

<p>Once a device is jailbroken, a lot of other files and applications are installed on the devcice. Checking for these files in the filesystem can help us identify whether the device is jailbroken or not. For e.g, most of the jailbreak softwares install Cydia on the device after jailbreaking. Hence just a simple check for the file path of Cydia can determine whether the device is jailbroken or not.</p>
<!-- more -->


<pre>
	NSString *filePath = @"/Applications/Cydia.app";
	if ([[NSFileManager defaultManager] fileExistsAtPath:filePath])
	{
		//Device is jailbroken
	}
</pre>

<p>However, not all devices that are jailbreaked have Cydia installed on them. In fact, most hackers can just change the location of the Cydia App. Checking for many other files related to Jailbroken devices can make this method much more efficient. For e.g, one can check if Mobile Substrate is installed on the device or not, which many applications require to run on a jailbroken device. One can also check for the location of the SSH Daemon, or the shell interpreter. Combining all these checks, we get a method like this.</p>


<pre>
	+(BOOL)isJailbroken{
  
	  if ([[NSFileManager defaultManager] fileExistsAtPath:@"/Applications/Cydia.app"]){
	      return YES;
	    }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/Library/MobileSubstrate/MobileSubstrate.dylib"]){
	      return YES;
	    }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/bin/bash"]){
	      return YES;
	    }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/usr/sbin/sshd"]){
	      return YES;
	    }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/etc/apt"]){
	      return YES;
	    }
	  return NO;
	}
</pre>

<p>We have also learnt from the previous articles that applications that run as a mobile user run in a sandboxed environment and go inside the directory <i>/var/mobile/Applications</i> whereas applications that run with the root user (e.g Apple's preloaded applications) aren't subject to any sandbox environment and go inside the directory <i>/Applications</i>. A user running a jailbroken device can install your application in the /Applications folder thereby giving it root privileges. Hence, adding a check to see whether the application follows sandboxing rules can help the user identify whether the application is jailbroken or not. A good way to check for it would be to see if we can modify a file in some other location outside the application bundle.</p>

<pre>
    NSError *error;
   	NSString *stringToBeWritten = @"This is a test.";
   	[stringToBeWritten writeToFile:@"/private/jailbreak.txt" atomically:YES
             encoding:NSUTF8StringEncoding error:&error];
   	if(error==nil){
       //Device is jailbroken
       return YES;
     } else {
       //Device is not jailbroken
       [[NSFileManager defaultManager] removeItemAtPath:@"/private/jailbreak.txt" error:nil];
     }
   
</pre>

<p>We know that a skilled hacker can just modify the location of the application. However, we know that 80% or more of the devices that are jailbroken have Cydia installed on them, and even if the hacker can change the location of the Cydia app, he most probably won't change the URL scheme with which the Cydia app is registered. If calling the Cydia's URL scheme (cydia://) from your application gives a success, you can be sure that the device is jailbroken.</p>

<pre>
    if([[UIApplication sharedApplication] canOpenURL:[NSURL URLWithString:@"cydia://package/com.example.package"]]){
      //Device is jailbroken
    }
</pre>

<p>Let's also add a condition to make sure this code does not execute if we are testing our application on a simulator and not an actual device. After combining all the above techniques, our method looks like this.</p>

<pre>

	+(BOOL)isJailbroken{
  
	#if !(TARGET_IPHONE_SIMULATOR)
  
	   if ([[NSFileManager defaultManager] fileExistsAtPath:@"/Applications/Cydia.app"]){
	      return YES;
	    }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/Library/MobileSubstrate/MobileSubstrate.dylib"]){
	      return YES;
	    }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/bin/bash"]){
	      return YES;
	    }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/usr/sbin/sshd"]){
	      return YES;
	    }else if([[NSFileManager defaultManager] fileExistsAtPath:@"/etc/apt"]){
	      return YES;
	    }
  
	  NSError *error;
		NSString *stringToBeWritten = @"This is a test.";
		[stringToBeWritten writeToFile:@"/private/jailbreak.txt" atomically:YES
	          encoding:NSUTF8StringEncoding error:&error];
		if(error==nil){
	    //Device is jailbroken
	    return YES;
	  } else {
	    [[NSFileManager defaultManager] removeItemAtPath:@"/private/jailbreak.txt" error:nil];
	  }
 
	  if([[UIApplication sharedApplication] canOpenURL:[NSURL URLWithString:@"cydia://package/com.example.package"]]){
	    //Device is jailbroken
	    return YES;
	  }
	#endif
  
	  //All checks have failed. Most probably, the device is not jailbroken
	  return NO;
	}
</pre>

<p>Honestly speaking, there is no foolproof method of detecting jailbroken devices. A skilled hacker will always find a way to bypass these checks. He can simply find the instructions in the binary and replace all instructions with No-op. He can also swizzle your method implementation with his own using Cycript.</p>

<p>He can first find the class information of the application using Class-dump-z. Over here, he can see a method named +(BOOL)isJailbroken in the JailbreakDetector class. Note that it is a class method as it begins with positive sign. It obviously means this method checks whether a device is jailbroken or not and returns YES if the device is jailbroken. If you are not getting any of this, you should consider reading <a href="http://highaltitudehacks.com/security">previous</a> articles.</p>

<img src="/images/posts/ios24/1.png" width="990" height="436" alt="1">


<p>He can then hook into this application using Cycript.</p>

<img src="/images/posts/ios24/2.png" width="1339" height="119" alt="2">
<p>And then print out all the methods for the JailbreakDetector class. Please note that we are using JailbreakDetector->isa.messages because isJailbroken is a class method. To find the instance methods, just using JailbreakDetector.messages would have worked for us.</p>

<img src="/images/posts/ios24/3.png" width="1437" height="519" alt="3">

<p>And then he can swizzle the method implementation with his own that always returns a NO. If you are not getting this, i suggest that you read the article on <a href="http://highaltitudehacks.com/2013/07/25/ios-application-security-part-8-method-swizzling-using-cycript">Method Swizzling</a>.</p>

<img src="/images/posts/ios24/4.png" width="641" height="101" alt="4">

<p>As a developer, what we can do is change the method name to something that doesn't look quite appealing to the hacker. For e.g, the className JailbreakDetector could be renamed as ColorAdditions and the method +(BOOL)isJailbroken could be replaced by +(BOOL)didChangeColor with the implementation being the same. Something like this wouldn't attract the attention of the hacker. He can always look at the calls that are being made inside this method using Snoop-it, GDB etc, but a small change like this can surely help in confusing him.</p>
