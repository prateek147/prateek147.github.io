---
layout: post
title: "iOS Application Security Part 45 - Enhancements in Damn Vulnerable iOS app version 2.0"
date: 2015-05-31 00:18
comments: true
categories: [security]
---

<p>In this article, i would like to give a quick walkthrough of the new vulnerabilities and challenges that we have added in version 2.0 of <a href="http://damnvulnerableiosapp.com">Damn Vulnerable iOS app</a>.</p>

<p>In the Insecure Data storage section, we have added challenges for the following databases.</p>

<ul>
	<li>Realm Database</li>
	<li>Couchbase Lite</li>
	<li>YapDatabase</li>
</ul>


<!-- more -->
<img src="/images/posts/ios45/1.png" width="235" height="321" alt="1">

<img src="/images/posts/ios45/2.png" width="235" height="310" alt="2">
<img src="/images/posts/ios45/3.png" width="235" height="314" alt="3">

<p>We have also added a new section on <i>Extension vulnerabilities</i>, which covers vulnerabilities in different application extensions, a feature that was introduced with iOS 8.</p>

<img src="/images/posts/ios45/4.png" width="235" height="328" alt="4">
<img src="/images/posts/ios45/5.png" width="235" height="328" alt="5">


<p>In the <i>Runtime Manipulation section</i>, we have added a challenge where you can write a cycript script to brute force a login screen.</p>
	
<img src="/images/posts/ios45/6.png" width="235" height="281" alt="6">

<p>Another new section is <i>Attacks on third party libraries</i>, which demonstrates the security gaps that can occur in your application when you use third party libraries in your project.</p>

<img src="/images/posts/ios45/7.png" width="235" height="315" alt="7">
<img src="/images/posts/ios45/8.png" width="235" height="320" alt="8">
<img src="/images/posts/ios45/9.png" width="235" height="265" alt="9">
<img src="/images/posts/ios45/10.png" width="235" height="256" alt="10">
<img src="/images/posts/ios45/11.png" width="235" height="281" alt="11">
<img src="/images/posts/ios45/12.png" width="235" height="266" alt="12">

<p>In the section on <i>Side Channel Data leakage</i>, we have added another vulnerability demonstrating insecure storage of cookies.</p>

<img src="/images/posts/ios45/13.png" width="233" height="365" alt="13">
	
<p>The current downloadable IPA file from the website is a fat binary that will work on both 32 bit and 64 bit devices. This app will work on all iOS versions starting from iOS 7.0.</p>
 


<p>Some important links</p>

<ol>
	<li><a href="http://damnvulnerableiosapp.com">Official Website</a></li>
	<li><a href="http://github.com/prateek147/DVIA">Github Page</a></li>
	<li><a href="http://damnvulnerableiosapp.com#downloads">Downloads Page</a></li>
</ol>

<p>We are working on getting the new solutions out as soon as possible so please be patient. For previous vulnerabilities, you can download the solutions for free from <a href="http://damnvulnerableiosapp.com#solutions">here</a>.</p>

<p>For any bugs, suggestions etc, please don't hesitate to contact me. Also, a very special thanks to <a href="http://twitter.com/igrekde">Egor</a> for his contributions to the project.</p>
