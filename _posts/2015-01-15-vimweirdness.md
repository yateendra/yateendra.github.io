---
layout: post
title: Weird Vim
---
Just like any other day I fired up vim to edit a file. But I was shown this instead.

{% highlight c %}
Error detected while processing function <SNR>81_MRU_LoadList:
line    5:
E684: list index out of range: 0
E15: Invalid expression: s:MRU_files[0] =~# '^\s*" Most recently edited files in Vim'
Press ENTER or type command to continue
{% endhighlight %}

Turns out that I was running low on disk space and as a result the installed vim plugin (MRU.vim) in this case that manages [Most Recently Used files] (http://www.vim.org/scripts/script.php?script_id=521) inside of vim was failing. [Here's](http://stackoverflow.com/questions/15397567/vim-error-detected-while-processing-function-snr37-mru-loadlist) more on the related issue on StackOverflow.
