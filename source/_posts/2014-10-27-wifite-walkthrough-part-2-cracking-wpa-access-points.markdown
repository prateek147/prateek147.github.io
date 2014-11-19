---
layout: post 
title: "Wifite Walkthrough part 2: Cracking WPA access points"
date: 2014-10-27 01:43
comments: true
categories: [security]
---

<p>In this article, we will look at cracking access points using WPA-PSK or WPA2-PSK using Wifite.</p>

<p>If you have used tools like airodump-ng, aircrack-ng etc to crack WPA access points before, you would know that the required thing to successfully crack a WPA-PSK network is a captured WPA four-way handshake. More details about the WPA four-way handshake can be found on <a href="http://en.wikipedia.org/wiki/IEEE_802.11i-2004">this</a> wikipedia page.</p>

<p>As mentioned in the previous article, there is a bug in Wifite that may or may not be there in your particular version of Wifite. The bug basically doesn't aireplay-ng to function properly and displays an error like <i>aireplay-ng exited unexpectedly </i>. In order to fix this, you will have to make slight modifications in the code of wifite. You can install gedit (apt-get install gedit) which is a text editor and then edit the wifite python script (found in /usr/bin/wifite) using the steps mentioned <a href="https://code.google.com/p/wifite/issues/detail?id=127">here</a>. To open wifite, use the command <i>gedit /usr/bin/wifite</i>. This will open up the source code of wifite. Then replace every occurence of <i>cmd = ['aireplay-ng',</i> with <i>cmd = ['aireplay-ng','--ignore-negative-one',</i></p>

<!-- more -->

<p>To start wifite for cracking a WPA access point, give it the option <i>-wpa</i> to only target WPA networks. Also, give it a dictionary file as an input for cracking the WPA passphrase with the <i>-dict</i> option. In kali linux, the wordlists are stored at the location <i>/usr/share/wordlists</i>. Wifite will now start scanning for WPA access points.</p>

<img src="/images/posts/wifite2/1.png" width="947" height="316" alt="1">

<p>Press Ctrl+C to give a target number. In my case, the target number is 2 which is an access point i have configured for testing purposes. The access point uses WPA2-PSK encryption with the key as "password".</p>

<img src="/images/posts/wifite2/2.png" width="741" height="443" alt="2">

<p>Wifite will now start listening for the handshake. Once it has found it, it will automatically start cracking the passphrase using the dictionary file that we supplied.</p>

<img src="/images/posts/wifite2/4.png" width="834" height="358" alt="4">

<p>And as you can see, Wifite has successfully found the passphrase for the access point.</p>

<p>Sometimes, things may not work as smoothly. In order to capture a WPA handshake between the client and the access point, the client has to connect to the wireless network during that period when we are monitoring the network. If the client is already connected, there will be no handshake that is captured. Wifite does this by automatically sending deauthentication packets to a particular client or a broadcast deauthentication packet if it is required. You can specify the time between deauthentication packets using the -wpadt flag. Hence, when the client tries to reconnect to the access point, the handshake is captured.</p>

<p>You can also specify which tool you want to use to crack the passphrase once the four-way handshake has been successfully captured. By default, aircrack-ng is selected. You can also use cowpatty, pyrit or tshark to crack the passphrase.</p>

<img src="/images/posts/wifite2/5.png" width="622" height="92" alt="5">

<p>Another cool option in Wifite is to anonymize your MAC address using the <i>-mac</i> option. Even though it is quite trivial using simple commands or <i>macchanger</i> utility to change the MAC address for a specific interface, it is good to have this feature in the tool itself. However, in order to make this work, you first have to take that specific interface for which you want to change the MAC address down to managed mode if it is in monitor mode previously. You can use the command <i>iwconfig</i> to check all the interfaces that are in monitor mode and then take them down using the command <i>airmon-ng stop interface-name</i> command. As we can see from the image below, the mon0 interface is in monitor mode.</p>

<img src="/images/posts/wifite2/6.png" width="698" height="287" alt="6">

<p>Hence, lets take it down using the command <i>airmon-ng stop mon0</i></p>.

<img src="/images/posts/wifite2/7.png" width="678" height="351" alt="7">

<p>Now we can add the <i>-mac</i> option to anonymize the MAC address. As you can see, Wifite is intelligent enough to change the MAC address to something that is similar the existing MAC address of the interface and not to something ridiculous (for e.g AA:BB:CC:DD:EE:FF) which is a giveaway.</p>

<img src="/images/posts/wifite2/8.png" width="895" height="378" alt="8">

<p>And when you stop the capture, Wifite is nice enough to change the MAC address back to the original one.</p>

<p>In this article, we looked at how we can use Wifite to crack networks using WPA-PSK or WPA2-PSK. Wifite is great at its job and automates almost everything, however it is important to understand how it uses the tools like airodump-ng, aircrack-ng etc under the hood to perform its task. I would recommend that you go through the source code of Wifite and figure out how this is done.</p>
