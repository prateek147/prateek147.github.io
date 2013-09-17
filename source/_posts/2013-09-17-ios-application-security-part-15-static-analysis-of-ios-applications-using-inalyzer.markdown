---
layout: post
title: "IOS Application Security Part 15 – Static Analysis of IOS Applications using iNalyzer"
date: 2013-09-17 13:11
comments: true
categories: [ios, security]
---

<p>In the previous article, we looked at how we can use Sogeti Data protection tools to boot an iDevice using a custom ramdisk with the help of a bootrom exploit. In this article, we will look at a tool named iNalyzer than we can use for black box assessment of IOS applications. iNalyzer allows us to view the class information, perform runtime analysis and many other things. Basically it automates the efforts of decrypting the application, dumping class information and presents it in a much more presentable way. We can also hook into a running process just like Cycript and invoke methods during runtime. iNalyzer is developed and maintained by <a href="https://appsec-labs.com">AppSec Labs</a> and its offical page can be found <a href="https://appsec-labs.com/iNalyzer">here</a>. iNalyzer is also made available open source and its github page can be found <a href="https://github.com/appsec-labs/iNalyzer">here</a>.</p>

<!-- more -->


<p>iNalyzer require some dependencies to be installed before use. Please make sure to install <a href="http://www.graphviz.org/download..php">Graphviz</a> and <a href="http://www.stack.nl/~dimitri/doxygen/download.html#srcbin">Doxygen</a> as iNalyzer won't function without these. Also, please note that while performing my tests on a Mac OS X Mountain Lion 10.8.4, i had problems with the latest version of Graphviz and it used to hang every time. Hence i downloaded an older version of Graphviz (v 2.30.1) and it worked fine for me. You can find older versions of Graphviz for Mac OS <a href="http://www.graphviz.org/pub/graphviz/stable/macos/">here</a>.</p>

<p>The first step is to install iNalyzer on your jailbroken iDevice. To do that, go to Cydia --> Manage and make sure the source http://appsec-labs.com/cydia/ is added as shown in the image below.</p>

<img src="/images/posts/ios15/1.PNG" width="320" height="480" alt="1">

<p>Then go to Search and search for <i>iNalyzer</i>. Depending on the IOS version that you are running, you should download the corresponding version of iNalyzer.</p>

<img src="/images/posts/ios15/2.PNG" width="320" height="480" alt="2">

<p>As you can see, i have already installed iNalyzer.</p>

<img src="/images/posts/ios15/3.PNG" width="320" height="480" alt="3">

<p>Now ssh into the device and navigate inside the iNalyzer application directory. iNalyzer is installed in the <i>/Applications</i> directory because it needs to run as a root user. If you don't understand this concept, please make sure to read the previous articles in this series.</p>

<img src="/images/posts/ios15/4.png" width="569" height="91" alt="4">

<p>Run ./iNalyzer to start iNalyzer.</p>

<img src="/images/posts/ios15/5.png" width="573" height="247" alt="5">


<p>Now if you go to your homescreen and look at the iNalyzer app icon, you will see a badge icon number on top of it. This indicates that the app can be remotely accessed via a web interface and the port number is the badge icon number represented here. If you run ./iNalyzer again, it will stop iNalyzer. Hence, make sure to remember that running ./iNalyzer starts and closes the application alternatively.</p>

<img src="/images/posts/ios15/6.png" width="255" height="160" alt="6">

<p>Now find the ip address of your iDevice and open the url ip:port from your browser. In my case the port number is 5544 and the ip address of the device is 10.0.1.23 . Hence the url is http://10.0.1.23:5544/ . Once you go there, you will be presented with an interface as shown in the figure below. You can then select an application and iNalyzer will prepare a zip file and download it on your system for analysis.</p>

<img src="/images/posts/ios15/7.png" width="598" height="498" alt="7">
<p>However, i had some problems while performing this. Hence, we will also be looking at an alternative solution to do the same step. To do that, first of all make sure iNalyzer is running. Then navigate inside the iNalyzer directory and run <i>iNalyzer5</i> without any arguments.</p>

