---
layout: post
title: "iOS Application Security Part 46 - App Transport Security"
date: 2016-06-23 01:03
comments: true
categories: [security]
---

One of the most common misconfiguration issues that i find during testing iOS apps is the bypass of the App Transport Security feature introduced by Apple in iOS 9. Here's an excerpt from Apple's documentation about ATS.

<b><i>"Starting in iOS 9.0 and OS X v10.11, a new security feature called App Transport Security (ATS) is available to apps and is enabled by default. It improves the privacy and data integrity of connections between an app and web services by enforcing additional security requirements for HTTP-based networking requests. Specifically, with ATS enabled, HTTP connections must use HTTPS (RFC 2818). Attempts to connect using insecure HTTP fail. Furthermore, HTTPS requests must use best practices for secure communications."</i></b>

<!-- more -->

It is important to note that just using HTTPs is not enough. The following screenshot taken from Apple's <a href="https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW33">documentation</a> discusses the necessary conditions necessary for ATS.

<img src="/images/posts/ios46/1.png" width="2186" height="1114" alt="1">

Since updating to iOS 9, developers start getting errors like if the app is not communicating over a secure connection.

<b><i>Connection failed: Error Domain=NSURLErrorDomain Code=-1022 "The resource could not be loaded because the App Transport Security policy requires the use of a secure connection." UserInfo={NSUnderlyingError=0x7fada0f31880 {Error Domain=kCFErrorDomainCFNetwork Code=-1022 "(null)"}, NSErrorFailingURLStringKey=MyServiceURL, NSErrorFailingURLKey=MyServiceURL, NSLocalizedDescription=The resource could not be loaded because the App Transport Security policy requires the use of a secure connection.}</i></b>

The reason for this is because developers are mostly testing their apps against a staging server with a misconfigured certificate. In some cases, they might be communication with production servers over HTTPs but might not be using old TLS versions. A simple google search will take you to a <a href="http://stackoverflow.com/questions/32631184/the-resource-could-not-be-loaded-because-the-app-transport-security-policy-requi">Stack overflow</a> that tells you how to bypass ATS by setting a single key NSAllowsArbitraryLoads to YES in the Info.plist file.

The following configuration in the Info.plist file shows the ATS feature bypass implemented.

<img src="/images/posts/ios46/x.png" width="499" height="190" alt="X">

However, in some cases the developer might have a secure communication with their backend server. But communication with third party servers for Analytics, Crash logs etc might not be over a secure connection. In this case, they can add certain domains as an exception.

<img src="/images/posts/ios46/y.png" width="558" height="108" alt="Y">

To find whether the app has ATS enabled or not, you can perform the following steps.

<ol>
<li>Decrypt the app using Clutch</li>
<li>Unzip the decrypted IPA file and look inside the Info.plist file.</li>
<li>Look for the key <i>App Transport Security</i>.</li>
</ol>


By the end of 2016, App transport security is going to be a <a href="https://techcrunch.com/2016/06/14/apple-will-require-https-connections-for-ios-apps-by-the-end-of-2016/">requirement</a> for App Store apps. It is important that you report any ATS bypass to the developers during security assessment of iOS Apps.






