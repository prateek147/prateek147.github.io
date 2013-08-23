---
layout: post
title: "IOS Application Security Part 8 -  Method Swizzling using Cycript"
date: 2013-07-25 15:16
comments: true
categories: [ios, security]
---

<p>In the previous article, we looked at how we can install custom apps on our device without a developer certificate. In this article, we will look at how we can perform method Swizzling using Cycript on a sample application.</p>

<p>The first thing is to download the sample Xcode project. You can download the Xcode project from <a href="https://dl.dropboxusercontent.com/u/34557464/MethodSwizzlingDemo.zip">here</a>. Or you can also just download the binary file on your device from <a href="https://dl.dropboxusercontent.com/u/34557464/MethodSwizzlingDemo.ipa">here</a>. If you have installed the Xcode project, you will have to build the Xcode project using a self signed certificate.The previous article talks about this in great detail. If you have downloaded the binary, you can directly run it on a jailbroken device without any issues but to get a look at the source code, it is recommended to download the Xcode project.</p>

<!-- more -->

<p>Also please make sure to sign the Xcode project with your own certificate.</p>

<img src="/images/posts/ios8/Self-signedcert.png" width="1143" height="343" alt="Self Signedcert">

<p>Once you have the app running on your device, ssh into your device and hook into the running process using Cycript.</p>

<p>You can hook into any running process with this command <code>cycript -p [APP_ID]</code></p>

<img src="/images/posts/ios8/1.png" width="817" height="170" alt="1">

<p>As you can see on the device, this application has a login form. Please note that for this article, we will only be bypassing login on tap of the <code>Login Method 1</code> button</b>.</p>

<img src="/images/posts/ios8/IMG_0102.PNG" width="320" height="480" alt="IMG 0102">

<p>The username and password are <code>admin:password</code>. It will take us to the admin page.</p>

<img src="/images/posts/ios8/IMG_0103.PNG" width="320" height="480" alt="IMG 0103">

<p>If we enter an incorrect username/password combination, we will be prompted with an alert.</p>


<img src="/images/posts/ios8/IMG_0104.PNG" width="320" height="480" alt="IMG 0104">
<p>Our goal hence is to bypass this login view.</p>

<p>So first, lets find the root view controller of this application. Use this command in cycript <code>UIApp.keyWindow.rootViewController</code></p>

<img src="/images/posts/ios8/2.png" width="334" height="30" alt="2">

<p>Ok, since the login page is the first view that we get to see in the application, we can be sure that the view controller class responsible for displaying this view is a part of the navigation controller that we found with the previous command. We can find the current view by finding the <code>visibleViewController</code> property of the navigation controller</p>

<img src="/images/posts/ios8/3.png" width="436" height="97" alt="3">

<p>Perfect. Now let's write a function to print out all the methods for this view controller. This method is taken from the Cycript tricks <a href="http://iphonedevwiki.net/index.php/Cycript_Tricks">page</a>. I recommend you to have a look at this page for many useful code snippets.</p>

<p>Here is the function i am using.</p>

<pre>
	function printMethods(className) {
	  var count = new new Type("I");
	  var methods = class_copyMethodList(objc_getClass(className), count);
	  var methodsArray = [];
	  for(var i = 0; i < *count; i++) {
	    var method = methods[i];
	    methodsArray.push({selector:method_getName(method), implementation:method_getImplementation(method)});
	  }
	  free(methods);
	  free(count);
	  return methodsArray;
	}
</pre>

<img src="/images/posts/ios8/4.png" width="816" height="182" alt="4">

<p>Now lets print out the methods for the current view controller. Please note that the parameter this method takes is the className, which in this case is <code>ViewController</code></p>

<img src="/images/posts/ios8/5.png" width="981" height="105" alt="5">

<p>Another way to know about the method names is by using the isa.messages property. According to Apple's <a href="http://developer.apple.com/library/ios/#DOCUMENTATION/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html#//apple_ref/doc/uid/TP40008048-CH104-SW1">documentation</a> isa is basically a pointer to the class structure.</p>


<p>Here is a line taken from the same page.</p>

<p><code>When a new object is created, memory for it is allocated, and its instance variables are initialized. First among the object’s variables is a pointer to its class structure. This pointer, called isa, gives the object access to its class and, through the class, to all the classes it inherits from.</code></p>

