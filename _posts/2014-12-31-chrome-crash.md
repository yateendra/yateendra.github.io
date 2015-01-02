---
layout: post
title: Chrome crash analysis
---
I have a habit of having multiple tabs open while working. I rarely shutdown my Mac and most times only end up closing applications like Chrome when I need extensive memory resources at my disposal, e.g. gaming.

Recently with Chrome version 39.0.2171.95 (64-bit) onwards I've started to observe weird crashes. The results include the entire browser crashing without being able to restore last opened tabs when relaunching. The entire browser state gets lost, without a trace to restore.

I decided to look into the Apple crash and diagnostics report this time around. This report gets generated when an app faces a crash and a OS watchdog gets triggered notifying that the app died.

Here's what the log header looks like

{% highlight bash %}
Process:         Google Chrome [305]
Path:            /Applications/Google Chrome.app/Contents/MacOS/Google Chrome
Identifier:      com.google.Chrome
Version:         39.0.2171.95 (2171.95)
Code Type:       X86-64 (Native)
Parent Process:  launchd [273]
Responsible:     Google Chrome [305]
User ID:         501
 
Date/Time:       2014-12-30 21:44:08.611 -0500
OS Version:      Mac OS X 10.9.5 (13F34)
Report Version:  11
Anonymous UUID:  47014693-58B4-4AAE-FA1F-AD4773B7D8E6
 
Sleep/Wake UUID: 40501AA0-DDD7-4AC9-B6D4-475CE1C63931
 
Crashed Thread:  11  Chrome_IOThread
 
Exception Type:  EXC_CRASH (SIGABRT)
Exception Codes: 0x0000000000000000, 0x0000000000000000
 
Application Specific Information:
abort() called
/SourceCache/libpthread/libpthread-53.1.4/src/pthread_mutex.c:pthread_mutex_unlock:700: __p_mutexdrop__ failed with error 16
{% endhighlight bash %}

### Hypothesis    

Okay the logs mention that as a result of the <code>pthread\_mutex\_unlock()</code> call, thread 11 crashed. The first  thing that catches the eye is the error return code: </code>16</code>. On Mac OS X system the error code 16 corresponds to <code>EBUSY</code>.

### Initial stacktrace analysis

{% highlight bash%}
Thread 11 Crashed:: Chrome_IOThread
0   libsystem_kernel.dylib          0x00007fff903f6866 __pthread_kill + 10
1   libsystem_pthread.dylib         0x00007fff8f18835c pthread_kill + 92
2   libsystem_c.dylib               0x00007fff8b978b1a abort + 125
3   libsystem_pthread.dylib         0x00007fff8f18b984 __pthread_abort + 49
4   libsystem_pthread.dylib         0x00007fff8f18ba38 __pthread_abort_reason + 180
5   libsystem_pthread.dylib         0x00007fff8f18a96f pthread_mutex_unlock + 184
{% endhighlight %}

We definitely see the thread getting aborted and then trying to get killed with whatever it was trying to do before the kill signal was received. On line 5, we notice the same <code>pthread\_mutex\_unlock()</code> call, our main suspect here.

The rest of the stack trace seems rather mysterious as to what led to the crash. We see that the thread was clean when started but something happened between line 20 to line 5.
{% highlight bash %}
6   com.google.Chrome.framework     0x000000010b051604 0x10a955000 + 7325188
7   com.google.Chrome.framework     0x000000010b04fd5c 0x10a955000 + 7318876
8   com.google.Chrome.framework     0x000000010b4dec91 0x10a955000 + 12098705
9   com.google.Chrome.framework     0x000000010b4dedf7 0x10a955000 + 12099063
10  com.google.Chrome.framework     0x000000010b4dc17d 0x10a955000 + 12087677
11  com.google.Chrome.framework     0x000000010b4dbeac 0x10a955000 + 12086956
12  com.google.Chrome.framework     0x000000010b4d892b 0x10a955000 + 12073259
13  com.google.Chrome.framework     0x000000010afe2a61 0x10a955000 + 6871649
14  com.google.Chrome.framework     0x000000010b06a655 0x10a955000 + 7427669
15  com.google.Chrome.framework     0x000000010afe2c28 0x10a955000 + 6872104
16  com.google.Chrome.framework     0x000000010b040a17 0x10a955000 + 7256599
17  com.google.Chrome.framework     0x000000010b02ac6d 0x10a955000 + 7167085
18  com.google.Chrome.framework     0x000000010da5b854 0x10a955000 + 51406932
19  com.google.Chrome.framework     0x000000010b059b53 0x10a955000 + 7359315
20  com.google.Chrome.framework     0x000000010b055bbb 0x10a955000 + 7343035
21  libsystem_pthread.dylib         0x00007fff8f187899 _pthread_body + 138
22  libsystem_pthread.dylib         0x00007fff8f18772a _pthread_start + 137
23  libsystem_pthread.dylib         0x00007fff8f18bfc9 thread_start + 13
{% endhighlight %}

