---
layout: post
title: "IOS Application security Part 2 - Getting class information of IOS apps"
date: 2013-06-16 02:56
comments: true
categories: [ios, security]
---

<p>Have you ever checked out an IOS app and thought it was cool, and wondered if you could find some information about the source code of the app, the third-party libraries it uses, or how the code is designed internally ? Have you ever wondered if it was possible to dump all the images, plist files used in any app either preinstalled on your device or downloaded from the App store? If the answer is Yes, then you have come to the right place.</p>

<p>In this article, we will look at how we can analyze any preinstalled app on your device or any other app downloaded from App store and discover things about the source code of the app like the classes that it uses, the names of the view controllers it uses, the internal libraries, and even intricate details like the variables and methods names used in any particular class or view controller. We will then look at how we can decrypt the applications downloaded from the App store and dump all the images, plist files that the app uses.</p>

<!-- more -->

<h2>Dumping class information for Preinstalled apps on the device</h2>

<p>Now we are at a stage that we can analyze apps for class information. So let's dump the class information for the Apple <em>Maps</em> app. The first step would be to locate the Apple <em>Maps</em> app executable. All IOS apps that come preinstalled with the device are stored in the directory <em>/Applications</em>. So let's navigate to that directory.</p>


<img src="/images/posts/ios2/20.png" width="1026" height="328" alt="20">


<p>Here you will see all the apps that come preinstalled with the device. Now let's navigate inside the <em>Maps</em> app directory and list the directories.<p>
	
<img src="/images/posts/ios2/21.png" width="1025" height="540" alt="21">

<p>As you can see, we can see all the images, plist files etc used by this app. We will discuss later how it is possible to fetch all the images and other files from a particular IOS app. Anyways, hidden in all this mess is an executable for the app with the name <em>Maps</em> as can be seen on the left side in the image below. Note that the name of the executable will be the same as the name of the app. Note that we can see some pdf's in the app bundle as well. I really don't see the need of including a pdf file in the bundle.</p>

<img src="/images/posts/ios2/22.png" width="1025" height="397" alt="22">

<p>To dump the class information for this app, just use the command <em>class-dump-z Maps</em></p>

<img src="/images/posts/ios2/23.png" width="903" height="642" alt="23">

<p>As you can see there is just too much output in the terminal right now, hence its better to save the output to a file, in this case with the filename <em>class-dump-Maps</em>.</p>

<img src="/images/posts/ios2/24.png" width="662" height="18" alt="24">

<p>You can now use <em>sftp</em> to ftp into the device and download the file. You can fetch any file with the command <em>get</em> followed by the path of the file as shown below.</p>


<img src="/images/posts/ios2/25.png" width="1025" height="216" alt="25">

<p>Since the file is now downloaded locally on the system, let's open it up in <em>TextMate</em> (you can use textedit or any other app as well)</p>

<img src="/images/posts/ios2/26.png" width="1229" height="692" alt="26">

<p>We can learn a lot about the way the code is designed just by looking at the interface files. For e.g over here you can see a View controller named <em>InfoCardController</em>. As you might have already guessed, this is the VC to display more info about a particular location when we tap on the right arrow button as shown in the image below.</p>

<img src="/images/posts/ios2/27.PNG" width="640" height="1136" alt="27">

<p>Now lets have a look at this view in the app. This page is actually displayed by <em>InfoCardViewController</em> which we found from <em>class-dump-z</em> information.</p>

<img src="/images/posts/ios2/28.PNG" width="640" height="1136" alt="28">


<p>If you look at this image and the class information above, you can easily see what are the methods names that get called when you tap on these buttons. For e.g if i tap on <em>Direction to here</em>, the method that will get called is </p>

<p><em>-(void)_directionsTo:(id)to person:(void*)person property:(int)property identifier:(int)identifier;</em></p>

<p>Similarly, if i tap on <em>Add to Bookmarks</em>, the method that will get called is</p>

<p><em>-(void)_addToBookmarks:(id)bookmarks person:(void*)person property:(int)property identifier:(int)identifier;</em></p>

<p>You can find a lot of other information from the app as well, for e.g here is a class named <em>UserLocationSearchResults</em> which inherits from SearchResult.</p>

<img src="/images/posts/ios2/28x.png" width="664" height="298" alt="28x">

<p>You can download the class information for the Apple <em>Maps</em> app from here.</p>	

<p>How much you can explore here is only up to your curiosity :).</p>
	
	
<h2>Dumping class information for apps downloaded from the App store</h2>

<p>Their are two important things to know if you want perform analysis of the apps that you download from the App store.</p>

<ul>
	<li>The apps are stored in a different location, <em>/var/mobile/Applications/</em></li>
	<li>Unlike the apps that come preinstalled with the device, the apps are <em>encrypted</em>, hence you will have to <em>decrypt</em> them first.</li>
</ul>

