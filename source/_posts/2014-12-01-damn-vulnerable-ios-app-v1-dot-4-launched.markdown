---
layout: post
title: "Damn Vulnerable iOS App v1.4 launched"
date: 2014-12-01 18:07
comments: true
categories: [security]
---

I am so excited to release the latest version of <a href="http://damnvulnerableiosapp.com">Damn Vulnerable iOS app for iOS 8.</a> Up till now, DVIA has been downloaded more than 75000 times and i can't wait for the count to reach 6 digits :-)

Following vulnerabilities and challenges have been added in the latest version.

<ol>
<li>Sensitive information in memory</li>
<li> Webkit Caching (Insecure data storage)</li>
<li>Certificate pinning bypass </li>
</ol>

You can download the latest version from <a href="http://damnvulnerableiosapp.com/#downloads">here</a>.
The source code is available on the project's github page <a href="https://github.com/prateek147/DVIA">here</a>.


<h3>Manual Installation</h3>

The easiest way is to install the application from Cydia. Add the source repo.kylelevin.com and search for DamnVulnerableiOSApp.

<img src="/images/posts/dvia/3.png" width="320" height="568" alt="3">

<!-- more -->


You can directly download the deb file also on your device and use dpkg -i DamnVulnerableiOSApp.deb to install the application followed by the command <i>uicache</i>

<img src="/images/posts/dvia/4.png" width="719" height="156" alt="4">


Or you can download the .ipa file from the <a href="http://damnvulnerableiosapp.com/#downloads">downloads</a> page, change its name from DamnVulnerableiOSApp.ipa to DamnVulnerableIOSApp.zip and unzip this file. This will unzip to a folder named Payload. Inside it, there will be a file named DamnVulnerableIOSApp.app. Then copy the .app file to the /Applications directory on the device using Scp. You can also use sftp or the utility iExplorer to upload this application.

<img src="/images/posts/dvia/1.png" width="1126" height="510" alt="1">

Now login as the mobile user, use the command su to get root privileges and give the DVIA binary executable permissions. Then use the exit command to go back as the mobile user, and use the command uicache to install the application. If this doesn’t work, you can reboot the device or try this method again.

<img src="/images/posts/dvia/2.png" width="799" height="284" alt="2">

To compile the application, you should follow the instructions mentioned <a href="http://damnvulnerableiosapp.com/2013/12/get-started/">here</a>.

Any commits to the source code on Github or suggestions to improve the app are welcome.

Special thanks to <a href="http://twitter.com/crylico">@crylico</a> to help test the application before release and hosting the application on his repo.

Happy hacking ! 

-Prateek
