---
layout: post
title: "IOS Application Security Part 12 – Dumping Keychain Data"
date: 2013-08-20 07:32
comments: true
categories: [security]
---


<p>In the previous article, we looked at the different ways in which we could analyze the network traffic and the api calls being made through an IOS application. In this article, we will look at how we can dump the contents of the Keychain from an IOS device.</p>

<h2>Keychain Basics</h2>

<p>According to Apple, a Keychain in an IOS device is a secure storage container that can be used to store sensitive infromation like usernames, passwords,network passwords, authentication tokens for different applications. Apple itself uses the Keychain to store Wi-fi network passwords, VPN credentials etc. It's a sqlite database file located at <i>/private/var/Keychains/keychain-2.db</i> and all the data stored in it is encrypted. Developers usually want to leverage this feature of the operating system to store credentials rather than storing it themseleves in NSUserDefaults, plist files etc. The reason for this could be that the developer may not want the user to log in everytime and hence store the authentication information somewhere in the device and use it to log in the user automatically whenver the user opens up the app again. The keychain information for every app is stored outside of its sandbox.</p>

<!-- more -->

<p>It is also possible to share keychain data between applications through keychain access groups. This group has to be specified while saving the data in the keychain. The best way of saving data in Keychain is to use the <i>KeychainItemWrapper</i> class by Apple. A sample project can be found <a href="http://developer.apple.com/library/ios/#samplecode/GenericKeychain/Listings/Classes_KeychainItemWrapper_h.html">here</a>. The first step is to create an instance of the class.</p>

<code> KeychainItemWrapper *wrapper = [[KeychainItemWrapper alloc] initWithIdentifier:@"Password" accessGroup:nil];</code>

<p>The identifier will help in fetching this information from the keychain later. In case you have to share information across applications, then you will have to specify an access group. Applications with the same access group can have access to the same keychain information.</p>

<code>KeychainItemWrapper *wrapper = [[KeychainItemWrapper alloc] initWithIdentifier:@"Account Number" accessGroup:@"YOUR_APP_ID_HERE.com.yourcompany.GenericKeychainSuite"];</code>

<p>To save info in the keychain, just use the setObject:forKey: method. In this case <i>(id)kSecAttrAccount</i> is a predefined key that we can use to define the account name for which we are storing the data. The <i>kSecClass</i> specifies the kind of information we are storing, in this case a generic password.The <i>kSecValueData</i> key can be used to store any form of data, in this case a password. </p>
	
<pre>
	[keychainItemWrapper setObject:kSecClassGenericPassword forKey:(id)kSecClass];
	[wrapper setObject:@"username" forKey:(id)kSecAttrAccount];
	[keychainItemWrapper setObject:@"password"forKey:(id)kSecValueData];
	[wrapper setObject:(id)kSecAttrAccessibleAlwaysThisDeviceOnly forKey:(id)kSecAttrAccessible];

</pre>
	

<p>The <i>kSecAttrAccessible</i> variable is used to specify when does the application need access to that data. We should be careful while using this option and use the most restrictive option. There are six possible values for this key which can be found in the screenshot below from Apple's <a href="http://developer.apple.com/library/mac/#documentation/Security/Conceptual/keychainServConcepts/iPhoneTasks/iPhoneTasks.html#//apple_ref/doc/uid/TP30000897-CH208-SW1">documentation</a>.</p>


<img src="/images/posts/ios12/1.png" width="1188" height="655" alt="1">

<p>Ofcourse, we should never use <i>kSecAttrAccessibleAlways</i>. A safe option to go for would be <i>kSecAttrAccessibleWhenUnlocked</i>. Also, there are options that end with <i>ThisDeviceOnly</i>. If this option is chosen, the data is encrypted with a hardware specific key and hence could not be transferred to or viewed from another device. Even though they enforce additional security, it might not be a good idea to use them unless you have a good reason to not allow the migration of data between backups.</p>

<p>To fetch information from the keychain, use this.</p>
	
<code>NSString *accountName = [wrapper objectForKey:(id)kSecAttrAccount];</code>

<h2>Analyzing Keychain read-write using Snoop-it</h2>

<p>One of the great tools to analyze the data being written to the keychain is Snoop-it. If you haven't seen it yet, check out <a href="http://resources.infosecinstitute.com/ios-application-security-part-9-analyzing-security-of-ios-applications-using-snoop-it/">this</a> tutorial in the same series on Snoop-it. Now run the project <a href="http://developer.apple.com/library/ios/#samplecode/GenericKeychain/Introduction/Intro.html">GenericKeychain</a>  on your jailbroken device and analyze it using Snoop-it. There is already a tutorial for running applications with self-signed certificates in this series which could be found <a href="http://resources.infosecinstitute.com/ios-application-security-part-7-installing-and-running-custom-applications-on-device-without-a-registered-developer-account/">here</a>. This app is just a sample project to demonstrate how to read and write from Keychain using a Obective-C wrapper class KeychainWrapper. Make sure this app is being analyzed using Snoop-it. Now open the app and this is the interface that we see. </p>

