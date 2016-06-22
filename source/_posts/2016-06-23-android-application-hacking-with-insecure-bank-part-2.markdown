---
layout: post
title: "Android Application hacking with Insecure Bank Part 2"
date: 2015-03-23 00:16
comments: true
categories: [security]
---

<p>In the previous article, we looked at setting up a mobile pentesting platform for Android applications. By now, you must have set up an emulator using genymotion and installed all the android command line tools along with some other additonal tools (drozer, dex2jar, apktool). In this article, we will look at some information gathering techniques. We will see how we can decompile an application to its java source, analyze the signature of the application and many more things.</p>

<p>At this point, i would also like to mention that if you are looking for a VM that contains all the tools to cater to your android application pentesting needs, have a look at <a href="https://androidtamer.com/">Android Tamer</a>.</p>

<!-- more -->

<p>First of all, make sure you have the latest version of InsecureBankv2 on your system. You can do a <i>git pull</i> to merge all the latest changes to your master branch.</p>


<img src="/images/posts/ib2//1.png" width="649" height="422" alt="1">

<p>Once this is done, let's do some analysis on the apk file. Copy the apk file into a seperate folder for some analysis. Just like an iOS ipa file, an apk file is a compressed file, so you can decompress it by just changing the extension from apk to zip and then extracting it.</p>

<img src="/images/posts/ib2//2.png" width="873" height="378" alt="2">

<p>Now browse over to the extracted folder and have a look. You can see a lot of files here.</p>

<img src="/images/posts/ib2//3.png" width="592" height="130" alt="3">

<p>Let's describe them one by one.</p>

<ul>
	<li>AndroidManifest.xml - This is probably by far the most important source of information. From a security point of view, it contains information about the various components used in an application and lists the conditions in which they can be launched. It also displays information about the permissiosns that the application uses. I would highly recommend you to go through Google's <a href="http://developer.android.com/guide/topics/manifest/manifest-intro.html">documentation</a> on the manifest file. We will discuss each component of an android application as we discuss vulnerabilities in them.</li>
	
	<li>assets - This is used to store raw assets file. The files stored here as compiled as is into the apk file.</li>
	
	<li>res - Used to store resources such as /images/posts/ib2/, layout files, and string values.</li>
	
	<li>META-INF - Contains important information about the signature and the person who signed the application.</li>
	
	<li>classes.dex - This is where the compiled application code lies. To decompile an application, you need to convert the dex file to a jar file which can then be read by a java decompiler</li>
	
	
</ul>

<p>The information about the public key certificate is stored in the CERT.RSA file in the META-INF folder. To find out information about the public key certificate, use the command <i> keytool –printcert –file META-INF/CERT.RSA </i></p>

<img src="/images/posts/ib2//z.png" width="730" height="426" alt="Z">
	
<p>Please note that it is also possible to modify the code of an apk file after decompiling and then recompile it to deploy to a device. However, once the application code is modified, it loses its integrity and hence needs to be resigned with a new public/private key pair. I would recommend that you have a look at <a href="http://developer.android.com/tools/publishing/app-signing.html">this</a> article that explains how to create your own public/private key pair. We will look at modifying application logic and then recompiling it in later articles in this series.</p>

<p>Once an application has been recompiled, you can verify its integrity using the jarsigner application.</p>

<img src="/images/posts/ib2//j.png" width="760" height="64" alt="J">

<p>Now let's decompile the application using dex2jar. dex2jar can also take input as an apk file (rather than .dex file) and converts it into a jar file.</p>

<img src="/images/posts/ib2//x.png" width="654" height="87" alt="X">

<p>Once this is done, you can simple open this file in JD-GUI and have a look at the source code.</p>

<img src="/images/posts/ib2//y.png" width="1026" height="621" alt="Y">

<p>We can now scan through the source code to find potential vulnerabilities in the application. We can clearly note how easy it is to reverse engineer an apk file and look at the source code. It is important to note here that we are able to see the source code and understand it mainly because there is no code obfuscation applied in the application. Google provides tools like Proguard to help in obfuscating code. While this is not foolproof, there is also a commercial version of Proguard knows an DexGuard that works even better in applying code obfuscation. We will look at obfuscating application code in later articles.</p>

<p>In this article, we looked at how we can extract information from an apk file. In the next article, we will start looking at the different types of vulnerabilities demonstrated in InsecurBankv2.</p>

