---
title: Removing Time Machine local backups on macOS Mojave
date: 2020-12-28 16:27:25 +0800
categories: [macOS]
tags: [macos]
---

After cleaning up my Mac filesystem I noticed that there were 105GB of hidden system 
files that can't be shown or deleted.

It turns out those were Time Machine local backups and after some research I found that I can use
the `tmutil` command to find and remove them.

First I used the following command to list the snapshots:
{% highlight shell %}
➜  ~ sudo tmutil listlocalsnapshots /
{% endhighlight %}

which resulted in this:
{% highlight shell %}
com.apple.TimeMachine.2020-12-29-122344
com.apple.TimeMachine.2020-12-29-143529
{% endhighlight %}

Then I used the `tmutil deletelocalsnapshots` command and passed the timestamp to remove remove each snapshot:
{% highlight shell %}
➜  ~ tmutil deletelocalsnapshots 2020-12-29-122344
Deleted local snapshot '2020-12-29-122344'

➜  ~ sudo tmutil listlocalsnapshots /
com.apple.TimeMachine.2020-12-29-143529

➜  ~ tmutil deletelocalsnapshots 2020-12-29-143529
Deleted local snapshot '2020-12-29-143529'
{% endhighlight %}

Checked again and they are gone
{% highlight shell %}
sudo tmutil listlocalsnapshots /
{% endhighlight %}

After checking the Disk Utility I confirmed the space is released.