<p>This image from Apple's <a href="http://developer.apple.com/library/ios/#DOCUMENTATION/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html#//apple_ref/doc/uid/TP40008048-CH104-SW1">documentation</a> will make things more clear.</p>

<img src="/images/posts/ios8/6.png" width="614" height="583" alt="6">

<p>So what is the <code>messages</code> property ? Well, first we must know what is a dispatch table. A dispatch table is a table containing entries that associate method selectors with the class-specific addresses of the methods they identify. Let's have a look at this image taken from Apple's <a href="http://developer.apple.com/library/ios/#DOCUMENTATION/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html#//apple_ref/doc/uid/TP40008048-CH104-SW1">documentation</a>.</p>

<img src="/images/posts/ios8/7.png" width="1185" height="109" alt="7">

<p>So now, it is easy to guess that the <code>messages</code> property is the complete list of messages that could be sent to an instance of the class or the class itself. This will be a huge list because the isa pointer will pick messages from all the superclasses also, leading up to NSObject. A very important line to be noted in the image above is this. <code>This is the way that method implementations are chosen at runtime—or, in the jargon of object-oriented programming, that methods are dynamically bound to messages.</code></p>

<p>Since the methods are dynamically bound to messages at runtime, it is possible for us to change a method implementation for a particular message.</p>


<p>In this case, let's print out the messages for the App Delegate class.</p>

<img src="/images/posts/ios8/8.png" width="1269" height="499" alt="8">

<p>Just using the messages property will also work.</p>

<img src="/images/posts/ios8/9.png" width="1271" height="539" alt="9">

<p>In this case, the view controller that we are concerned with is the one used to display the login page, which we found out earlier to be named as <code>ViewController</code>. So let's display all the messages for this VC.</p>

<img src="/images/posts/ios8/10.png" width="1179" height="610" alt="10">

<p>On the top of the output, you could see some of the methods for this VC.</p>

<img src="/images/posts/ios8/11.png" width="1180" height="47" alt="11">

<p>The method <code>validateLogin</code> looks interesting. Let's have a look at this method from the class-dump-z output. In case you are not familiar with class-dump-z, please have a look at the <a href="http://resources.infosecinstitute.com/ios-application-security-part-2-getting-class-information-of-ios-apps/">2nd part</a> of this series.</p>

<p>Here is the information that we get for the <code>ViewController</code> class from its class-dump-z output.</p>

<img src="/images/posts/ios8/12.png" width="510" height="228" alt="12">

<p>As we can see,the method validateLogin returns a BOOL value. Well, it can be assumed that this method checks whether the username and password are correct and returns a YES or NO accordingly. With Cycript, we can change the implementation for a particular message. So let's change the implementation to something that always returns TRUE.</p>

<img src="/images/posts/ios8/13.png" width="606" height="60" alt="13">

<p>So the R.H.S contains a javascript function that always returns true. Let's now tap on <code>Login Method 1</code> in the app.</p>

<img src="/images/posts/ios8/IMG_0103.PNG" width="320" height="480" alt="IMG 0103">

<p>As you can see, the authentication was successful and the app let us through. We have just performed method swizzling using Cycript to bypass a login form.</p>

<h2>Some other cool things</h2>

<p>Now that we have seen how method swizzling works, it would be interesting to know some other way to bypass this authentication. From the class-dump-z output, we can figure out that once validateLogin returns TRUE, the method <code>pushLoginPage</code> gets called. Some other names for such methods could have been <code>pushUserPage</code>, or <code>pushLoginSuccessfulPage</code> etc. Well, we don't need the validation to be true. We can always call this method ourselves.</p>


<img src="/images/posts/ios8/14.png" width="585" height="40" alt="14">
<p>Since this is an instance method, we got to the instance using <code>UIApp.keyWindow.rootViewController.visibleViewController</code> and then called the method .Please note that this may lead to crashes or inconsistent data as the properties of the pushed View Controller may depend on the login information entered.</p.

<p>If you are up for another challenge, try bypassing the login form after tapping on <code>Login Method 2</code>. We will discuss how to bypass this in later articles.</p>

<h2>Conclusion</h2>

<p>In this article we looked at some Method Swizzling techniques using Cycript. In later articles we will discuss more automation techniques and better tools that we can use to achieve this goal. We will also look at how we can use gdb for performing runtime analysis and otool for gathering other kinds of information.</p>