<img src="/images/posts/ios15/8.png" width="542" height="163" alt="8">

<p>You will see all the list of apps available for analysis. In this case, let's select the Defcon App for analysis.</p>

<img src="/images/posts/ios15/9.png" width="834" height="162" alt="9">

<p>You will see that iNalyzer begins its work. It decrypts the app, finds out the class information and other things. As you can see from the figure below, once iNalyzer has finished its job, it will create an ipa file and store it at the location as highlighted in the image below.</p>

<img src="/images/posts/ios15/10.png" width="860" height="170" alt="10">

<p>So now we need to get this ipa file and download it on our system. We can do that via sftp.</p>

<img src="/images/posts/ios15/11.png" width="859" height="106" alt="11">

<p>Once we have the ipa file, change its extension to zip. Then unzip the file.</p>

<img src="/images/posts/ios15/12.png" width="824" height="237" alt="12">

<p>Now with a terminal, navigate inside the folder Payload-->Doxygen.</p>

<img src="/images/posts/ios15/13.png" width="862" height="108" alt="13">

<p>You will see a shell script named doxMe.sh. If you look inside it, you will notice that it automates the task of running Doxygen for us. Doxygen also runs Graphviz for generating graphs and the results are stored inside a folder with the name <i>html</i>. Basically, iNalyzer has already stored all the class information for us inside a folder named <i>Reversing Files</i> and it uses Doxygen and Graphviz to display the information in a much more presentable format.This shell script also opens up the <i>index.html</i> file inside the created <i>html</i> folder.</p>

<img src="/images/posts/ios15/14.png" width="860" height="136" alt="14">

<p>So lets run this shell script and let iNalyzer do all the things for us.</p>

<img src="/images/posts/ios15/15.png" width="859" height="358" alt="15">
<img src="/images/posts/ios15/16.png" width="450" height="358" alt="16">

<p>Once this is done, iNalyzer will automatically open up the index.html file stored inside the html folder that was created. Here is what it looks like. In this case, i am using chrome. However, the developer of this tool personally recommended me to use firefox browser for runtime analysis as the other browsers may be buggy. As you can see from the image below, the first page gives a strings analysis of the entire app. It divides the strings into SQL and URL strings.</p>

<img src="/images/posts/ios15/17.png" width="1440" height="722" alt="17">

<p>You can also have a look at all the view controller classes used in the app.</p>

<img src="/images/posts/ios15/18.png" width="1439" height="718" alt="18">

<p>Tapping on any of the view controllers will show you its methods and properties.</p>

<img src="/images/posts/ios15/19.png" width="1440" height="729" alt="19">

<p>You can also look at the contents of the Info.plist file.</p>

<img src="/images/posts/ios15/20.png" width="774" height="629" alt="20">

<p>If you go under the Classes Tab and under <i>Class Index</i> you will see a list of all the classes being used in the app. Some of them are Apple's own classes while some are created by the developer of this app.</p>

<img src="/images/posts/ios15/21.png" width="1440" height="685" alt="21">

<p>If you go under the <i>Class Hierarchy</i> tab, you will see the class information and relationships being represented in a graphical format. This gives us a fair amount of knowledge on how this application works. These graphs are generated by the Graphviz tool.</p>

<img src="/images/posts/ios15/22.png" width="1440" height="719" alt="22">

<p>If you go to the files tab, you can have a look at all the interface files that iNalyzer generated.</p>

<img src="/images/posts/ios15/23.png" width="1438" height="721" alt="23">

<b>Conclusion</b>

<p>In this article, we looked at static analysis of IOS applications using iNalyzer and how easy it makes our job. In the next article, we will look at how we can use iNalyzer further for runtime analysis of IOS applications.</p>

<b>References</b>

<ul>
     <li><p>iNalyzer</br><a href ="https://appsec-labs.com/iNalyzer">https://appsec-labs.com/iNalyzer</a></p>
     </li>
	
</ul>