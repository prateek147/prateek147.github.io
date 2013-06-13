---
layout: post
title: "W3af walkthrough Part 1"
date: 2013-06-13 08:49
comments: true
categories: [w3af, web-application-security]
---

<p>w3af (Web Application audit and attack framework) is a framework for auditing and exploitation of web applications. In this series of articles we will be looking at almost all the features that w3af has to offer and discuss how to use them for Web application Penetration testing. In the first part of this series we will be working with w3af console and getting ourselves familiar with the commands. We will also be looking at the different types of plugins that w3af has to offer and discuss how to use them for optimal performance. </p>

<!-- more -->

<p>Some of the major features of w3af are:</p>

<ol>
	<li>It has plugins that communicate with each other. For eg. the discovery plugin in w3af looks for different url's to test for vulnerabilities and passes it on to the audit plugin which then uses these URL's to search for vulnerabilities.</li>
	<li>It removes some of the headaches involved in Manual web application testing through its Fuzzy and Manual request generator feature. It can also be configured to run as a MITM proxy. The requests intercepted can be sent to the request generator and then manual web application testing can be performed using variable parameters.</li>
	<li>It also has features to exploit the vulnerabilities that it finds.</li>
</ol>
		
		
<p>It is important to understand that no  automated web application scanner is perfect and false positives will always occur. With w3af the first and the foremost step is to make sure that we have the latest version. This is very important because w3af developers (Andres Riancho and the w3af team) are constantly fixing bugs and hence it is very important to make sure that we have the most bug free version. To open up w3af console, type in the command as shown in the figure below.</p>

<img src="/images/posts/w3af1/1.png" width="640" height="47" alt="1">

<p>w3af may ask you to update the version. It is advisable to keep updated with the latest version. Ok, so now that we are in the console, type in <i>help</i> to look at the list of available commands.</p>

<img src="/images/posts/w3af1/2.png" width="645" height="382" alt="2">


<p>We can see the list of available options available to us. Type the <i>keys</i> command to look at the various shortcuts keys available to us. I recommend you get familiar with them.</p>

<img src="/images/posts/w3af1/3.png" width="758" height="149" alt="3">


<p>Let's have a look at the plugins which are available in w3af.  Type <i>plugins</i>. You can see the console output change to <i>w3af/plugins</i>. Type <i>back</i> to go back or type <i>help</i> to display the list of available plugins.</p>

<p>To know information about a specific plugins, just type <i>help pluginName</i>. For e.g if i want to know about the discovery plugin, i would type <i>help discovery</i>.</p>

<img src="/images/posts/w3af1/5.png" width="788" height="365" alt="5">


<p> We can see that there are about 9 types of different plugins. </p>

<p><b>1)Discovery</b>- The discovery plugin helps in finding more Url's, forms etc to be used for vulnerability scanning. This information is then passed over to the audit plugin. There are a number of different discovery plugins like webSpider, spiderMan, hmap etc. All these plugins have a different function. A user can enable one or more plugins at the same time.</p>

<p>To see the discovery plugins, just type <i>discovery</i>.</p>
	
	
<img src="/images/posts/w3af1/4.png" width="1405" height="335" alt="4">

<p>To find specific information about a particular plugin, just type <i>pluginType desc pluginname</i>. For e.g if i want to know more information about the spiderMan <a href="index.html" id="" title="index">index</a>plugin i would write the command <i>discovery desc spiderMan</i>.</p>

<img src="/images/posts/w3af1/6.png" width="1018" height="343" alt="6">

<p>One of the important things to note here is that the spiderMan plugin has 2 configurable parameters. To set the configurable parameters, type in the following commands as shown in the figure below. As you can see from the figure below, i have set the listenPort to 55555.</p>

<img src="/images/posts/w3af1/7.png" width="1100" height="295" alt="7">

<p>Here are some other commands that could be used.</p>
<p>1) discovery pluginType1, pluginType2 - Selects two plugins. </p>
<p>2) discovery all- Enables all the plugins (not advisable as it may take a long time to finish).</p>
<p>3) discovery !all - Removes all the enabled plugins.</p>
<p>4) list discovery enabled - Lists all the plugins currently enabled.</p>

<p>Here is a screenshot below showing some of these commands in action.</p>

<img src="/images/posts/w3af1/8.png" width="1423" height="344" alt="8">

<p>Let's now run one of the discovery plugins. I will be using the hmap plugin in discovery to know the version of the server running on a remote host. As you can see from the figure below, i have enabled the hmap plugin.</p>

<img src="/images/posts/w3af1/9.png" width="1155" height="168" alt="9">


<p>Once this is done, it is now time to give the location of the target server. Type <i>back</i> to navigate back. Then type the following commands as shown in the figure below to set the target. As we can see, the target is set by the <i>set target target-address</i> command.</p>

<img src="/images/posts/w3af1/10.png" width="1075" height="212" alt="10">


<p>Once this is done, type <i>back</i> to navigate back and the type <i>start</i> to start the plugin. As we can see, w3af has figured out the version of Apache and php running on my server. We will discuss more features of the discovery plugin later.</p>

<img src="/images/posts/w3af1/11.png" width="870" height="240" alt="11">