Okay, well that does confirm two things right away; the crashing thread in question was a thread responsible for I/O of the app. Memory seems to be the first suspect. Further on we see that on a <code>pthread\_mutex\_unlock()</code> call the code crashed. Seems like an unexisting resource guarded by a lock was being attempted to be freed/unlocked.

### x86 thread-state analysis

The logs also contain an interesting set of register values which the thread in question was responsible for holding at the moment of the crash let's see what they tell us.

{% highlight bash %}
Thread 11 crashed with X86 Thread State (64-bit):
  rax: 0x0000000000000000  rbx: 0x0000000119075000  rcx: 0x0000000119074418  rdx: 0x0000000000000000
  rdi: 0x0000000000008703  rsi: 0x0000000000000006  rbp: 0x0000000119074440  rsp: 0x0000000119074418
   r8: 0x0000000000000040   r9: 0x0000000119074400  r10: 0x0000000008000000  r11: 0x0000000000000206
  r12: 0x00006080008184c0  r13: 0x00006080006bc010  r14: 0x0000000000000006  r15: 0x0000000000000000
  rip: 0x00007fff903f6866  rfl: 0x0000000000000206  cr2: 0x0000000112ac7068
  
Logical CPU:     0
Error Code:      0x02000148
Trap Number:     133
{% endhighlight %}

