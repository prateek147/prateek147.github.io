---
layout: post
title: "Octopress: Category pages with preview and pagination"
date: 2013-06-30 02:27
comments: true
categories: [octopress]
---


So i have been playing around with Octopress for a few weeks now and i love it. While creating this site, i bumped into a small problem.

<h3>How do i display a category page with a preview of all the blog posts and include pagination in it as well ?</h3>

Ofcourse, if you just want to visit the default category page that Octopress creates, you can just append something like <code>/categories/[category_name]/</code> to the base url. For e.g <a href="/categories/security">here</a> is the link to the posts with the category security. But this is so boring. It would have been cool to have a preview of each post just like the home page, and that also had pagination, something like <a href="/security">this</a> . 


Here is how i did it.

 <!-- more --> 

First of all, create a new page that will serve all the posts for that category. Let's assume that category name is <code>Travel</code>.


``` 
Prateeks-MacBook-Pro:octopress prateekgianchandani$ rake new_page["security"]
mkdir -p source/security
Creating new page: source/security/index.markdown

```

Then go to <code>source/index.html</code> inside your Octopress directory. It would look something like this.

``` html

  {% include_code index.html %}

```

The only change that needs to be done is that instead of iterating over all the pages, we will have to iterate over the posts of a specific category. So basically you replace <code>paginator.posts</code> with <code>site.categories.security</code> where security is the category name. Here is how it will look like.

``` html

{% include_code security.html %}

```

Copy this code inside <code>security/index.markdown</code> file.

Now, your category page will be available at /security :) 

Here is how it looks like on this site http://highaltitudehacks.com/security/

If you have any questions, feel free to ask them in the comments section below.


