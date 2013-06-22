---
layout: post
title:  Travis - add and forget
---
So, you may have heard that Travis is nice build testing service. It
is - I even use it for my JavaScript projects. But, guess what will
happen if somebody uses Travis incorrectly.

The problem is about one of the most commonly languages made by
[bad developers][1] for [bad developers][2]. But this article isn't
about why this language is bad - we all know it. Technically, I would
use PHP instead of Brainfuck if I would have choice between PHP and
Brainfuck, but it would be choice between very bad and worse.

6 months ago, [Davey Shafik] though - everybody uses Travis CI,
why PHP source code doesn't. He wanted to add Travis CI to his
projects, but after quick look he noticed he doesn't have test cases
in his code and he was too lazy to add them. So, why not add Travis
CI to some project that already has test cases so he could see how
Travis CI would work.

And so he did. He decided to ask PHP to add Travis CI to
[its source code]. Of course, the reaction is obvious.

> This is awesome, thanks!

> If this gets merged in and you guys are interested in pull-request
> testing then shoot us a note at <contact@travis-ci.org>. This feature
> is currently being rolled out manually but we'd love to turn it on
> for php.

> Cool, Travis is awesome.

> FYI looking into it. General idea is great, have to read about
> travis, etc first.

After general "awesome" comments (seriously, do you know other words?)
the code review started. For example, [David Soria Parra] complained
about `!!` usage

{% highlight php startinline %}
define('TRAVIS_CI' , !!getenv('TRAVIS_PHP_VERSION'));
{% endhighlight %}

Of course, version below is better. I mean, it's longer so it's
definitely better. Yeah... is any programmer using `Boolean()` function
anyway in JavaScript (warning: not `Boolean` constructor - `Boolean`
constructor is something you shouldn't ever use as it's always truthy).

{% highlight php startinline %}
define('TRAVIS_CI', (boolean) getenv('TRAVIS_PHP_VERSION'));
{% endhighlight %}

I've decided to look for explicit booleans. So, I made a search query
and I've found [answer on Stack Overflow].

{% highlight php startinline %}
$test_mode_mail = $string == 'true'? true: false;
{% endhighlight %}

I seriously don't know what to think about this. It got 8 upvotes. It
works - but it would be seriously stupid code in any language that
isn't PHP. I mean - `$string == 'true'` is boolean, so why use ternary
operator for it? And WHY use ternary operator for it even if it
wouldn't be ternary operator. I'm not going to mention usage of `==`,
instead of `===`, it doesn't really matter in this case.

My solution would be like this:

{% highlight php startinline %}
define('TRAVIS_CI', isset($_ENV['TRAVIS_PHP_VERSION']));
{% endhighlight %}

But good design should avoid dynamic constants.

Anyway, Davey Shafik has changed `!!` into `(bool)`... hey, hasn't
David Soria Parra said him to use `(boolean)`... check. Those PHP
aliases attack again. Why? Why converting to boolean could be done
using two casts with only different name? Why converting to float
could be done using _three_ casts with only different name -
`(float)`, `(double)` and `(real)`?

But that isn't real problem. The Travis CI change was merged. Now
README page of PHP contains the "build status: failing". I've decided
to click that image - every single change had either "build status:
failing" or "build status: unknown" (because of failure while doing
`git clone` (possibly GitHub went down?)). PHP wastes time of Travis
CI just to get "build status: failing" again.

After some time, David Soria Parra has decided to
[add e-mailing about failures] to php-qa@lists.php.net.

What went wrong? Well...

{% highlight yaml %}
notifications:
    email:
        recipients:
            - php-qa@lists.php.net
        on_success: change # [always|never|change] default: change
        on_failure: always # [always|never|change] default: always
{% endhighlight %}

But... PHP always fails - so PHP Quality Assurance would get lots of
useless messages. Well, the solution for PHP developers wasn't to fix
PHP issues, definitely not - that would be too clever for programmers
that work on PHP. The fix for developers was to
[disable e-mail notifications].