<img src="/images/posts/ios12/q.PNG" width="320" height="480" alt="Q">

<p>Now enter some username and password and save it. In this case, lets enter the username as "Test User" and the password as "password". You will see that Snoop-it detects any change to the keychain and is able to tell us the information stored in the keychain. It also tells us the protection attributes for the saved information.</p>


<img src="/images/posts/ios12/r.png" width="1216" height="477" alt="R">

<p>Here is the info that gives away the username and password.</p>

<img src="/images/posts/ios12/s.png" width="670" height="22" alt="S">

<p>Now, enter an account number. Snoop-it is able to detect that as well.</p>

<img src="/images/posts/ios12/t.png" width="1210" height="327" alt="T">

<p> As you can clearly note, Snoop-it is able to detect the action on the keychain database file as well, i.e whether it is a read, write or a delete. If you reset the contents of the keychain by tapping on Reset, you will see that it detects that as well.</p>
<img src="/images/posts/ios12/g.png" width="1186" height="51" alt="G">

<h2>Dumping Keychain data using Keychain Dumper</h2>

<p>One of the most popular tools for dumping information from the keychain is Keychain dumper by ptoomey3. The github page for this project can be found at this link https://github.com/ptoomey3/Keychain-Dumper. Just go to this url and download the zip file and unzip it. Inside this folder, the only file that we are interested is the <i>keychain_dumper</i> binary. The information that is allowed to be accessed by an application in the keychain is specified in its entitlements. This binary is signed with a self signed certificate with <i>wildcard</i> entitlements and hence it is able to access all the keychain items. There could also have been other ways to make sure all the keychain information is granted, like having the entitlements file contain all the keychain access groups or using a specific keychain access group that provides access to all the keychain data. For e.g a tool <a href="https://code.google.com/p/iphone-dataprotection/wiki/KeychainViewer">Keychain-viewer</a> uses the following entitlments.</p>


<pre>
	<key>com.apple.keystore.access-keychain-keys</key>
	<true/>
	<key>com.apple.keystore.device</key>
	<true/>
</pre>


<p> Just upload this binary into your device in the /tmp folder and make sure its executable.</p>

<img src="/images/posts/ios12/x.png" width="690" height="231" alt="X">

<p>Now make sure that the keychain database file stored at the location <i>/private/var/Keychains/keychain-2.db</i> is world readable.</p>

<img src="/images/posts/ios12/y.png" width="574" height="59" alt="Y">

<p>Now run the binary.</p>

<img src="/images/posts/ios12/3.png" width="646" height="542" alt="3">


<img src="/images/posts/ios12/3x.png" width="694" height="548" alt="3x">

<p>As you can see, it dumps all the keychain information. You can see a lot of usernames and passwords stored here. For e.g, we can see that the Mail app stores the username/password of your account in the keychain. Similarly, it is possible to find passwords of some wireless networks that you have previously connected to and much more information. By default, the command above will only dump out the generic and internet passwords. You can see the usage information by using the "-h" command.</p>

<img src="/images/posts/ios12/4.png" width="809" height="276" alt="4">

<p>You can dump all the information using the "-a" command.</p>

<img src="/images/posts/ios12/5.png" width="679" height="475" alt="5">
	
<p>One of the things that could make the information in the keychain more secure is using a strong passcode. This is because the passcode is used to encrypt the data in the keychain for specific protection attributes and hence having a strong passcode would enforce better security. IOS by default allows for a 4 digit passcode which could range from 0-9999 and hence could be bruteforced in a few minutes. We will look at bruteforcing passcodes later in this series. But there is also another option to set alphanumeric passwords which would take significantly more time to be  bruteforced. A combination of a proper protection attribute combined with a strong passcode will help in keeping the data in the keychain much more difficult to be fetched out.</p>

<b>Conclusion</b>

<p>In this article, we looked at how easy it is to dump information from the Keychain of an IOS device. Even though keychain is a much better place of storing credentials and sensitive information than something like NSUserDefaults or plist files, it is however not difficult to break into it either.</p>

<b>References</b>
<ul>
<li><p>Keychain-Dumper</br><a href ="https://github.com/ptoomey3/Keychain-Dumper">https://github.com/ptoomey3/Keychain-Dumper</a></p>
</li>
</ul>