---
layout: post
title: Resolve R 
---

So far the most annoying Android Studio bug I've encountered is the following:

<code>
cannot resolve symbol R
</code>

This is really frustrating and sometimes can take from anywhere ten minutes to a few hours to fix it. Fortunately [this](http://www.techrepublic.com/blog/software-engineer/a-comprehensive-troubleshooting-guide-for-androids-r-cannot-be-resolved-error/) blog post details many different ways to to deal with it. I'll briefly summarize the points mentioned below:

* Clean your project often.
* Disable auto-imports within Eclipse/Android Studio.
* Check /res dir for malformed XML
* Double check other XML files (including AndroidManifest.xml) for any errors.
* Ensure the SDK versions are correctly set.

### Credits
Thanks to [William Francis] (https://plus.google.com/112891506225324205445) for contributing to this post.
