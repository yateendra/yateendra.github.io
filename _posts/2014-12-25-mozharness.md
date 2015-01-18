---
layout: post
title: Mozharness
tags: [mozilla]
---

In the recent past most of my work has involved writing code in python. For the most part, I haven't been working on code bases that large to worry about writing a full blown test harness to manage it. But recently working on a network simulator, me and my partner [@clouisa] (https://github.com/clouisa) were faced a problem: how to debug python code that is non-deterministic in nature. 

Following the project guidelines, the code was supposed to be a random, unifomly distributed, sequence of events that could depending on their values could take a range of value which would result in a sequence of different code branches being taken. For example consider the following code:

{% highlight python %}
def next_gen_time(current_tick):
    gen_number = random.random()
    gen_time = (-1.0 / ARRIVAL_RATE) * math.log(1 - gen_number)
    gen_tick = math.ceil(gen_time / TICK_DURATION)
    return int(gen_tick + current_tick)
{% endhighlight %}

This is an example of a [Markovian Distribution](http://en.wikipedia.org/wiki/Markovian_arrival_process).

### What's Mozharness?

Mozharness is a python test harness that serves a purpose two-fold. It is basically a set of scripts that can be used by both the author of the code and tester alike. This eliminates the need for a separate test specific teams as the developers themselves are capable of running full tests locally on their dev machines.

Currently **mozharness** is used in the Mozilla community to test the nightly releases of Firefox. It is language specific, so as long as the tests have a defined pass/fail conditions, regardless of what language the initial codebase being tested was written in, it will work just fine.

There are three main parts to the Mozharness infrastructure:

* BaseLogger: [<code>mozharness/base/log.py</code>] (https://github.com/mozilla/build-mozharness/blob/master/mozharness/base/log.py)
* BaseConfig: [<code>mozharness/base/config.py</code>] (https://github.com/mozilla/build-mozharness/blob/master/mozharness/base/config.py)
* BaseScript:&nbsp; [<code>mozharness/base/script.py</code>] (https://github.com/mozilla/build-mozharness/blob/master/mozharness/base/script.py)

### BaseLogger

The BaseLogger component is responsible for logging all the runs of the mozharness test harness and storing them under <code>logs/<testname_STREAM.log></code>. It is useful to note that the logs are stored by stream so it's easier to just look at the respective stream (e.g. ERROR or INFO) when needed. 

The output looks something like this:

{% highlight python %}
 16:23:06     INFO - Reading from file tmpfile_stdout
 16:23:06     INFO - Output received:
 16:23:06     INFO -  /home/simar/mozilla/mozharness/build/application/firefox/firefox
 16:23:06     INFO - Running post-action listener: _resource_record_post_action
 16:23:06     INFO - #####
 16:23:06     INFO - ##### Running run-tests step.
 16:23:06     INFO - #####
 16:23:06     INFO - Running pre-action listener: _resource_record_pre_action
 16:23:06     INFO - Running main action method: run_tests
 16:23:06     INFO - Running command: ['/home/simar/mozilla/mozharness/build/venv/bin/python', '--  version']
 16:23:06     INFO - Copy/paste: /home/simar/mozilla/mozharness/build/venv/bin/python --version
 16:23:06     INFO -  Python 2.7.6
 16:23:06     INFO - Return code: 0
 16:23:06     INFO - mkdir: /home/simar/mozilla/mozharness/build/blobber_upload_dir
 16:23:06     INFO - ENV: MOZ_UPLOAD_DIR is now /home/simar/mozilla/mozharness/build/               blobber_upload_dir
{% endhighlight %}

### BaseConfig

config files define a set of tests that are required to be run. These config files can then be expanded and built on top of the standard so called **sanity** tests. BaseConfig does just that. BaseConfig provides a set of known and most commonly used sets of test cases that are more or less likely to be used.

It also provides methods to parse in various paramaters that can be used to pass in additional arguments into scripts.

{% highlight python %}
def _create_config_parser(self, config_options, usage):
        self.config_parser.add_option(
            "--work-dir", action="store", dest="work_dir",
            type="string", default="build",
        )
        self.config_parser.add_option(
            "--base-work-dir", action="store", dest="base_work_dir",
            type="string", default=os.getcwd(),
        )
{% endhighlight %}

### BaseScript

BaseScript inherits both BaseConfig and BaseLogger and is then used to define high level tasks/tests that are needed to be run. They may include definitions for tests in a python list:

{% highlight python %}
['clobber',
 'read-buildbot-config',
 'download-and-extract',
 'clone-talos',
 'create-virtualenv',
 'install',
 'run-tests',
]

{% endhighlight %}

### So how does it work?

A standard call to mozharness might look like this:

<code>
python scripts/talos_script.py --suite chromez --add-option --webServer,localhost --branch-name Firefox-Non-PGO --system-bits 64 --cfg talos/linux_config.py --download-symbols ondemand --use-talos-json --blob-upload-branch Firefox-Non-PGO --cfg developer_config.py --installer-url http://ftp.mozilla.org/pub/mozilla.org/firefox/nightly/latest-trunk/firefox-37.0a1.en-US.linux-x86_64.tar.bz2
</code>

Breaking it down:

* <code>python scripts/talos_script.py</code>: Here we are using the talos_script.py which acts like a base script for our tests.
* <code>--add-option</code>: We can any addtional command line options that we may want.
* <code>--cfg</code>: Here we define the config file to use for this test. In this particular case we are running on a local developer host so need the [developer_config.py](https://github.com/mozilla/build-mozharness/blob/master/configs/developer_config.py) file.


