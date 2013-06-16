---
layout: post
title: "Defcon 20 Day 3 Review"
date: 2013-06-15 02:53
comments: true
categories: [defcon, security]
---

<p>Defcon day 3 started with one of the most awaited talks of Defcon 20. It was the talk "Defeating PPTP VPNs and WPA2 Enterprise with MS-CHAPv2" by Moxie Marlinspike, David Hulton and Marsh Ray. Moxie marlinspike has been one of the most popular speakers at Defcon for the past few years and as expected, the hall was full of people.</p>

<!-- more -->

<img src="/images/posts/defcon3/IMG_1216.JPG" width="5184" height="3456" alt="IMG 1216">


<h2>Defeating PPTP VPNs and WPA2 Enterprise with MS-CHAPv2</h2>

<p>This talk was mainly about pointing out the vulnerabilities in the MS-CHAP v2 protocol. The speaker talks about how the previous research has shown that the security of the protocol is based on the password of the user. PPTP and WPA 2 Enterprise (which is used by the Defcon wi-fi network) both depend on MS-CHAP v2 protocol. He then explains how many of the VPN providers support PPTP, giving a specific example about Ipredator which only supports PPTP. He then gives a quick explaination about the MS-CHAP v2 handshake. He then explains how all the parameters in the handshake except the md4 hash of the password can be found as it is either know or sent in plaintext. Using some advanced mathematical logic, he then explains how the complexity of the password is that of a single DES encrypt, which is 2 to the power of 56. So the only thing to do now is to bruteforce the challenged response. Moxie then passes on the conversation to David hulton who explains how by using a GPA in a machine with a number of cores, it is possible to decrypt the challenged response in less than 24 hours. He then passes on the conversation to Moxie who explains about a new tool called "Chapcrack" that they will be releasing which is capable of parsing packets, get all the information about MS-CHAP v2 handshake, username etc. He then explains how the ability to crack hashes faster is not available to the general public. Finally, he announces the additional feature to crack MS-CHAP v2 responses in his website Cloud Cracker. The user can enter his email address on the website and get the result within 1 day. </p>

<img src="/images/posts/defcon3/IMG_1217.JPG" width="5184" height="3456" alt="IMG 1217">
	
<h2>Owning bad guys with Javascript Botnets</h2>

<p>This was also a very good and informative talk by Chema Alonso. The speaker starts by telling how people think that creating a botnet is considered a very tedious task. He then talks about a new idea for creating botnet that he has been working on. He talks about the idea of a JS in the middle attack. This would allow the attacker to modify form fields, get access to cookie etc. But how will it be possible to inject JS code in the middle. He then talks about using a proxy server which will allow the users to direct their traffic through it. He then tells about how the information about the proxy server is posted by other people on the internet. Once the IP address of the proxy server is spread over the internet, people will connect to it without thinking much. Once the people connect through, all the javascript files are infected with the malicious Javascript code. The speaker then tells that how in just one day he was able to get control of 5000 bots. The speaker then finally concludes by giving examples of the information that he found in some of the bots and gives a quick look into the control panel of this botnet.</p>

<img src="/images/posts/defcon3/IMG_1221.JPG" width="5184" height="3456" alt="IMG 1221">

<p>A pic from the panel discussion "Meet the Fed Panel Two"</p>

<img src="/images/posts/defcon3/IMG_1225.JPG" width="5184" height="3456" alt="IMG 1225">

<p>A pic from "Q & A with the Men (and Women) in Black"</p>

<img src="/images/posts/defcon3/IMG_1226.JPG" width="5184" height="3456" alt="IMG 1226">

<p>A pic from "Bigger Monster,Weaker Chains: The NSA and the Constitution"</p>

<img src="/images/posts/defcon3/IMG_1227.JPG" width="5184" height="3456" alt="IMG 1227">

<p>If you are at Defcon, you may not want to miss on the Defcon nightlife. There are a lot of parties held everyday, some of them are private and need invitations, while some of them are open to all the Defcon attendees. I went to the Infected mushroom party on Saturday night. Here is a pic from the party.</p>

<img src="/images/posts/defcon3/IMG_1245.JPG" width="5184" height="3456" alt="IMG 1245">

<p>And here is a video from the Infected Mushroom party.</p>

### EMBED CODE FOR VIDEO GOES HERE ###

<p>This article was originally published on the <a href="http://resources.infosecinstitute.com/">resources</a> page at <a href="http://infosecinstitute.com/">Infosec Institute</a>. For more information, please visit my author <a href="http://resources.infosecinstitute.com/author/prateek/">page</a>.</p>