{% highlight yaml %}
notifications:
    email:
        recipients:
            - php-qa@lists.php.net
        on_success: never # [always|never|change] default: change
        on_failure: never # [always|never|change] default: always
{% endhighlight %}

Now, I wonder why simply didn't removed `notifications:` section. I
mean, they gave e-mail and told Travis - whatever happens, don't
e-mail. I'm going to call this [brillant].

After some time, David Soria Parra has noticed that he doesn't have
to give e-mail to PHP Quality Assurance. So, he [removed it].

{% highlight yaml %}
notifications:
    email: false
{% endhighlight %}

Now I wonder why he didn't removed whole section. He doesn't want
any notifications, so why have this section? So now, Travis CI does
nothing and everybody ignores it. I mean, it compiles, but...

It has 1123 compilation warnings. It has 106 test failures (with
43 expected test failures), one expected SIGSEGV (wait...) and 70.4%
code coverage. And it's not about unimplemented features - those are
simply SKIPed. No, it isn't better in PHP 5.4 - I mean, PHP 5.4 has
103 test failures (with 43 expected test failures).

PHP developers - please fix every bug before implementing new features.
Having codebase that constantly contains lots of test failures isn't
really fun.

If you are PHP user, I would advice you trying some other language,
such as [Perl] <!----> (believe me or not, Perl is way better than PHP)
or [Python]. Those languages are consistent and more structured than
PHP. Annoyed by security issues? Those languages make it easier to
protect against them.

If you still want to use PHP, use OOP everywhere and never use global
variables (with the exception for `$_GET`, `$_POST` and `$_SERVER` of
course). PHP is simply unusable without OOP. But still, I would
recommend using other langauges. Also, use template engine and prepared
SQL queries.

### 2012-10-29 update
The older version of article was mentioning following syntax.

{% highlight php startinline %}
const TRAVIS_CI = isset($_ENV['TRAVIS_PHP_VERSION']);
{% endhighlight %}

Turns out that it doesn't work. Why? Because constant value isn't
constant - it depends on environment variable existing. All you get is
cryptic syntax error. PHP stupidly uses its grammar to annoy developer,
even if this syntax would be allowed in other sane languages. For
example, you cannot do `func()()`, just because grammar forbids that.
I haven't heard of other language with similar problems.

    PHP Parse error:  syntax error, unexpected 'isset' (T_ISSET)

Instead, the solution should be more like this.

{% highlight php startinline %}
$travis_ci = isset($_ENV['TRAVIS_PHP_VERSION']);
{% endhighlight %}

It's not constant. It shouldn't be because it isn't real constant.

[1]: http://use.perl.org/use.perl.org/_Aristotle/journal/33448.html "use Perl: The blind leading the blind"
[2]: http://me.veekun.com/blog/2012/04/09/php-a-fractal-of-bad-design/ "fuzzy notepad: PHP: a fractal of bad design"
[Davey Shafik]: https://github.com/dshafik "GitHub: dshafik"
[its source code]: https://github.com/php/php-src/pull/68 "GitHub: php/php-src: Pull request #68 (Add support for Travis CI)"
[David Soria Parra]: http://github.com/dsp "GitHub: dsp"
[answer on Stack Overflow]: http://stackoverflow.com/a/7336873/736054 "Stack Overflow: How to convert string to boolean php"
[add e-mailing about failures]: https://github.com/php/php-src/commit/1fc6b3c4d9f364aab0353cce979f582908eab61b "GitHub: php/php-src: Send mails to php-qa@lists.php.net whenever a build is failing"
[disable e-mail notifications]: https://github.com/php/php-src/commit/f8d60e2f0963da833a024347a3996a7bb8904822 "GitHub: php/php-src: Travis stop spamming"
[brillant]: http://thedailywtf.com/Articles/The_Brillant_Paula_Bean.aspx "The Daily WTF: The Brillant Paula Bean"
[removed it]: https://github.com/php/php-src/commit/0d85a86bbb151537a2362f17224d076556252ada "GitHub: php/php-src: Disable email notifications for travis-ci"
[Perl]: http://www.perl.org/ "The Perl Programming Language"
[Python]: http://python.org/ "Python Programming Language - Official Website"
