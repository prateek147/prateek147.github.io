---
layout: post
title: "IOS Application security Part 5 – Advanced Runtime analysis and manipulation using Cycript (Yahoo Weather App)"
date: 2013-07-02 17:26
comments: true
categories: [ios, security]
---

<p>In the previous article, we learnt how to setup Cycript on your idevice, hook into a running process and obtain information about its properties in runtime. In this article, we will look at some advanced runtime analysis techniques. We will look at how we can obtain information about a particular class (methods, instance variables) and modify them at runtime.</p>

<h2>Finding methods for a particular class</h2>

<p>Let's say we are analyzing the flow of an app during its runtime. It would be really good to know what are the methods being called in a particular view controller or in a particular class. Since Cycript is a blend of Objective-C and Javascript, we can write a function that has both Objective-C and Javscript syntax. We can define functions in the interpreter and use them anytime we want to find out some particular information. A good source for finding such code snippets is available <a href="http://iphonedevwiki.net/index.php/Cycript_Tricks">here</a> and we will be using most of the code snippets from here for this article.</p>

<p>First of all, lets make sure we are hooked into the running process.</p>

<img src="/images/posts/ios5/1.png" width="892" height="186" alt="1">

<!-- more -->

<p>Let's define a method that prints out the methods for a particular class. You can find the code snippet on the Cycript tricks page <a href="http://iphonedevwiki.net/index.php/Cycript_Tricks">here</a>.</p>

<img src="/images/posts/ios5/2.png" width="892" height="310" alt="2">

<p>Now that we have the method defined, we can input any class here and get the corresponding methods for it. From the previous article, we found out that the delegate class for this app was YWAppDelegate. Hence, let's try and see all the methods contained in this class.</p>

<img src="/images/posts/ios5/3.png" width="1102" height="285" alt="3">

<p>This gives us all the methods defined in the class YWAppDelegate. Everything after the @selector is the name of the method. Note that this will also give us information about the private methods. Also, this will also include the getters and setters for the properties defined in the class.</p>

<p>Similarly, we can also print out the methods of YahooSlidingViewController.</p>

<img src="/images/posts/ios5/4.png" width="1379" height="733" alt="4">

<p>We know that YahooSlidingViewController manages the sliding meny and works as a facade over the other view controllers. In order to find out the view controller that is actually responsible for displaying weather in the app, we can use the following command.</p>

<img src="/images/posts/ios5/5.png" width="1032" height="130" alt="5">

<p>Hence, the YWMainViewController is the view controller responsible for displaying the weather in the app. So the view shown in the screenshot below is actually the one coming from YWMainViewController.</p>

<img src="/images/posts/ios5/IMG_0094.PNG" width="640" height="960" alt="IMG 0094">

<p>Let's print out the methods for YWMainViewController.</p>

<img src="/images/posts/ios5/6.png" width="1380" height="651" alt="6">

<p>As you can see, there is a method named userDidRequestUpdate.</p>

<img src="/images/posts/ios5/9y.png" width="861" height="17" alt="9y">

<p>From the method name, its obvious that this method gets called whenever the user pulls down on the app to refresh. With Cycript, we can call this method anytime we want. We will have to reference this view controller and then call this method on it. Here is how it's done.</p>

<img src="/images/posts/ios5/9.png" width="1273" height="36" alt="9">

<p>And if you see in the app, the update method gets called even though we didn't actually pull down.</p>


<img src="/images/posts/ios5/IMG_0097.PNG" width="640" height="960" alt="IMG 0097">
<p>As told before in this article, these methods also contain the getters and setters of the properties.</p>

<p>From a security point of view, such power to manipulate the runtime of an application gives us a lot of advantages.We can call any method whenever we want in the app. Imagine a flow in the app where the user first logs in the app by entering the username/password and then once he is logged in, a method named <i>didLogin</i> gets called. In our case, we can just call this method ourselves without having to enter any username/password combination.</p>

<p>It would be a bit helpful if we could print out all the variables used in a particular view controller. So lets define a function that prints out all the instance variables. You can find the code snippets <a href="http://iphonedevwiki.net/index.php/Cycript_Tricks">here</a> </p>

<img src="/images/posts/ios5/7.png" width="999" height="76" alt="7">

<p>Now, lets print out the instance variables for YWMainViewController.</p>


<img src="/images/posts/ios5/8.png" width="1374" height="431" alt="8">

<p>As you can see, there is an instance variable named location view controllers. In the Yahoo weather app, you can swipe left and right to see the weather for different locations. From its name, it looks like the variable locationViewControllers is an array of view controllers which is responsible for holding a list of location view controllers. Using Cycript, we can also print out the value of this instance variable.</p>

<img src="/images/posts/ios5/9x.png" width="1272" height="75" alt="9x">

<p>Now let me swipe right to another location New york.</p>

<img src="/images/posts/ios5/IMG_0098.PNG" width="640" height="960" alt="IMG 0098">

<p>Let's print the value of this variable now.</p>

<img src="/images/posts/ios5/10.png" width="1269" height="59" alt="10">

<p>As you can see, this array always has 3 location view controllers inside it and the others are null. It doesn't contain all the reference of location view controllers so as to manage memory better. So at a particular time, we can have the view controller that we are looking at, and the left and right view controllers being instantiated. When we move to a different location, it automatically adjusts to make the visible view controller instance the one in the center and instantiates the left and right view controllers so the user can swipe left and right and won't face any delay. This is one example of writing code that doesn't take up much memory.</p> 

<h3>Conclusion</h3>

<p>In the previous two articles, we have performed the runtime analysis of the Yahoo weather app. In the next article, we will be looking at some more Cycript tricks and will focus specially on a technique known as method swizzling.</p>

<b>References:</b>
<ul>
<li><p>Cycript</br><a href ="http://www.cycript.org/">http://www.cycript.org/</a></p>
</li>
<li><p>Cycript tricks</br><a href="http://iphonedevwiki.net/index.php/Cycript_Tricks">http://iphonedevwiki.net/index.php/Cycript_Tricks</a></p>
</li>
</ul>

<p>This article was originally published on the <a href="http://resources.infosecinstitute.com/">resources</a> page at <a href="http://infosecinstitute.com/">Infosec Institute</a>. For more information, please visit my author <a href="http://resources.infosecinstitute.com/author/prateek/">page</a>.</p>
