---
layout: post
title: 0xbadpoint
---

Sometimes you just have a bad time and all hell breaks loose.

So consider a structure that has the following fields:

{% highlight c %}
typedef struct MY_TYPE {
    uint8_t id;
    uint8_t length;
    boolean flag;
} EventQ;
{% endhighlight %}

and now let's create a pointer to this:

{% highlight c %}
MY_TYPE* my_ptr;
{% endhighlight %}

Let's use it regularly as we've done whatever is needed to use it.

{% highlight c %}

void setStuffUp() {
    memcpy(my_ptr->id, "0", sizeof(uint8_t));
    memcpy(my_ptr->length, "0", sizeof(uint8_t));
    my_ptr->flag = true;
}

{% endhighlight %}

All good so far, nothing bad.    
<br />
Well not really, I forgot to initialize them. Whoops.

### How was this discovered?

Fortunately this was part of a security routine and I had random calls going on to the stack area that was part of the security routine. No one else was touching it so going through all the other pointers made it clear who was the culprit.

### The fix?
{% highlight c %}
MY_TYPE my_event = {0};
MY_TYPE* my_ptr = &my_event;
{% endhighlight %}

Simple, but not on a long day.

### Bonus level
So as pointed out, I've come to know there are two ways to fix the above.
* Setting all fields manually to <code>zero/NULL</code>.
* Doing a <code>memset()</code>

The second approach only has one caveat. Consider the following code:

{% highlight c %}
typedef struct MY_TYPE {
    uint8_t id;
    uint8_t length;
    boolean flag;
    int* ptr_to_an_int_arr;
} EventQ;
{% endhighlight %}

Now if the ptr_to_an_int_arr was actually pointing to an integer array, doing a <code>memset()</code> on a <code>sizeof()</code> would end us up with a pointer lost to the integer array. And even worse still we would end up modifying the address that was being previously held by the structure and would now be pointing to a random location in memory, which is equally if not more dangerous than having no initialization.
