---
layout: post
title: "Ghost USB Honeypot Part 1- Interview with Project Leader Sebastian Poeplau"
date: 2013-06-15 04:05
comments: true
categories: [security, honeypots]
---

<p>Malware threats have become very common these days. In the past, many honeypots have been created to detect malware propagation over the network. These honeypots trick the malware into believing that they are a part of the network. These honeypots are however isolated and once they have been infected, they can be used to study the behaviour of the malware. Network based malware have always been more successful, given the large number of systems they can affect in a short period of time.</p>

<!-- more -->


<p>These kind of network based malwares are however not able to infect systems that are isolated from the network. Such systems could contain highly confidential data. In the last few years, we have seen how USB based malwares can be used to target highly protected machines, i.e those which are not connected to the internet. This can allow these malwares to infect any system which supports plugging in of USB devices. One of the main examples of USB based malware is Stuxnet, a malware that used to spread via removable USB flash drives. In order to counteract the threat caused by USB based malware, the Ghost USB Honeypot project was created.</p>
	
<h2>Ghost USB Honeypot</h2>

<p>Ghost is a honeypot for detecting malware that spreads via USB devices. The honeypot currently supports Windows XP and Windows 7. The way Ghost works is that it first tries to emulate a USB thumb drive. If the malware identifies it as a  USB thumb drive, it will trick the malware into infecting it. Ghost then looks for write based requests on the drive, which is an indication of a malware. A good video about the project by Sebastian Poeplau can be found <a href ="http://www.youtube.com/watch?v=9G9oo3b9qR4">here</a>.</p>

<b>Interview with Project Leader Sebastian Poeplau</b>

<p>The project is lead by Sebastian Poeplau, here is an interview with him about the Ghost USB Honeypot Project.</p>

<h3>1. Can you please start by telling us something about yourself, and how you got interested in malware research ?</h3>

<p>My name is Sebastian Poeplau, i am 22 years old and pursuing my studies in Computer Science from Bonn university, Germany. My interest with malware research began when i was working under a professor in second year of college and was offered some student assistantship. I liked this domain of security a lot and this is how it all began.</p>

<p>This article was originally published on the <a href="http://resources.infosecinstitute.com/">resources</a> page at <a href="http://infosecinstitute.com/">Infosec Institute</a>. For more information, please visit my author <a href="http://resources.infosecinstitute.com/author/prateek/">page</a>.</p>


<h3>2. Can you please tell us in brief about the the Ghost USB honeypot project?</h3>

<p>During the course of our research in malware analysis and detection, we realized that there was no such honeypot which detects malwares that spread over USB devices.  So me and my project members at the Computer Science department at Bonn university decided to create one on our own. Ghost works by first emulating a USB storage device, which tricks the malware into infecting the honeypot. Currently, Ghost only supports Windows XP and Windows 7, but we plan to port it to different versions of Windows, mainly because malwares generally target Windows based systems.</p>
 
<h3>3. What are the major challenges faced in performing malware analysis today?</h3>	

<p>One of the major challenges is to be able to somehow analyze the huge amount of data that could be written to a honeypot to extract out the interesting bit of information. There are many good honeypots available that can capture the large amount of data, but there needs to be some kind of improvement in the current tools which could somehow help in automating the process of analyzing such a huge amount of data to get the interesting part.</p>

<h3>4. We have recently seen the emergence of some malwares that propagate over USB devices? This definitely helps the malware reach those systems that are not connected to the internet. Do you think there could be any other medium in the future over which malwares can propagate?</h3>

<p>I think there's just no need for malware authors to utilize another medium at the moment. These (network and USB) are the 2 major mediums over which malwares propagate. Also, since network based malwares have been so popular, i think we will see improvement in the way these malwares are created.</p>	
	

<h3>5. Currently, the Ghost USB honeypot supports only Windows XP and Windows 7. Are there any plans to port it to other operating systems?</h3>

<p>Currently, we don't have any plans to make it available for other operating systems like Linux and Mac OS. This is mainly because USB malware are mainly created for Windows based operating systems. We do have plans to make it available for other remaining versions of Windows though.</p>

<h3>6. What were the major challenges that you faced while working on this project?</h3>

<p>One of the major challenges that i faced was to emulate USB storage devices. The api inside the windows kernel wasn't designed to emulate USB device. So i had to find a certain level within the layered architecture of the kernel where i can start to do the emulation. The problem is that the malwares should somehow be tricked into believing that the device is a proper USB device, i.e with removable capability. So i had to first emulate the USB device and then make sure that the malware identifies it as a proper storage device.
	
	
	
<h3>7. Once the device is being emulated, how do you go about detecting the malware?</h3>

<p>It's simple. We just look for write requests. Any kind of write request without the user's permission is an indication of a possible malware.</p>

<h3>8. How far do you think has virtualization helped in facilitating the process of malware analysis? Also, where can we find samples of malware for testing purposes ?</h3>

<p>Virtualization has helped a lot. We no longer need a dedicated hardware to get infected with malware. It also helps in performing active analysis of the malware rather than just passive. This is because behaviour  of some of the malwares could only be analyzed in a live environment. <a href ="http://www.offensivecomputing.net/">Offensive Computing</a> has a good database of malwares that we can use for testing purposes. Also, people can now deploy their own honeypots in a virtual environment and use it for testing.</p>

<h3>9. What is the best way to stay in touch with the project?</h3>

<p>Keep checking the project <a href ="http://code.google.com/p/ghost-usb-honeypot/">homepage</a>. We post regular updates on it. Also, please feel free to get in touch with me at (Sebastian dot poeplau) on my gmail account. Also, you can follow by <a href="https://honeynet.org/blog/352">blog</a>.</p>
	

<ul>
<li><p>Ghost USB Honeypot</br><a href ="http://code.google.com/p/ghost-usb-honeypot/">http://code.google.com/p/ghost-usb-honeypot/</a></p>
</li>
<li><p>Sebastian Poeplau's blog</br><a href ="https://honeynet.org/blog/352">https://honeynet.org/blog/352</a></p></li>

 </ul>