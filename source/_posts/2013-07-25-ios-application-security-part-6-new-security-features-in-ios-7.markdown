---
layout: post
title: "IOS Application Security Part 6 - New Security Features in IOS 7"
date: 2013-07-25 15:07
comments: true
categories: [security]
---

<p>As we all know, Apple recently introducted its new version of IOS at WWDC 2013 with a completely redesinged User Interface. If you haven't seen it yet, check out this video from WWDC 2013.</p>

<iframe width="560" height="315" src="http://www.youtube.com/embed/zcebqUdvDrw" frameborder="0" allowfullscreen></iframe>

<p>This article will be a small deviation from the other parts in this series. In this articles, we  will discuss about the latest security features introduced in IOS 7.</p>

<!-- more -->

<h2>Getting IOS 7.</h2>

<p>To get IOS 7 beta, you have to be a registered apple developer. Go to http://developer.apple.com  , log in with your registered Apple ID, and go to <a href="https://developer.apple.com/devcenter/ios/index.action#betadownloads">this</a> url. Then click on the IOS 7 SDK tab on the top and click on Downloads. You can then download the latest build of IOS 7 beta.</p>

<img src="/images/posts/ios6/Screen Shot 2013-06-25 at 5.35.32 AM.png" width="1158" height="674" alt="Screen Shot 2013 06 25 At 5.35.32 AM">
<p>Here are some of the new security features introduced with IOS 7.</p>

<h2>Activation Lock</h2>

<p>Previously, if some user had lost their iPhone, they could use the "Find my Iphone" app to track down their phone using GPS. But there was one very big flaw with this whole process. Even a not so aware thief could perfom a factory reset of the device and that would just erase all contents and settings on he device. However, now with the activation lock feature, the thief would need to know the password for the Apple Id of the user in order to perform a factory reset.</p>

<p>To Check this feature Go To <code>Settings</code>, then <code>General</code>, then scroll to the bottom and you will see a <code>Reset</code> button.</p>
<img src="/images/posts/ios6/photo 1.PNG" width="640" height="1136" alt="Photo 1">


<p>If you tap on this button and then tap on <code>Erase All Contents and Settings</code>, you will see a popup which asks for your Apple Id password.</p>

<img src="/images/posts/ios6/photo 2.PNG" width="640" height="1136" alt="Photo 2">

<p>This is a very handy feature and prevents the thief from resetting your iPhone's settings and data. But we can be sure that's its just a matter of time before someone finds a workaround for it after this is released to the public. You will get the same popup when you to go your iCloud Settings and try to turn off <code>Find My iPhone</code>.</p>

<img src="/images/posts/ios6/photo 3.PNG" width="640" height="1136" alt="Photo 3">


<p>As you can see, we get an alert asking for the Apple Id password.</p>

<img src="/images/posts/ios6/photo 4.PNG" width="640" height="1136" alt="Photo 4">


<h2>Encrypted data transfer with Airdrop</h2>

<p>Airdrop lets you share files with anyone around you with just a tap. Airdrop was introduced for IOS with IOS 7. The good thing about this is that the files are encrypted. By default, this service only lets you be visible to only your contacts. But you can change this setting as well.</p>

<img src="/images/posts/ios6/photo0.PNG" width="640" height="1136" alt="Photo0">

<h2>Third-party app data protection.</h2>

<p>Data protection is a feature for those devices that support hardware encryption, which means it supports devices from Iphone 3Gs and so on. According to <a href="http://support.apple.com/kb/ht4175">Apple</a>...</p>

<code>Data protection enhances the built-in hardware encryption by protecting the hardware encryption keys with your passcode. This provides an additional layer of protection for your email messages and attachments. Third-party applications can use the data protection APIs in iOS 4 and later to further protect application data.</code>

<p>Uptil IOS 6, if you go to <code>Settings -> General -> Passcode</code> and choose a passcode, you will see at the bottom that there is a text saying <code>Data Protection is Enabled</code>. This meant that data protection was enabled for the default Apple apps that come with IOS 6.</p>

<img src="/images/posts/ios6/photo.PNG" width="480" height="640" alt="Photo">

<p>Previously, the developers had to use the Data protection API to make the data in their apps secure. Now, with IOS 7, all the third-party apps have data protection enabled automatically, which means that the information contained within them is secure until the user unlocks the device. As we know, by default, Apple allows for a 4 digit passcode that can easily be bruteforced. Hence it is advisable to use better passcodes for enhanced security.</p>

<h2>Security Improvements in Game Center</h2>

<p>If you play games regularly on IOS and regularly use Game Center to challenge friends for a game or just check out some scores, you might have noticed some exceptionally high scores for some particular games. Usually, these are not legitimate scores but are scores that have been tampered with.Hackers exploit vulnerabilities in the api or sometimes modify variables in the app during runtime to create such high scores. With IOS 7, Apple has now allowed developers to make sure such tampering with the scores doesn't happen. Now, developers can set a maximum score for every game. This way, the people with unusually high scores will not show up in the leaderboard but will still be saved on the server. The developers can later allow these unusually high scores to show up if they want. Apple has also added some tools for developers to spy on any suspicious activity by a user, and in such a case, block that user. Apple is also integrating signed submissions into Game Center which means that Apple can reject scores that it believes have been tampered with. It isn't sure how they do it though.</p>

<h2>iCloud Keychain</h2>

<p>Most of us might have used the Safari AutoFill feature. With IOS 7, Safari now remembers much more information like username, password, credit card numbers etc and saves it in the keychain. It then automatically fills that info whenever a user visits a website. All this information is encrypted using 256-bit AES encryption. This keychain can also be synced via iCloud. Also, creating a new password for every new site that you sign in for may be a bit irritating and also tough to remember. Safari comes with a built in password generator that creates a unique password for every site that you sign in and then remembers it so you don't have to remember it yourself.</p>

<h2>Per App VPN</h2>

<p>Sometimes, users want to access confidential information over a secure channel, and hence they use a VPN. With IOS 7, Apple allows users to use different VPN's for every app. This is really useful when you want to access your office related information through a VPN provided by your office but for other apps, you are better off using another VPN. This feature will also be useful for government employees who want to acccess confidential data through their mobile apps.</p> 


<p>If you are an IOS developer and want to learn about the new things introduced with IOS 7, you can check out the WWDC 2013 videos here https://developer.apple.com/wwdc/videos/ </p>

<b>References:</b>

<ul>
     <li><p>Apple sticks it to cheaters with Game Center on iOS 7</br><a href ="http://www.idownloadblog.com/2013/06/15/ios7-game-center-dev-tools/">http://www.idownloadblog.com/2013/06/15/ios7-game-center-dev-tools/</a></p>
     </li>
	
</ul>
 