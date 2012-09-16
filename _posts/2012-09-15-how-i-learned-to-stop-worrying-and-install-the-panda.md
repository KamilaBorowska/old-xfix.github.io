---
layout: post
title:  How I learned to stop worrying and install the <code>panda</code>
--- 
So, do you want to install `panda`? You have managed to somehow install
Rakudo using `git`. Well, you were doing it wrongly - you should
instead have used Rakudo Star which includes it.

Oh, you don't want to wait again for Parrot, NQP and Rakudo to install.
Well, it's possible. It's not easiest thing ever, but it sort of works.

What you have to do depends on whatever you have installed panda before
(so it worked) or you haven't.

### I haven't installed `panda` before

Well, this is rather common. First, run following shell command.

{% highlight bash %}
which perl6
{% endhighlight %}

It will tell you what perl6 will be used. If it says location in home
directory, you should be fine. But if it says stuff like `/bin/perl6`
or `/usr/bin/perl6`, you should fix it - in current version `panda`
runs `perl6` at certain points. You wouldn't want `panda` to run
outdated version of Rakudo your system could have (also, remove your
system Perl 6 if you want to be safe - you won't need it).

So, you want to fix. Let's assume your shell is either `bash` or `zsh`
- all you have to do is modify either `.bashrc` or `.zshrc` and add
following line at end, where `[bin-path]` should be replaced with your
path to Rakudo's /bin directory (on my system it's `~/rakudo/bin`).

{% highlight bash %}
export PATH="[bin-path]:$PATH"
{% endhighlight %}

Close your shell and run it again. Just to be sure, run `which perl6`
again. If your `perl6` is in correct location you can continue.

Download panda using following command if you haven't already.

{% highlight bash %}
git clone https://github.com/tadzik/panda.git
{% endhighlight %}

And run bootstrap script.

{% highlight bash %}
cd panda
./bootstrap.pl
{% endhighlight %}

Congratulations, you have working `panda` now! If you want, add `panda`
to your path, by modifying $PATH you have added before. Of course, as
said before, modify `[panda-bin-path]` and `[bin-path]` with correct
paths.

{% highlight bash %}
export PATH="[panda-bin-path]:[bin-path]:$PATH"
{% endhighlight %}

### I have installed `panda` before

Run rebootstrap script.

{% highlight bash %}
./rebootstrap.pl
{% endhighlight %}

Wasn't that simple?
