---
layout: post
title: "iOS Application Security Part 38 - Attacking apps using Parse (Guest Lecture by Egor Tolstoy)"
date: 2015-01-24 00:16
comments: true
categories: [security]
---

<b>This is a guest lecture by <a href="http://etolstoy.ru">Egor Tolstoy</a>. Egor is a full-time iOS developer working at Rambler&Co and living with his lovely wife in Moscow, Russia. In his spare time he investigate iOS applications for different vulnerabilities and blogs about my research.</b>

<div><a href="http://parse.com/">Parse</a> is a wonderful BaaS which helps with setting up backend infrastructure for your mobile application as fast as possible. Maybe just because of this simplicity many developers forget about a number of new security issues and vulnerabilities.</div>
<div><br/></div>


<!-- more -->

<div><img src="/images/posts/ios38/8822BC1A-FA0D-4BFF-8DC0-8CC103DFB92D.png" height="495" width="816"/><br/></div>
<div><br/></div>
<div>For those who don’t know what this service is, let’s make a brief introduction. Parse provides a lot of useful capabilities to mobile developer: cloud data storage, push notifications, usage statistics and crash logs gathering, code hosting, background jobs and a many other things. Within the boundaries of thes research we are interested in the cloud data storage, named <i>Cloud Core</i>.</div>
<div>All the data in Cloud Core is stored in so called custom classes (ordinary database tables).</div>
<div><br/></div>