<p>To decrypt the apps, we will be using a command line tool called <em>Clutch</em>. Please note that <em>Clutch</em> was being offered by <em>Hackulous</em> which has been shut down a few months back. But the binary for <em>Clutch</em> is still available on the internet.</p>


<p>Now you need to upload the binary onto your device. To do that, we are going to use <em>sftp</em>. To upload a file onto the device, just use the <em>put</em> command.</p>

<img src="/images/posts/ios2/29.png" width="1027" height="174" alt="29">

<p>Now, ssh into your device and type <em>clutch</em>. This will give you a list of all the apps that could be <em>cracked</em>.</p>

<img src="/images/posts/ios2/30.png" width="1025" height="185" alt="30">

<p> To crack a particular app, just type <em>clutch app-name</em> For e.g if we want to crack the Facebook app, we will type <em>clutch Facebook</em></p>

<img src="/images/posts/ios2/31.png" width="537" height="107" alt="31">

<p>Once it is done cracking, it will tell you the location where it has saved the <em>ipa</em> file. Now an <em>ipa</em> file is just a compressed version of the whole app bundle. To unzip it, just use the <em>unzip</em> command and save it to a directory by using the <em>-d</em> command  as shown in the figure below. Note that you can also copy this <em>ipa</em> file on your system using <em>sftp</em> and then unzip it over there. You will then have access to all the images of the app as well as any other files that may be present in the unzipped folder.</p>

<img src="/images/posts/ios2/32.png" width="762" height="404" alt="32">

<p>Now that we have the decrypted file, we can use <em>class-dump-z</em> to dump the class information for it and save it in a file which in this case is named <em>class-info-Facebook</em>.</p>

<img src="/images/posts/ios2/33.png" width="748" height="26" alt="33">

<p>Once this is done, you can exit the ssh session, log in via <em>sftp</em> and then download the <em>class-info-Facebook</em> file.</p>

<img src="/images/posts/ios2/33x.png" width="1028" height="173" alt="33x">
	

<p>You can now check out this file using any text viewer. For e.g here is a protocol named <em>FBFacebookRequestSender</em> which has methods for sending asynchronous requests as well as a method to check if the Facebook Session is valid or not.</p>


<img src="/images/posts/ios2/34.png" width="1076" height="126" alt="34">

<h2>Fetching images and other files from a particular app.</h2>
	
<p>As discussed previously in the article, one of the methods would be to use <em>sftp</em> to fetch all the files that you want from that app's directory. However, there are much easier ways to do this, one of which is to use <a href ="http://www.macroplant.com/iexplorer/download-ie3-mac.php">iExplorer</a>. Download it from the official website. Once this is done, just open it up and make sure your device is connected to the system via USB.</p>

<img src="/images/posts/ios2/35.png" width="1439" height="803" alt="35">

<p>To view the filesystem, just click on <em>files</em>.</p>

<img src="/images/posts/ios2/36.png" width="720" height="300" alt="36">
	
<p>To check out files for a particular app, click on <em>Apps</em></p>

<img src="/images/posts/ios2/37.png" width="1440" height="778" alt="37">

<p> As you can see, it is very easy to browse the filesystem and upload/download files. In this case, lets download all the image and files present in the <em>Facebook</em> app. On the left side, look for <em>Facebook</em> and click on it. This will take you to the directory containing Facebook app files. All the images and files are containing inside the <em>Facebook.app</em> directory.</p>

<img src="/images/posts/ios2/38.png" width="1437" height="778" alt="38">

<p>To download all the files, just press <em>Cmd + A</em>, and right click and select <em>Export to Folder</em>. Then choose the location where you want to save all the files.</p>

<h2>Conclusion</h2>
	
<p>In the first two parts of this article, we have learnt how to setup a mobile auditing environment on a jailbroken device. We then learnt how to dump the class information for any particular app and use it to understand the design of the code and its internal workings. We also learnt how to decrypt an app downloaded from the App store and audit it for information. We then learnt how to un-munge images from apps using both sftp and iExplorer.</p>

<p>Well, the good thing is that it is possible to know all the methods that get called by using the class information that we get from class-dump-z. <em>But is it possible to perform some runtime modification in the app ?</em> For e.g if a method like -(BOOL)isFacebookSessionValid returns false in a particular case, is it possible for us to manipulate the app in such a way that it returns YES and hence let the application do unexpected things ? <em>Further, is it possible to create our own custom method and execute it instead of this method whenever this method gets called ?</em> Is it possible to modify the values of instance variables during runtime, or after any specific instruction ?The answer is <a href= "http://cycript.org">YES</a>, and we will learn about it in the next article :).</p>

<p>This article was originally published on the <a href="http://resources.infosecinstitute.com/">resources</a> page at <a href="http://infosecinstitute.com/">Infosec Institute</a>. For more information, please visit my author <a href="http://resources.infosecinstitute.com/author/prateek/">page</a>.</p>
