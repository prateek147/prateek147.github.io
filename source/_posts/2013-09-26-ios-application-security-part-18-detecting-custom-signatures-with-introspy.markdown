---
layout: post
title: "iOS Application Security Part 18 – Detecting custom signatures with Introspy"
date: 2013-09-26 17:18
comments: true
categories: [security]
---

<p>In the previous article, we looked at how we can use Introspy for Black-box assessment of iOS applications. In this article, we will look at how we can use Introspy to set up our own custom signatures and detect them in an application trace. Setting up our own predefined signatures could be useful for cases where you have a found a method in a particular application that seems of particular interest to you and you want to know when it is being called. Introspy already has a list of predefined signatures that it uses to flag vulnerabilities or insecure configurations. However, it also allows us to add our own signatures.</p>

<p>You can find the predefined signatures in Introspy in the signatures.py file inside the analyzer folder. </p>

<!-- more -->

<img src="/images/posts/ios18/1.png" width="876" height="586" alt="1">

<p>From here, we can see that a signatures consists of a title, description, a severity level and a filter which consists of the method calls that correspond to the signature. So let's look at a sample signature.</p> 
<img src="/images/posts/ios18/2.png" width="651" height="199" alt="2">


<p>Over here, you can see a signature that checks whether the application uses Pasteboards or not. Pasteboards are generally very insecure as they can allow an application to copy some data from the Pasteboard into their application. Hence this signature makes sense. You can see that the filter consists of two values, <i>classes_to_match</i> and <i>methods_to_match</i>. You can also specify a parameter <i>args_to_match</i> in your signature. So from this signature, it is pretty much clear that these following method implementations will match the above signature.</p>

<ul>
	<li>UIPasteboard *pasteboard = [UIPasteboard generalPasteboard];</li>
	<li>UIPasteboard *pasteboard = [UIPasteboard pasteboardWithName:@"XYZ" create:YES];</li>
	<li>UIPasteboard *pasteboard = [UIPasteboard pasteboardWithUniqueName];</li>
</ul> 

<p>Another signature shown in the image below checks for methods that bypass credential validation while connecting to a remote server. This happens in cases where you are using a self singed SSL certificate and would like to trust it everytime without any kind of validation.</p>

<img src="/images/posts/ios18/3.png" width="796" height="147" alt="3">

<p>For any LibC signature, just set the <i>classes_to_match</i> attribute as <i>C</i>.</p>

<img src="/images/posts/ios18/4.png" width="610" height="156" alt="4">

<p>Now lets understand a signature that has some arguments also with it as a filter. The filter can be defined using 3 classes which can be found in the file Filters.py. These classes are <i>ArgumentsFilter</i>, <i>ArgumentsNotSetFilter</i> or <i>ArgumentsWithMaskFilter</i>. Here are some screenshots from the code itself that define the purpose of these classes.</p>

<img src="/images/posts/ios18/8.png" width="689" height="94" alt="8">
<img src="/images/posts/ios18/9.png" width="678" height="245" alt="9">
<img src="/images/posts/ios18/10.png" width="715" height="132" alt="10">

<p>Here is a signature written in the Signatures.py file that detects the scenario when some data is written to the keychain without a secure protection domain (pdmn). As you can see, both the <i>ArgumentsFilter</i> and <i>ArgumentsNotSetFilter</i> filters have been used to detect signaturres. The <i>ArgumentsFilter</i> signature is used to find pdmn's that are insecure whereas <i>ArgumentsNotSetFilter</i> is used to find cases where no accessibility option is provided and hence defaults to kSecAttrAccessibleAlways.</p>

<img src="/images/posts/ios18/11.png" width="1082" height="562" alt="11">

<p>Now lets add a custom signature to the signature.py file. In this case, we are checking for the case whenever someone gets a string saved in NSUserDefaults.</p>

<img src="/images/posts/ios18/5.png" width="821" height="163" alt="5">

<p>Now run the python script introspy.py file on the saved database file.</p>

<img src="/images/posts/ios18/6.png" width="764" height="21" alt="6">

<p>In the report under Potential Findings, you will see that our signature was identified in many different places.</p>

<img src="/images/posts/ios18/7.png" width="829" height="692" alt="7">

<b>Conclusion</b>

<p>In this article, we looked at how we can use Introspy to set up our own custom signatures and detect them in an application. Using these custom signatures when performing static analysis of these applications can be very useful if you are looking to track some specific method implementations.</p>

<b>References</b>
<ul>
<li><p>Introspy</br><a href ="https://github.com/iSECPartners/introspy">https://github.com/iSECPartners/introspy</a></p>
</li>
</ul>

</body>
</html>

