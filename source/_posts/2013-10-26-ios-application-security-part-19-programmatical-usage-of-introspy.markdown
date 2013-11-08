---
layout: post
title: "IOS Application Security Part 19 – Programmatical Usage of Introspy"
date: 2013-10-26 23:38
comments: true
categories: [security]
---

<p>In this article, we will look at how we can Introspy as a python module in our scripts.</p>

<p>The first thing to do is to import the introspy module and Namespace from argparse module.</p>

<img src="/images/posts/ios19/1.png" width="555" height="140" alt="1">

<p>We then create an instance of the Introspy class. The arguments that we need to provide are the database name, 
the group name, the subgroup name and the list. Now, for this case, lets provide all the parameters as None except the database path. Introspy will hence include all the groups rather than just including a particular group.</p>

<!-- more -->

<img src="/images/posts/ios19/2.png" width="920" height="89" alt="2">

<p>Let's create an object with a group and a subgroup. Now even though i couldn't find the different categories of groups and subgroups anywhere in the documentation, but from the HTML reports for Introspy that we generated in the previous articles, i was able to find a list of groups and subgroups. If you look at the image below, all the main menu items can be used as group names whereas all the submenu items can be used as subgroup names.</p>

<img src="/images/posts/ios19/3.png" width="850" height="87" alt="3">

<p>So for example, the group names can be DataStorage, Crypto, Network, IPC etc and the subgroup name for the group Network can be HTTP. So let's create an Instrospy object with the group Network and the subgroup HTTP.</p>
<img src="/images/posts/ios19/4.png" width="938" height="101" alt="4">


<p>Let's look at all the methods and attributes available to the introspy object.</p>

<img src="/images/posts/ios19/5.png" width="515" height="42" alt="5">

<p>As we can see the Introspy object has an analyzer attribute. Let's look at all the methods and attributes for it.</p>

<img src="/images/posts/ios19/6.png" width="943" height="151" alt="6">

<p>You can see all the findings by either using the findings attribute or using the get_findings method.</p>

<img src="/images/posts/ios19/7.png" width="1423" height="250" alt="7">
	
<p>We can also look at all the signatures for the analyzer by using the signatures attribute.</p>
	
<img src="/images/posts/ios19/8.png" width="1423" height="118" alt="8">

<p>However, these are just instances of the signature object. Let's try and get some meaningful information from these signatures. If we look at the signatures.py file, we will see that every Signature instance has attributes like title, description, filter etc.</p> 

<img src="/images/posts/ios19/9.png" width="569" height="197" alt="9">
<p>So let's iterate through every Signature object and print out its title and description.</p>

<img src="/images/posts/ios19/10.png" width="1419" height="413" alt="10">

<p>As you can see, we can pretty much find out whatever we want from these instances. In the next case, i have printed out all the class and the methods combinations from the filters for every signature. Please note that this may not be the exact method implementation used by the filter because the method can be a class or an instance method. For e.g, as shown in the output, there is no method like <i>[NSUserDefaults stringForKey:]</i> but there is a instance method <i>[[NSUserDefaults standardUserDefaults] stringForKey:]</i></p>

<img src="/images/posts/ios19/11.png" width="1064" height="613" alt="11">

<p>Every analyzer object has an attribute named tracedCalls which is a list of all the calls traced during runtime. If we look at the code for tracedCalls class, we will see that it has attributes like callId, method, clazz etc.</p>

<img src="/images/posts/ios19/12.png" width="914" height="185" alt="12">

<p>We can analyze whatever information we want from any tracedCall. In the image below, i have printed out the attributes for the first traced call.</p>


<img src="/images/posts/ios19/13.png" width="1417" height="494" alt="13">


<b>Conclusion</b>

<p>In this article, we looked at how we can import the introspy module in our own python scripts and fetch different kinds of information like signatures, tracedCalls etc from a generated database file and present them in whichever way we want. This could be used in preparing our own custom reports for the analysis of a particular IOS application. This information can also be fed into other python scripts that can be able to perform further tasks like runtime manipulation etc.</p>

<b>References</b>

 <ul>
 <li><p>Introspy</br><a href ="https://github.com/iSECPartners/introspy">https://github.com/iSECPartners/introspy</a></p>
 </li>
 </ul>

	</body>
</html>


