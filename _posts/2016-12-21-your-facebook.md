---
published: true
title: How To Unfollow All Friends On Facebook At Once
layout: post
---

Hello everyone! You automatically follow people who you're friends with. If you want to Unfollow all friends at once, this trick will surely work for you. This trick works only on desktop view of Facebook.

Here are the steps to follow -

Step #1 From following navigation options select  'News Feed Preferences'

![]({{site.baseurl}}/http://4.bp.blogspot.com/-IZ8S8L2Jw6g/VH64s-XO1dI/AAAAAAAAB-k/o47aTBdMB_4/s1600/Capture.JPG)

Step #2 Select People under Feed Preference popup. Friends name will appear, scroll down to load all friend names. You are currently following all friends as you can see in picture -

![]({{site.baseurl}}/http://3.bp.blogspot.com/-KyEXUa0-uqk/VH65XZAP6tI/AAAAAAAAB-s/xD9yxHWud84/s1600/Capture2.JPG)

Step #3  Right click mouse and select 'Inspect element ' tool. Under this click on 'Console'. Now paste following javascript code in it, and press Enter.

{% highlight html %}

var inputs = document.getElementsByClassName('_42ft _4jy0 _4jy3 _517h _51sy'); 
for(var i=0; i<inputs.length;i++) 
  { 
  inputs[i].click(); 
  }

{% endhighlight %}

![]({{site.baseurl}}/http://4.bp.blogspot.com/-5kUvxcN96hc/VH66suz3MGI/AAAAAAAAB-8/abrCbtKDkVc/s1600/Capture4.JPG)

Step #4 Done ! all your friends will be Unfollowed within just a few seconds as you can see -

![]({{site.baseurl}}/http://4.bp.blogspot.com/-o9fhZCTgEpo/VH66sLOZdyI/AAAAAAAAB-4/7Yu5lTqP4Tg/s1600/Capture3.JPG)

If you go through friend list and unfollow selectively it will take much more time, but if you you want to follow all friends again then repeat the same procedure it will take a few seconds. In similar way you can unfollow all groups and pages. 

How it works?

Browser follows the given javascript's for loop and clicks all the follow button which are having same class names.

Thanks!
Hope you liked this post, share and make it reach more people !
