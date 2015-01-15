---
layout: post
title: VirtualEnv
---
Lately, I've been doing some py packaging work. This constantly involves testing built packages and re-packaging them in case they weren't packed rightly. Instead of modifying your existing OS installation and software packages, virtualenv provides a light weight process to isolate packages in testing phase.

### What is virtualenv?
In short: A virtualization software that allows a developer to have multiple python installation and system packages across the same system and allows them to co-exist freely.

### Why should I use virtualenv?
If you are testing new packages, or packages that maybe damaged or modify system files. Virtualenv is also handy to isolate venv sessions into tricking them and deriving them of any existing system packages that may be installed.

This behaviour maybe be useful in order to simulate a fresh installation of a pacakge and also helps get around caching mechanism that may otherwise contribute to the success of a package installation.

### How to use virtualenv?
{% highlight c %}
$ virtualenv venv/test_virt
$ source venv/test_virt/bin/activate
{% endhighlight %}

You can also enable the <code>--no-site-packages</code> option to isolate any system level pacakges that may be installed on the host system.

### Useful addons
<code>virtualenvwrapper</code> is a nifty wrapper around virtualenv and can greatly help virtualenv sessions.