<p><b>2)Audit</b>-Audit plugins are used to detect vulnerabilities in the URL's or forms provided by the discovery plugins. This is where the interaction between plugins in w3af comes to use. The audit plugin has options for testing different types of vulnerabilities like xss, sqli, csrf etc. It does this by injecting different strings in its request and then looking for a specific value (corresponding to the input string) in the response. False positives may occur during this process. If i want to know how the sqli plugin works, i could type in the commands as shown in the figure below.</p>

<img src="/images/posts/w3af1/12.png" width="870" height="150" alt="12">

<p>Again, i can set the different configuration parameters while selecting a particular plugin. For e.g in the figure below i am increasing the number of checks while performing a XSS audit. </p>

<img src="/images/posts/w3af1/13.png" width="1075" height="166" alt="13">


<p><b>3)Grep</b> - The grep plugin is used to find interesting information in the requests and responses going through like email accounts, forms with file upload capabilities, hashes, credit card numbers, email addresses etc. You can set the type of information you want to look for by setting the appropriate plugin. Since the grep plugin only analyzes the request and response, it is important to have some kind of discovery plugin enabled for it to work. Otherwise grep plugins are of no use. As you can see in the figure below i have set grep to use the getMails plugin.</p> 

<img src="/images/posts/w3af1/14.png" width="1112" height="144" alt="14">

<p><b>4)Brute force</b> - Brute force plugins can be used to brute force login forms as well as http-auth logins. Once the discovery plugin finds any form with form based input or an http-auth input it will automatically launch the brute force attack against it if the corresponding brute force plugin is enabled. Some of the important things to know about the brute force are the configuration parameters. </p>

<img src="/images/posts/w3af1/15.png" width="1061" height="458" alt="15">

<p>It is advisable that you use your own configuration file for the list of usernames and passwords. Also be sure to take a look at some other options. As you can see in the figure below, i have set the option passEqUser to false simply because i don't think users wouldn't have their passwords as the same as their username.</p>

<img src="/images/posts/w3af1/16.png" width="1413" height="304" alt="16">

<p>One of the other good configurable parameter is the useMails option. This options uses the email addresses that w3af finds (maybe through the grep plugin) to be one of the inputs for the username field. For e.g if one of the usernames is example@infosecinstitute.com, then the username tried would be example. This is another example of how the interaction between the different plugins could make the job much more effective.</p>

<p><b>5)Output</b> - The output plugin helps us decide the format in which we want the output. w3af supports many formats like console, emailReport, html, xml, text etc. Again you can set various parameters here like the filename, verbosity etc. In the figure below, i have set <i>verbose</i> to True as i want a very detailed report about the application that i am testing.</p>

<img src="/images/posts/w3af1/17.png" width="882" height="205" alt="17">

<p><b>6)Mangle</b> - The mangle plugin is used to mangle with request and responses on the fly. It has only one plugin named sed (Stream editor) which is used to modify requests and responses using different regular expressions. The expressions should have a specific format. The usage is quite evident from the description.</p>

<img src="/images/posts/w3af1/18.png" width="857" height="353" alt="18">

<p>As you can see from the figure below, i have set the plugin to look for the string Yahoo and replace it with Google in the request header.</p>

<img src="/images/posts/w3af1/19.png" width="1086" height="175" alt="19">


<p><b>7)Evasion</b>- The evasion plugins uses various techniques to bypass WAF (Web application firewalls). For e.g one of the options rndHexEncode randomly encodes the url in hex format to avoid detection while the plugin fullWidthEncode does a full width encode of the Url to bypass Http content scanning systems using the vulnerability described <a href="http://www.kb.cert.org/vuls/id/739224">here</a>.</p>

<img src="/images/posts/w3af1/20.png" width="1397" height="292" alt="20">

<p><b>8)Auth</b> - Last but not the least, auth plugin is one of the most important plugins in w3af. It has only one type called generic. This is because while crawling on a target web application, if w3af hits a login form, then it needs to submit the credentials automatically in order to continue looking for information. By using this plugin, we can specify a predefined username/password that w3af should enter when it hits a login form. We need to specify all the parameters for generic in order for it to work successfully.</p>

<p>In the figure below i am setting options for w3af to successfully log in to DVWA (Damn vulnerable web application) which is located on the address http://10.0.1.24/dvwa</p>

<img src="/images/posts/w3af1/21.png" width="1400" height="411" alt="21">

<h2>Conclusion</h2>

<p>In this article we discussed about the plugins available in w3af and learnt how to work with the w3af console. In the upcoming articles in this series, we are going to discuss the following topics.</p>

<ol>
	<li>Using different profiles</li>
	<li>Exploiting a vulnerability found by the audit plugin</li>
	<li>Using the Manual Request and Fuzzy request feature</li>
	<li>Using the Mitm proxy and the encoder/decoder features</li>
	<li>w3af scripting</li>
	<li>Optimizing w3af scans</li>
</ol>

<p>Please drop a comment if you liked the article or if there is something about w3af that you want to see in the upcoming articles.</p>

<h2>References:</h2>

<ul>
     <li><p>w3af User Guide</br><a href ="http://w3af.sourceforge.net/documentation/user/w3afUsersGuide.pdf">http://w3af.sourceforge.net/documentation/user/w3afUsersGuide.pdf</a></p>
     </li>
</ul>