---
layout: post
title: "Defcon 20 Day 2 Review"
date: 2013-06-15 02:51
comments: true
categories: [security, defcon]
---

<p>The talks on Defcon day 2 were scheduled to begin from 10 am. I reached the venue at 8:30 am and decided to use the time to buy some Defcon merchandise. The lines for the merchandise are usually very long but it wasn't at that time of the day.</p>

<!-- more -->

<img src="/images/posts/defcon2/1.JPG" width="5184" height="3456" alt="1">

<p>After buying a Defcon T-shirt, i decided to go to the room where the CTF contest was being held. The room was already filled with participants who were just setting up their systems for the contest. </p>

<img src="/images/posts/defcon2/2.JPG" width="5184" height="3456" alt="2">


<p>Here are some pics of the contests hall where some of the events like Bro CTF, gaming events etc were being held.</p>

<img src="/images/posts/defcon2/IMG_1092.JPG" width="5184" height="3456" alt="IMG 1092">

<img src="/images/posts/defcon2/IMG_1094.JPG" width="5184" height="3456" alt="IMG 1094">

<p>And there was also the very famous Wall of Sheep.</p>

<img src="/images/posts/defcon2/Wall_OF_sheep.JPG" width="5184" height="3456" alt="Wall OF Sheep">

<p class = "talk">Making Sense of Static - New tools for hacking GPS </p>

<p>This talk was mainly focussed on explaining how the GPS works and introduced some of the new tools for GPS hacking. The talk was mainly divided into three parts a) Why is GPS interesting for hacking. b) GPS 101 and c) New tools for hacking GPS. The speakers started by explaining that these days there are a lot of GPS devices being used and hence a lot of techniques have been developed for performing spoofing and jamming attacks. They then gave a very detailed description about GPS, how the geolocation information is being determined by locating the distance from the satellites and all the other technical aspects related to it.In the end, the speakers introduced two new GPS hacking tools named Libswiftnav and Piksi.</p>

<img src="/images/posts/defcon2/pIksi.png" width="746" height="508" alt="PIksi">

<p class = "talk">Drones</p>

<p>The speaker Chris Anderson starts by telling how the vast amount of technology required to create Drones is already available to the public for a very cheap amount. He then tells about how he once started a community DIY drones dedicated to created drones. Over the past 5 years, it has transformed into the largest amateur UAV community on the web. He then explains that the success of this was because of the support from the community and the use of Open source hardware and software. DIY Drones has been able to create thousands of Unmanned aerial vehicles at a cost of as less as 1% than the cost of the equivalent military drones. He then explains about the challenges that he faced while creating the community. He tells about his plan to make these drones available to the public at a good price and the legal challenges that he is going to face while doing this. He also explains how by doing this the community can actually help them improve their product. He explains about his plan to compete with some of the aerospace industries. </p>

<img src="/images/posts/defcon2/IMG_1115.JPG" width="5184" height="3456" alt="IMG 1115">

<p class = "talk">Can You Track Me Now? Government And Corporate Surveillance Of Mobile Geo-Location Data</p>

<p>This was a panel discussion by Christopher Soghoian, Ben Wizner, Catherine Crump and Ashkan Soltani. The speakers tell about the investigation they have done on how the government is utilizing geolocation data of regular users via their mobile devices. The speakers first start by explaining about some of the local and network attacks which could lead to disclosure of geolocation information. These could be local caches stored on their phone or through third party apps which disclose the user's geolocation information. In some cases, the photos in the device and are available to the third party apps and these photos could sometimes contain geolocation information. They explain about how the carriers companies are forced by the government to reveal the location data of their customers. This geolocation information could be of different types for e.g real time GPS data, historical data or triangulated data. They explain how the carrier companies have built web portals which allow government to track the users while sitting at their office. They also explain that encrypting information on a mobile device doesn't server the purpose as Apple and Google (which are the two major manufacturers of mobile OS) can undo the encryption if needed by the government. A lot of innocent citizens are subjected to surveillance and they don't even know about it. They also explain how the laws for protecting user's privacy are outdated and are not up to pace with the technology. The speakers finally conclude by saying that the least these carries companies can do is inform the users if their private information is being looked into. </p>

<img src="/images/posts/defcon2/IMG_1086.JPG" width="5184" height="3456" alt="IMG 1086">


<p class = "talk">New techniques in SQLi obfuscation</p>

<p>The speaker Nick Galbreath discusses about some of new techniques of hiding SQL injection from WAF's. He first starts by introducing  Libinjection which is a quasi-SQL tokenizer and parser to detect SQL injection. He then explains how he tested it with a very large number of Sql injection attacks and noted that some of the code in WAF's was never even executed. He explains how he walked through some of the dark corners of different languages like MySql, PGSql, Oracle looing for different kinds of syntax. He starts by explaining about the NULL character. NULL in MySQL an be written as \N, whereas \n means a new line. This means any WAF that does a to_lower on the user input and looks for null will miss this test case. He then explains about some of the unique Postgres functions that might slip through WAF's too. He explains that because of so much of syntax available and because of the ability to write things in different formats, it becomes tough to write a regular expression to detect SQL injection. He continues the discussion by talking about some oracle special literals, hexadecimal literals . binary literals, money Literals. comments, strings etc and how their syntax can be used to get through WAF. He finally concludes the talk by talking about his intention to add more parsing support for libinjection.</p>
<img src="/images/posts/defcon2/IMG_1122.JPG" width="5184" height="3456" alt="IMG 1122">



<p class = "talk">Divashark</p>

<p>The speaker talks about a new tool named Divashark he has been working on. He starts by telling that the inspiration behind this work came from the Defcon Capture the Packet contest where contestants use Wireshark filters to get the information they need. Divashark is an application which assists in live network traffic analysis. He explains how Wireshark can sometimes contain too much information, and if someone is looking for high level information like username and password, then it becomes a very tedious task. He tells that Divashark has the capability to follow tcp and udp streams and group them together. It also has some Protocol dissectors which are designed to get you the information that you want. He finally concludes by telling that the user interface isn't ready yet but should be available soon.</p>

<img src="/images/posts/defcon2/IMG_1125.JPG" width="5184" height="3456" alt="IMG 1125">

<p>Here are some other pictures from Defcon day 2 </p>

<img src="/images/posts/defcon2/IMG_1104.JPG" width="5184" height="3456" alt="IMG 1104">

<img src="/images/posts/defcon2/IMG_1110.JPG" width="5184" height="3456" alt="IMG 1110">

<img src="/images/posts/defcon2/IMG_1097.JPG" width="5184" height="3456" alt="IMG 1097">	

<p>This article was originally published on the <a href="http://resources.infosecinstitute.com/">resources</a> page at <a href="http://infosecinstitute.com/">Infosec Institute</a>. For more information, please visit my author <a href="http://resources.infosecinstitute.com/author/prateek/">page</a>.</p>
