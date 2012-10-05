---
layout: posts
title: Yet another DuckDuckDuck goodie
---
Lately, I have made yet another plugin for [DuckDuckGo] <!---->(this
time it isn't connected with Perl 6 - but hey, it's mine).

So, well, I have seen times like 11:00 EST. The problem is - what hour
it is actually. So, I can do query like:

    11:00 EST into GMT+1

And it will reply me.

    11:00 (EST, UTC-5) is 17:00 (GMT+1)

Also, you can skip timezone and it will think it's GMT - it's rather
common today. While fixing GMT is easy, it's very likely that you meant
that.

So, well. It's likely to be very quickly applied to DuckDuckGo - they
already have said they're ready to deploy the plugin in [pull request].

[DuckDuckGo]: http://duckduckgo.com/ "DuckDuckGo"
[pull request]: https://github.com/duckduckgo/zeroclickinfo-goodies/pull/125 "GitHub: duckduckgo/zeroclickinfo-goodies (Pull Request #125)"