<!-- more -->
<div><img src="/images/posts/ios38/8879CA5B-B494-43CD-A136-1DB8059F5AC1.png" height="420" width="1043"/><br/></div>
<div><br/></div>
<div>You can set a number of different client permissions for each of these classes: <i>GET, FIND, UPDATE, CREATE, DELETE</i> and <i>ADD FIELDS</i>. All of them are <i>Public</i> by default. Of course, most of the developers forget about the need of setting client access permissions once they configure their tables.</div>
<div><br/></div>
<div><img src="/images/posts/ios38/FDF17B0E-CD2B-47AB-BB46-F0D0BE9BDE7E.png" height="535" width="403"/><br/></div>
<div><br/></div>
<div>I’ve closely faced Parse during one of my work projects and spent a lot of time configuring ACLs properly - so I became interested in how other developers maintain their Parse accounts. I’ve found the object for my little research right on <a href="https://parse.com/customers">parse.com/customers</a>. It was <a href="http://cubefreeapp.com/">Cubefree</a> - a service for locating cowering spaces.</div>
<div><br/></div>
<div>A pair of keys is used for connecting to Parse account from a mobile application: <i>Application ID</i> and <i>Client Key</i>. We’ve got to find out these strings in order to manipulate the data in Cloud Core. Let’s decrypt the application binary with the help of <a href="https://github.com/dmayer/idb">idb</a> - an awesome iOS pentesting utility. While the decryption process is going on, we can check <i>NSUserDefaults</i> - a rather common place for storing such kind of data (only for reckless developers, of course).</div>
<div><br/></div>
<div><img src="/images/posts/ios38/03B3C111-1C51-4330-899E-7E5873D94B7F.png" height="486" width="879"/><br/></div>
<div><br/></div>
<div>As you can see, nothing criminal was found - no signs of confidential data. Let’s get back to our decrypted application binary and feed it to <a href="http://www.hopperapp.com/">Hopper</a> - a well known disassembler, specialized in reverse-engineering Objective-C applications. Our quest for Parse keys will begin in <span style="color: rgb(34, 34, 34); font-family: 'Helvetica Neue', Helvetica, Helvetica, Arial, sans-serif; background-color: rgb(255, 255, 255);"><i>application:didFinishLaunchingWithOptions method</i> of <i>App Delegate.</i> One of the noteworthy Hopper features is the ability to represent any procedure in pseudocode form, which flattens the reversed code understanding curve.</span></div>
<div><span style="color: rgb(34, 34, 34);"><br/></span></div>
<div><img src="/images/posts/ios38/AB772C3F-377A-4B02-BA39-946D0B54831C.png" height="602" width="667"/><span style="color: rgb(34, 34, 34);"><br/></span></div>
<div><span style="color: rgb(34, 34, 34);"><br/></span></div>
<div><font color="#222222">As expected, the connection to Parse is initiated right here. Now we’ll analyse the structure of Parse data and its client permissions.</font></div>
<div><span style="color: rgb(34, 34, 34);"><br/></span></div>
<div><font color="#222222">The next step is identification of Parse tables names. Actually, we can see them on the same screenshot as client keys - there is a plenty of <i>registerSubclass</i> method calls. These classes are children of the root <i>PFObject</i> class. Each of them has a method </font><span style="color: rgb(34, 34, 34); font-family: 'Helvetica Neue', Helvetica, Helvetica, Arial, sans-serif; background-color: rgb(255, 255, 255);"><i>parseClassName, </i>which returns a corresponding Parse table name.</span></div>
<div><span style="color: rgb(34, 34, 34);"><br/></span></div>
<div><img src="/images/posts/ios38/FC415E09-109A-460B-9E04-763532556FDB.png" height="87" width="582"/><span style="color: rgb(34, 34, 34);"><br/></span></div>
<div><span style="color: rgb(34, 34, 34);"><br/></span></div>
<div><font color="#222222">Let’s inspect the structure of these tables:</font></div>
<div><a href="https://gist.github.com/igrekde/cb8e2c12408715c9f739#file-parse-security-1">https://gist.github.com/igrekde/cb8e2c12408715c9f739#file-parse-security-1</a><span style="color: rgb(34, 34, 34);"><br/></span></div>
<div><span style="color: rgb(34, 34, 34);"><br/></span></div>
<div>The knowledge of classes organization, however, is not enough. We should try to inspect access permissions for all the Parse classes to determine how we can influence the application behaviour. It’s quite simple - all we have to do is to make a couple of queries to Parse and analyse their results. I’ve wrote a small utility - <a href="https://github.com/igrekde/ParseRevealer">Parse Revealer</a>, which simplifies these routine actions and automatically determines the access permissions for all known classes.</div>
<div><br/></div>
<div><img src="/images/posts/ios38/E171D146-358B-41FD-98E2-FBC293EBAFEF.png" height="694" width="592"/><br/></div>
<div><br/></div>
<div>We can create a table using all the derived data:</div>
<div><br/></div>
<div><img src="/images/posts/ios38/%D0%A1%D0%BA%D1%80%D0%B8%D0%BD%D1%88%D0%BE%D1%82%202015-01-24%2020.41.30.png" height="761" width="738"/><br/></div>
<div><br/></div>
<div><br/></div>
<div>As we can see from the list of permissions, the developers tried to implement a security policy, but it wasn’t enough. Let’s show what we can achieve by manipulating the <i>ChatMessage</i> class.</div>
<div><br/></div>
<div>The most obvious vulnerability is that the attacker is able to modify the text of any message in any chatroom. After the execution of this code block the reasonable statement turns into a nonsense:</div>
<div><a href="https://gist.github.com/igrekde/cb8e2c12408715c9f739#file-parse-security-2">https://gist.github.com/igrekde/cb8e2c12408715c9f739#file-parse-security-2</a><br/></div>
<div><img src="/images/posts/ios38/cubefree-screen.png" height="569" width="642"/><br/></div>
<div><br/></div>
<div>We can also post new messages to any chat by providing a new <i>PFObject</i> with a correct chatId. But we are noble pentesters, so let’s pay attention to the fact that we aren’t able to delete any message due to developers paranoia :).</div>
<div><br/></div>
<div>A much more serious vulnerability consists in incorrect data mapping algorithm. When a <i>ChatMessage</i> object doesn’t have anything in the sender field, the Cubefree application crashes. So, it’s possible to loop through all the chatrooms, post an invalid <i>ChatMessage</i> - and the application will always crash when somebody opens the chat screen. It’s fraught with bad App Store ratings, users outflow and a complete project failure.</div>
<div>There are same vulnerabilities in other classes - but they are not within the scope of current investigation.</div>
<div><br/></div>
<div>As for security of your own applications - everything is quite transparent, just follow these simple rules:</div>
<ul>
<li>Always configure client permissions for all of your Parse classes.</li>
<li>Make use of user-based ACLs - it’s a great Parse feature.</li>
<li>If a client should be able to modify only one property of your class, think of encapsulating this field in the separate class. By doing it you will circumvent the possibility of illegal changes in your objects.</li>
<li>Don’t rely on Parse - always do a proper validity check of the data it returns you.</li>
<li>Remember that, theoretically, applicationID and clientKey can be found by any attacker, so you should build your application security grounding on this knowledge.</li>
<li>The previous advice doesn’t cancel the necessarily of obfuscating strings in code :)</li>
<li>Use Cloud Code in more complex situations.</li>
</ul>
<div><br/></div>
<div>If you recognise some of your applications in this research, don’t blame Parse - it’s the amazing service, which minimize backend developing costs. All of the examined vulnerabilities lie heavy on the developers conscience.</div>
<div><br/></div>
<div>Further reading:</div>
<ul>
<li><a href="https://www.parse.com/docs">Parse Documentation</a></li>
<li><a href="http://blog.parse.com/2014/06/30/parse-security-i-are-you-the-key-master">A small series of articles about Parse security</a></li>
<li><a href="https://github.com/igrekde/ParseRevealer">Parse Revealer</a></li>
<li><a href="http://highaltitudehacks.com/2014/01/17/ios-application-security-part-28-patching-ios-application-with-hopper/">iOS Application Security Part 28 - Patching iOS Application With Hopper</a><br/></li>
<li><a href="https://www.mikeash.com/pyblog/friday-qa-2012-01-06-the-hopper-disassembler.html">The Hopper Disassembler by Mike Ash</a></li>
</ul>
<div> </div>