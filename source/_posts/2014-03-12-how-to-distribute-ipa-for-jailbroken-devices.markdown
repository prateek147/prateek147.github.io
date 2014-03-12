---
layout: post
title: "How to distribute IPA file for jailbroken devices"
date: 2014-03-12 11:53
comments: true
categories: [ios, security]
---

So i have been getting a few queries on how to create an IPA file from Xcode and distribute it for jailbroken devices. Here is how i did it for <a href="http://damnvulnerableiosapp.com">Damn Vulnerable iOS App</a>.

First we need to run the application using Xcode on the device. This requires a valid provisioning profile. I am doing this on Xcode 5.x but on the previous versions of Xcode, it was possible to run the application on the device without a valid provisioning profile. 

Once the application is installed on the device, copy the .app folder from the device on your system.

<img src="/images/posts/distribute/1.png" width="525" height="320" alt="2">

<!-- more -->


Navigate inside this directory on your system and self sign the application binary using ldid. Make sure you have a proper working version of ldid installed.

<img src="/images/posts/distribute/2.png" width="681" height="64" alt="2">

Now create a new folder and name it Payload. Put the .app folder inside it and compress the Payload folder. It will be named Payload.zip. Rename Payload.zip to [APP_NAME].ipa, for e.g DamnVulnerableiOSApp.ipa. Once this is done, you can install the application using similar techniques as mentioned in the youtube video below.

<iframe width="560" height="315" src="//www.youtube.com/embed/PwES8Sk00wk" frameborder="0" allowfullscreen></iframe>