Apple has published a [Technical Note TN2123] (https://developer.apple.com/library/mac/technotes/tn2004/tn2123.html) which describes what the CrashReporter reports with regards to the x86 thread-state register values.

There are two main values in question here that can confirm us the type of crash which would prove if our hypothesis of having a bad memory access was correct or not. Before that its useful to note that we are primarly looking at the <code>Program Counter [PC]</code> which on x86-64bit platforms is represented by the <code>rip</code> register. The <code>rip</code> register points to the instruction being currently executed, in this case the instruction at the time of crash.

The two cases that are formed are as follows:

* For Arithmetic exceptions:    
The <code>rip</code> register holds the value of the of the instruction that caused the arithmetic exception. In our case we know it wasn't a arithmetic exception as indicated by the type of crash earlier from the logs.

* For badmemory exceptions:    
If <code>rip</code> holds the same value as indicated from the exception address, then it means that we are fetching a bogus function pointer or making a call on a bogus object. It could also mean that we are returning to a bad address in memory and now the stack is corrupt. In our case this is not the case as we have the following:

{% highlight bash %}
Exception Type:  EXC_CRASH (SIGABRT)
Exception Codes: 0x0000000000000000, 0x0000000000000000
 ...snip...
rip: 0x00007fff903f6866 
{% endhighlight %}

<code>rip</code> is storing a non-zero value and we don't have a arithmetic based crash. This means the only possibility we have left is the following: We dereferenced an invalid pointer.

### ..but why?

If you look closely the stack trace it's hard to believe what happened.

{% highlight bash %}
0   libsystem_kernel.dylib          0x00007fff903f6866 __pthread_kill + 10
1   libsystem_pthread.dylib         0x00007fff8f18835c pthread_kill + 92
2   libsystem_c.dylib               0x00007fff8b978b1a abort + 125
3   libsystem_pthread.dylib         0x00007fff8f18b984 __pthread_abort + 49
4   libsystem_pthread.dylib         0x00007fff8f18ba38 __pthread_abort_reason + 180
5   libsystem_pthread.dylib         0x00007fff8f18a96f pthread_mutex_unlock + 184
..snip..
21  libsystem_pthread.dylib         0x00007fff8f187899 _pthread_body + 138
22  libsystem_pthread.dylib         0x00007fff8f18772a _pthread_start + 137
23  libsystem_pthread.dylib         0x00007fff8f18bfc9 thread_start + 13
{% endhighlight %}

Line <code>5</code> indicates that <code>pthread\_mutex\_unlock()</code> failed. But we don't see a corresponding <code>pthread\_mutex\_lock()</code> call anywhere on the stacktrace. Really odd that an <code>unlock</code> call would fail out so badly without even a corresponding <code>lock</code> being held in place.

### Need moar code

Seems like the question still hasn't been answered as to why the crash happened with an <code>unlock</code> call in the first place. Let's look at the actual <code>pthread\_mutex\_unlock()</code> to see how it's implemented.    
<br />Interestingly enough, Apple still provides an open sourced codebase for some of it's OS code. The following snippet was taken from [Apple Opensource](http://opensource.apple.com/source/Libc/Libc-825.40.1/pthreads/pthread_mutex.c)
website.

{% highlight c linenos %}
/*
 * Unlock a mutex.
 * TODO: Priority inheritance stuff
 */
int
pthread_mutex_unlock(pthread_mutex_t *omutex)
{
    npthread_mutex_t * mutex = (npthread_mutex_t *)omutex;
    int retval;
    uint32_t mtxgen, mtxugen, flags, notify, updateval;
    int sig = mutex->sig; 
    pthread_t self;
    uint64_t selfid;
    volatile uint32_t * lseqaddr, *useqaddr;
    int firstfit = 0;

...snip...

        if ((updateval = __psynch_mutexdrop(omutex, mtxgen, mtxugen, mutex->m_tid, flags))== (uint32_t)-1) 
#endif /* USE_COMPAGE ] */
        {
            retval = errno;
#if _KSYN_TRACE_
    (void)__kdebug_trace(_KSYN_TRACE_UM_UNLOCK | DBG_FUNC_END, (uint32_t)mutex, retval, 0, 0, 0);
#endif
            if (retval == 0)
                return(0);
            else if (errno == EINTR)
                return(0);
            else {
                LIBC_ABORT("__p_mutexdrop failed with error %d\n", retval);
                return(retval);
            }

{% endhighlight %}

Aha! Okay we did find the branch of code that is being taken. We have a nonzero errno and its not EINTR. But who is causing this mess?

### The real culprit
I browsed around more of <code>pthread_cond.c</code> and found this. 

{% highlight c linenos %}
int
pthread_cond_destroy(pthread_cond_t *ocond)
{
    ...snip...
        // <rdar://problem/13782056> Need to clear preposts.
        uint32_t flags = 0;
        bool needclearpre = ((scntval & PTH_RWS_CV_PBIT) != 0);
        if (needclearpre && cond->pshared == PTHREAD_PROCESS_SHARED) {
            flags |= _PTHREAD_MTX_OPT_PSHARED;
        }
{% endhighlight %}

Yikes. That seems to be like a internal PR link to a bug in the source code. I can't access it from the outside world but I could imagine while running <code>pthread\_cond\_destroy()</code> from somewhere within a <code>pthread\_mutex\_unlock()</code> what could possible go wrong.    
<br />

In general any generic subsystem library consists of a give architecture: A userspace component for userland level calls to use and a corresponding system or a kernel level component for the conversion of calls from userspace to kernelspace. Here's a [good read] (http://sysdigcloud.com/fascinating-world-linux-system-calls/) to see the mentioned things in action.    
<br />

With that in mind, here's a possible scenario. When using a <code>pthread</code> subsystem to create a condition variable; two entries are created: a userspace address and a kernel space address for the OS to use. This is by design as discussed previously as to how the susbsytem calls maintain an interface between the userlevel and the kernellevel.    
<br />

The problem could very well arise if the following was to take place: The userspace call cleaned up the userspace memory address and free'd it after using it **but** couldn't free the kernel level memory address (for whatever reason). We now have a dangling reference that is not yet marked to be free and has no pointer pointing to it.    
<br />

### Conclusion
This dangling reference is clearly bad and is a possible cause of what has happened in this case. The dangling reference at some point was used by a call in <code>Thread 11</code> within the Chrome browser and led to a series of events which ended up generating a nonzero <code>errno</code> from the <code>pthread\_mutex\_unlock()</code> which was further handled by the thread as a <code>SIGABRT</code> and led to the thread getting killed and subsequently it being an <code>IO</code> level thread the entire browser getting crashed.    
<br />

### Who is to blame?
I didn't expect this blog post to go the way it did. To me it initally sounded like a bad memory access within chrome that led to the OS killing it but in reality the problem lied within the OS level API <code>pthread_subsystem</code> itself.    
<br />

This again might not be the real cause of the entire crash in the first place. The crash happened on Mac OS X 10.9 and the API calls that are open sourced are from OS X 10.8. It could very well be some other system level call failing due to other reasons but in either case the problem exists in the OS APIs and Chrome itself cannot be blamed.

### Reproducibility

Very low to none, I would say. Although I did find some more evidence on the internet that is along similar lines. [Here's] (http://rayne3d.com/blog/02-27-2014-rayne-weekly-devblog-4) a writeup that also seems to have a workaround for the same problem.

