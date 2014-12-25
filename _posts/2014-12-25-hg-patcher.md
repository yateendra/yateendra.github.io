---
layout: post
title: Mercurial Tools
---

Currently, Mozilla maintains a repo of tools to help developers who are new to the mercurial VCS and are more familiar with git. These set of tools help automate a lot of menial tasks and one of them being the ability to apply hg patches into a git repo.

The tool is called [hg-patch-to-git-patch] (https://github.com/simar7/moz-git-tools/blob/master/hg-patch-to-git-patch) and righty so does the job as the title. The tool is pretty handy although to keep track of my commits I use the <br /> <code>--signoff</code> option that git has. Unfortunately mercurial doesn't have the notion of having a signoff in the commits and therefore the tool lacks the same.

{% highlight python %}
if signoff:
    user_name = os.popen('git config user.name').read().strip()
    user_email = os.popen('git config user.email').read().strip()
    print("Signed-off-by: %s <%s>" % (user_name, user_email))
{% endhighlight %}

My patch adds the ability to take the current developers username and email and apply to the newly created patch using the tool. It's simple and helps developers keep track and credit for their commits. 

