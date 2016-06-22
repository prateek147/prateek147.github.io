---
layout: post
title: "Android Application hacking with Insecure Bank Part 4"
date: 2015-03-29 00:17
comments: true
categories: [security]
---

<p>In this article, we will look at a very handy framework for analysis of android applications named Drozer. Drozer is a very useful tool as it eliminates the need for having seperate tools for performing different security checks in an android application. It has a list of modules that you can use to interact with the application using Android's Inter-Process communication. Additionally, you can also install exploits and use it to exploit an android device.</p>

<p>The main purpose of this article is to make sure you are familiar with drozer so we can use it in the future articles. </p>

<!-- more -->

<p>The first thing to do is to install the drozer community edition from <a href="https://www.mwrinfosecurity.com/products/drozer/">this</a> link. You need to install both the drozer installer and the Agent.apk file which is the application that needs to be deployed on the device/emulator and acts as a communicator between the system and the application to be audited. </p>

<p>Once drozer has been installed on your system, install the agent.apk on your device/emulator.</p>

<img src="/images/posts/ib4/1.png" width="600" height="90" alt="1">

<p>You will first need to set up port forwarding so that your system can connect to a TCP socket opened by the Agent inside the emulator, or on the device. By default, drozer uses port 31415:</p>

<img src="/images/posts/ib4/2.png" width="800" height="54" alt="2">

<p>Also make sure to start the agent application and start the server.</p>

<img src="/images/posts/ib4/3.png" width="513" height="277" alt="3">


<p>Now you can connect to the agent using the following command.</p>

<img src="/images/posts/ib4/4.png" width="750" height="360" alt="4">

<p>You can list all the different modules by using the list command.</p>

<img src="/images/posts/ib4/5.png" width="900" height="220" alt="5">

<p>Every module requires different options. If you want to see the different options for a particular module, use run followed by the module name followed by -h.</p>

<img src="/images/posts/ib4/6.png" width="900" height="420" alt="6">

<p>For e.g, to see a list of all the packages installed, you can use the module app.package.list.</p>

<img src="/images/posts/ib4/7.png" width="500" height="220" alt="7">

<p>Now, to find info about a particular packages, use the module app.package.info. It will give out a lot of info about the application, for e.g the path where the application files are stored, the permissions that the application uses etc.</p>

<img src="/images/posts/ib4/8.png" width="550" height="280" alt="8">


<p>Another useful module is app.package.attacksurface. It tells you about the exported components as well as whether the application is debuggable or not. We will look at exploiting debuggable applications in later articles.</p>


<img src="/images/posts/ib4/9.png" width="500" height="120" alt="9">

<p>Now, let's do the same thing we did in the last article, call an exported activity in the insecure bank application. For that, we will use the module app.activity.start.</p>

<img src="/images/posts/ib4/10.png" width="800" height="32" alt="10">

<p>And you will see the same result.</p>

<img src="/images/posts/ib4/11.png" width="700" height="320" alt="11">

<p>In some cases, the activity might have an intent filter. For e.g, below is a sample intent filter.</p>

<p>
	
<activity android:name="ShareActivity">
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="text/plain"/>
    </intent-filter>
</activity>
</p>

<p>Drozer supports calling activities by specifying actions and extra paramters also.</p>

<img src="/images/posts/ib4/12.png" width="700" height="245" alt="12">

<p>Here is an example of calling an activity with extra parameter</p>

<p><i>
run app.activity.start --component com.mwr.example.intenttest com.mwr.example.intenttest.IntentActivity --flags ACTIVITY_NEW_TASK --extra string URL "Some Text"
</i></p>

<p>In this article, we got comfortable with using Drozer. Drozer can do much more, and we will be discussing all those features as we discuss more vulnerabilities in InsecureBank in the next article.</p>