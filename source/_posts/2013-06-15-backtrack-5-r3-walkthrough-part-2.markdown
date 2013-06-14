---
layout: post
title: "Backtrack 5 R3 Walkthrough part 2"
date: 2013-06-15 03:07
comments: true
categories: [security, backtrack]
---

<p>This article is in continuation to <a href="http://resources.infosecinstitute.com/backtrack-5-part-1/">part 1</a> of the Backtrack Walkthrough Series. In the previous articles we discussed some of the most important new tools that were added in the most recent revision of Backtrack 5 like Dnmap, Fern-Wifi-Cracker etc. In this article we will look at some of the other main tools added in Backtrack 5 R3.</p>

<!-- more -->

<h2>HTExploit</h2>

<p>HTExploit was released at Blackhat 2012 by Matias KATZ and Maximiliano SOLER. HTExploit (HiperText access Exploit) is a tool that is used to bypass authentication mechanisms which is deployed on websites using .htaccess files. The tool is written in Python. Once the restriction is bypassed, it will be possible to figure out the contents of a directory and even download those files. The tool works in a recursive manner,i.e once it downloads the first chunk of files, it looks for links inside those files and downloads those files as well. This process keeps on going until it has downloaded the entire content of the directory. It then generates an html report informing us about all the files that it has downloaded.</p>

<p>The tool has 2 modules that can be executed.</p>
<ol>
	<li>Detect- This module only informs the user if the target is vulnerable to the exploit or not.</li>
	<li>Full - This module runs the attack on the directory using a dictionary that contains a list of the common file names. If those file names are found and if the directory is vulnerable, it is possible to download that file from the server.</li>
</ol>

<p>In a recent chat session with Maximiliano Soler, one of the 2 people behind this tool, he informed us about how the actual vulnerability is exploited.</p>

<p>Maximilano Soler:<i>The main problem is that sometimes the restrictions are based in the directive by using "Limit GET POST".So the problem is that they only put these well known methods for authentication checking. But what happens when we create a different method? for eg. "POTATO".Apache will proceed with the request and pass this "unknown" method to PHP. PHP says...ok, I will use this method like "GET". Voila! If you have the exact name of the file, you will be able to download it. This is not a bruteforce attack as we are able to figure out the contents of your directory without knowing your password. There are some ways in which you can protect yourself. Remove the methods from "Limit". Use Limit with GET and POST, but also with LimitExcept. Alternatively, you can use a module from Apache Mod_AllowMethods. If you are a developer you could also validate the typical variables: $PHP_AUTH_USER, $_SERVER["REQUEST_METHOD"]. If something else is found that GET or POST, then disallow it.</i></p>
	
<p>Here is some sample code used in htaccess files that puts limitations for only GET and POST methods.</p>	
<img src="/images/posts/bt5r2/1.png" width="297" height="105" alt="1">
	
<p>HTExploit can be found under the directory /pentest/web/htexploit.</p>

<img src="/images/posts/bt5r2/2.png" width="653" height="391" alt="2">


<p>And now for a demo of HTExploit, i modified the htaccess settings in one of my websites and was able to successfully run the tool against it. Type the command as shown in the image below to run HTExploit against a targeted website.Once it detect that the target is vulnerable, it will ask you if you want to run a full scan on it. </p>

<img src="/images/posts/bt5r2/4.png" width="712" height="305" alt="4">

<p>After this, wait for the scan to complete.</p>

<img src="/images/posts/bt5r2/5.png" width="910" height="332" alt="5">

<p>Once the scan is run, it will generate a HTML report reporting the files that it was able to download locally. Here is what a sample report looks like.</p>

<img src="/images/posts/bt5r2/5_2.png" width="845" height="550" alt="5 2">

<h2>Wifi Honey</h2>

<p>Wifi Honey is another great tool that was introduced with Bactrack 5 R3. Basically, in most of the cases it is possible to crack the WEP or WPA encryption key of a network  with just a client which is probing for that network. In case of WEP, it is possible by Caffe Latte attack whereas in WPA, it is possible to capture the first 2 packets of the WPA handshake by using just the probing client and that gives us sufficient information in order to crack the WPA key for that network. Using airodump-ng, it is very easy to figure out which network (ESSID) the client is probing for. However, what is not clear by figuring out the ESSID of the probed network is the encryption that network is using. Only by knowing the kind of encryption will we be able to figure out how to crack the encryption. A general technique used to figure this out is by creating four different access points with encryption such as None, WEP, WPA, and WPA 2 using airbase-ng. The probing client will then connect to one of these networks and hence the kind of encryption being used is figured out. At the same time, airodump-ng could also be used to capture the traffic and hence later used to crack WPA. What Wifi Honey does is automate this whole process of creating fake Access points. It create 5 virtual interfaces, 4 of them for creating 4 AP's with same ESSID but different encryption and another 5th interface for airodump-ng to monitor the traffic on. Hence, at the time the probing client connects to our fake Access point, airodump-ng is being used to capture the traffic.</p>

<p>Wifi Honey takes 3 parameters, the ESSID of the network that is being probed, the channel no on which you want the AP to listen, and the interface on which you want to create it.</p>

<img src="/images/posts/bt5r2/6.png" width="733" height="167" alt="6">

<p>Once we enter this, we will see that it creates 4 networks of the same name with different encryption and also starts airodump-ng at the same time to capture the traffic. Now the probing client will connect to this network and the captured traffic by airodump-ng could be used to crack the encryption key.</p>

<img src="/images/posts/bt5r2/7.png" width="1050" height="562" alt="7">

<h2>UrlCrazy</h2>

<p>URLCrazy is a tool to determine if a domain name is being abused or not by looking at different examples of domain names caused by typos in the original domain name. For e.g a phishing attack can be carried out very easily by changing just one character in a domain name and then redirecting the user to that domain name, mainly because the user will not be able to recognize the change. What Urlcrazy does is use typos in your domain names to generate new domain names and figure out if those domain names exist or not. If they exist, it fetches out info like A and MX records for that particular domain name.</p>

<p>Here are the different options available in urlcrazy.</p>

<img src="/images/posts/bt5r2/8.png" width="796" height="574" alt="8">

<p>Let's run a urlcrazy query against google.com. As you can see, it found a number of domain names similar to Google.</p>

 <img src="/images/posts/bt5r2/9.png" width="878" height="613" alt="9">

<img src="/images/posts/bt5r2/10.png" width="873" height="600" alt="10">

<p>In some cases, the url found may not be used for malicious purposes. For e.g google.fr is just the French version of Google and so on. However, some other search results look like they were bought mainly to be used in case someone typed that domain name instead of Google by mistake. Overall, this tool could be highly beneficial to large corporations who are looking to protect themselves from phishing attacks and any other form of corporate espionage.</p>

<h2>References:</h2>

<ul>
 <li><p>HTExploit</br><a href ="http://www.mkit.com.ar/labs/htexploit/">http://www.mkit.com.ar/labs/htexploit/</a></p>
 </li>
 <li><p>Wifi Honey - DigiNinja</br><a href ="http://www.digininja.org/projects/wifi_honey.php">http://www.digininja.org/projects/wifi_honey.php </a></p>
 </li>
 <li><p>URLCrazy</br><a href ="http://www.morningstarsecurity.com/research/urlcrazy">http://www.morningstarsecurity.com/research/urlcrazy</a></p>
 </li>
</ul>