---
layout: post
title: "IOS Application Security Part 7 - Installing and Running Custom Applications on Device without a registered developer account"
date: 2013-07-25 15:15
comments: true
categories: [security]
---

<p>Usually, to test apps on a device, you need to be a registered developer which costs about $99/year. For people who want to learn IOS Application security, it is very important that they should be able to run applications on device so that they can perfom tests on them. For some people who do not want to publish any apps on the app store, it may not be worth it to pay the $99/year fees. In this article we will be looking at how we can build and install an application on a jailbroken idevice without having a registered developer account. Then in the next article we will look at how we can run our own applications on the device and use Cycript to perform method swizzling and other techniques.</p>

<!-- more -->

<p>This article will focus on running custom apps on device using Xcode 4.5.2 on IOS 5.1 . This same technique might or might not work on other versions of IOS or Xcode. If you face some problems running the application on device, please drop a comment and i will respond to that asap.</p>

<p>The first step is to create a self signed certificate. We will use this certificate to sign thw apps that we want to run on the device.</p>

<p>Open the <code>Keychain Access</code> application. Tap on <code>Keychain Access --> Certificate Assistant --> Create a Certificate</code></p> 
 
<img src="/images/posts/ios7/1.png" width="822" height="612" alt="1">

<p>Name the certificate, in this case <code>Prateekg</code>, select the certificate type as <code>Code Signing</code> and do not check the option <code>Let me Override defaults</code>. Click on <code>Create</code> and tap on <code>Continue</code>.</p>

<img src="/images/posts/ios7/2.png" width="623" height="435" alt="2">

<p>As you can see, our certificate has been created.</p>

<img src="/images/posts/ios7/3.png" width="616" height="436" alt="3">

<p>Now copy the Info.plist file from the location /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Info.plist to your Desktop. We are doing this because we want to edit this file but we cannot do it on its original location. So in order to do this, we first have to copy this file to Desktop, edit it and then put it back.</p>

<img src="/images/posts/ios7/4.png" width="1268" height="49" alt="4">

<p>Open it up.</p>

<img src="/images/posts/ios7/xyx.png" width="714" height="98" alt="Xyx">

<p>Now in this file, replace all occurences of <code>XCiPhoneOSCodeSignContext</code> with <code>XCCodeSignContext</code> and save it. Here it how your file should look like.</p>

<img src="/images/posts/ios7/5.png" width="1040" height="718" alt="5">
<img src="/images/posts/ios7/6.png" width="1044" height="307" alt="6">
<img src="/images/posts/ios7/7.png" width="1042" height="169" alt="7">
<p>Now copy this file back from where you fetched it. You will need to have appropriate permissions for this. The command in my case is <code>sudo cp /Users/prateekgianchandani/Desktop/Info.plist /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Info.plist</code></p>
<img src="/images/posts/ios7/8.png" width="1360" height="39" alt="8">


<p>Now its time to create a sample Xcode project. Open up Xcode, click on <code>Create a new Xcode Project</code>, Select <code>Single View Application</code></p>

<img src="/images/posts/ios7/9.png" width="727" height="496" alt="9">

<p>Name the projet anything, in this case <code>SelfSignedApp</code>, click on <code>Next</code>, then click on <code>Create</code></p>
<img src="/images/posts/ios7/10.png" width="734" height="492" alt="10">


<p>Since we will be running this app on a device running IOS 5.1, we need to make sure the deployment target matches that. Tap on the project name in the <code>Project Navigator</code> on the top left, select the project and then inside <code>Info</code>, set the Deployment target to <code>5.1</code> as shown in the figure below. You can choose your deployment target depending on the operating system you are running your device on.</p>

<img src="/images/posts/ios7/11.png" width="883" height="382" alt="11">

<p>For cases where we are running the app on IOS 5.1, since we have a storyboard in this app, and because IOS 5.1 does not support the Autolayout feature in Storyboards, the app will crash. Hence make sure that the <code>Use Autolayout</code> option is not selected on the storyboard.</p>

<img src="/images/posts/ios7/12.png" width="256" height="112" alt="12">

<p>Now just drag and drop a label into the storyboard and write some text on it.</p>

<img src="/images/posts/ios7/13.png" width="1150" height="723" alt="13">

<p>Now we have to tell Xcode to sign this app using our self generated certificate. To do that, tap on the project name in the <code>Project Navigator</code> on the top left, select the project and then inside <code>Build Settings</code>, select the certificate that we created a while back. If the certificate doesn't show up in the dropdown for some reason, try restarting code.</p>

<img src="/images/posts/ios7/14.png" width="1143" height="343" alt="14">

<p>Now select the device that you want to run the app on. If for some reason, the device doesn't show up on the list, go to Organizer --> Devices --> Your device and click on <code>Use for development</code>.</p>

<img src="/images/posts/ios7/15.png" width="533" height="108" alt="15">

<p> After you have selected the device, click on Run and you will get a warning as shown in the image below. Click on <code>Always Allow</code>.You might get an error on the device or in Xcode but the app will be installed on the device. So just disconnect your device from your computer, quit the current instance of the app and run it again. It will now run the app without any troubles.</p>

<img src="/images/posts/ios7/16.png" width="1182" height="571" alt="16">

<p>There are some other ways to run the app on the device as well. Just build the app and that will create a .app file inside a particular directory in Xcode. The default location for that build is <code>/Users/$[YOUR_USER_NAME]/Library/Developer/Xcode/DerivedData/$[YOUR_APP_NAME_APP_ID]/Build/Products/Debug-iphoneos/</code>. In my case, the location is <code>/Users/prateekgianchandani/Library/Developer/Xcode/DerivedData/SelfSignedApp-bfzixtyoynrxxlgigskifizrfqqw/Build/Products/Debug-iphoneos/</code></p>

<p>Let's copy the .app file to Desktop using the following command.</p>

<p>mv /Users/$[YOUR_USER_NAME]/Library/Developer/Xcode/DerivedData/$[YOUR_APP_NAME_APP_ID]/Build/Products/Debug-iphoneos/SelfSignedApp.app /Users/$[YOUR_USER_NAME]/Desktop/</p>

<img src="/images/posts/ios7/17.png" width="1019" height="94" alt="17">
<img src="/images/posts/ios7/18.png" width="299" height="27" alt="18">

<p>Now create a folder named Payload, put the SelfSigned.app file under it, compress that folder (it will be initially named as Payload.zip) and name it SelfSigned.ipa .As we saw in part 2 of this series, this is the bundle in which IOS applications are stored.</p>

<p>Once we have the ipa file, there are 2 ways to install it. One is to just drag and drop this ipa file into the apps section in iTunes, then use iTunes to install the app on the device.</p>

<p>Another technique is to upload this ipa file to the device using sftp and then use a utility called <code>installipa</code> to install it on your device.<code>Installipa</code> can be downloaded directly onto your device using Cydia.</p>

<p>Upload the ipa file to your device using sftp.</p>

<img src="/images/posts/ios7/19.png" width="1021" height="160" alt="19">

<p>Then ssh into your device and install the app using the command line utility <code>installipa</code></p>

<img src="/images/posts/ios7/20.png" width="844" height="161" alt="20">

<p>This will install the application on your device. You might have to restart or respring the device for the app to function properly.</p>

<h2>Conclusion</h2>
 
<p>In this article, we looked at how we can install custom applications on the device without a valid developer certificate. In the next article, we will be using these techniques to install our own applications on the device and then perform various tests on them.</p>

