---
layout: post
title: "iOS Application Security Part 36 – Bypassing certificate pinning using SSL Kill switch"
date: 2014-11-03 01:00
comments: true
categories: [security]
---

<p>In this article, we will look at how we can analyze network traffic for applications that use certificate pinning. One of the best definitions i found of certificate pinning is mentioned below. It is taken directly from <a href="https://www.infinum.co/the-capsized-eight/articles/securing-mobile-banking-on-android-with-ssl-certificate-pinning">this</a> url.</p>

<p>
By default, when making an SSL connection, the client checks that the server’s certificate:</p>

<ul>
<li>has a verifiable chain of trust back to a trusted (root) certificate</li>
<li>matches the requested hostname</li>
<li>What it doesn't do is check if the certificate in question is a specific certificate, namely the one you know your server is using.</li>
</ul>

<!-- more -->

Relying on matching certificates between the device's trust store and the remote server opens up a security hole. The device’s trust store can easily be compromised - the user can install unsafe certificates, thus allowing potential man-in-the-middle attacks.

Certificate pinning is the solution to this problem. It means hard-coding the certificate known to be used by the server in the mobile application. The app can then ignore the device’s trust store and rely on its own, and allow only SSL connections to hosts signed with certificates stored inside the application.

This also gives a possibility of trusting a host with a self-signed certificate without the need to install additional certificates on the device.
</p>

<p>Certificate pinning is used by many popular applications for e.g Twitter, Square etc. So the question that arises is, how do you bypass this certificate validation that is happening on the client side ? The important thing to note here is all that all the validation is happening on the client side. And since there are frameworks like Mobile Substrate that allow us to patch any method during runtime and modify its implementation, it is possible to disable the certificate validation that is happening in the application.</p>

<p>A POC tool for this by released in Blackhat and it was named iOS SSL Kill Switch. The full presentation can be found <a href="https://media.blackhat.com/bh-us-12/Turbo/Diquet/BH_US_12_Diqut_Osborne_Mobile_Certificate_Pinning_Slides.pdf">here</a>. After some time, the author realized that he was able to inspect traffic from apps that used certificate pinning (for e.g Twitter), but he wasn't able to see the traffic going through the App Store app. He then realized he needed to patch even more low level methods and kill specific processes in order to inspect traffic going via the App store app. The full writeup for this could be found <a href="https://nabla-c0d3.github.io/blog/2013/08/20/intercepting-the-app-stores-traffic-on-iOS/">here</a> and it's quite interesting, so i suggest you give it a read. Also note that this tool will also be able to disable the default SSL certificate validation, so you don't need to install a certificate as trusted root as well, which is what we usually do for inspeting traffic over HTTPs.</p>

<p>To really check that the Twitter app uses certificate pinning, install the Twitter app and route the device traffic through Burp Proxy. Make sure you are inspect traffic via HTTP/HTTPS using the steps mentioned in <a href="http://highaltitudehacks.com/2013/08/20/iOS-application-security-part-11-analyzing-network-traffic-over-http-slash-https">Part 11</a> of this series. However, when you open the twitter app and navigate around, the traffic is not captured by Burpsuite.</>

<p>To inspect the traffic going via Twitter, ssh into your device and download the iOS SSL Kill Switch package from it's <a href="https://github.com/iSECPartners/iOS-ssl-kill-switch/releases">releases</a> link. Also, make sure to install the following packages via Cydia.</p>

<ul>
<li>dpkg</li>
<li>MobileSubstrate</li>
<li>PreferenceLoader</li>
</ul>

<p>Now install the deb package using the command <i>dpkg -i <packagename></i>.</p>
	

<img src="/images/posts/ios36/1.png" width="967" height="134" alt="1">
<p>Now, respring the device using the command <i>killall -HUP SpringBoard</i>.</p>

<p>Once this is done, go to Settings app. There will be a new menu for SSK Kill Switch and a slider to Disable certificate validation. Make sure the slider is set to on.</p>

<img src="/images/posts/ios36/2.png" width="832" height="254" alt="2">

	
<p>Now route the traffic in the device to pass through Burp Proxy. Open twitter app and now you can see all the data going through via the twitter app as well.</p>
<img src="/images/posts/ios36/3.png" width="1045" height="451" alt="3">

<p>To verify that SSL Kill Switch is being injected into the application, go to Xcode -> Devices (I am using Xcode 6), look for your device in the left menu and click on the arrow pointing up in the lower left corner to see the device logs. You will see that SSL Kill Switch is being injected into the application.</p>

<img src="/images/posts/ios36/4.png" width="598" height="123" alt="4">

<p>Another cool utility that does the same job is <a href="https://github.com/intrepidusgroup/trustme">trustme</a>. I recommend you check it out.</p>
