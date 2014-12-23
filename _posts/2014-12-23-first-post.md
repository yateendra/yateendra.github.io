---
layout: post
title: First post 
---

So I've decided to stick on with GitHub pages for blogging purposes. It's fast and easy to maintain. However have made the following changes:

### Switched over from simple [poole] (http://demo.getpoole.com/) to [lanyon] (http://lanyon.getpoole.com/).

simple poole was good for a landing page but wasn't appropriate for a blog type of webpage. Also no one wants to see a post right in their face. title-only landing pages are better.

### New theme @ [commit b72095] (https://github.com/simar7/simar7.github.io/commit/b720956991d2ef39ea574a9f30263985c35054ba)

removed a lot of useless and heavy colors to make the theme minimal and light. Interestingly, the color I currently have was a random key sequence **#fff4942**
 
{% highlight css %}

.theme-base-07 .sidebar {
 color: #9a9a9a;
 background-color: #fff;
}

.theme-base-07 .sidebar-nav-item {
  color: #9a9a9a;
  background-color: #fff;
}

.theme-base-07 .sidebar-nav-item.active {
  color: #ff4942;
  background-color: #fff;
}

.theme-base-07 #sidebar-checkbox:checked ~ .sidebar-toggle {
  background-color: #ff4942;
}

.theme-base-07 .container a,
.theme-base-07 .sidebar-toggle,
.theme-base-07 .sidebar-toggle:active,
.theme-base-07 .related-posts li a:hover {
  color: #ff4942;
}

{% endhighlight %}

### Acquired [git.io/simar] (http://git.io/simar).

woot.

### Source
As always, the source code is openly available [here] (https://github.com/simar7/simar7.github.io).
