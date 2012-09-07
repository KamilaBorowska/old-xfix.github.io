---
layout: post
title:  Searching in p6doc
---
First of all, I would like to apologize for my rather weak English.
It's not my native language and I still learn it (but, my English is
way better than two years ago, so I guess it's good enough).

So, you may have heard about [p6doc], the project which started 2 months
ago. According to moritz, it's intended to be comprehensive place where
you can direct people to. It doesn't yet describe everything (for
example `Str.indent` wasn't described when I've wrote this blog post),
but it's very likely that when you read this blog post it's already
described - everything is added to documentation rather quickly :-).
And you can [contribute it] with ease, just modify a page and send
[pull request].

But, while the p6doc is already easy to use, finding stuff in it takes
time, you have to type `doc.perl6.org` in your browser bar, press
CTRL+F (well, I usually press dot, but this only works in Opera), type
name of method and click the method. While it's fast already, it could
be faster.

Of course, there is `perl6doc` tool activated from command line, but
currently it's rather slow (according to `time` utility rendering
`Str.split` took 4 seconds). But when Perl 6 implementations will
improve their performance, it could be acceptable.

The problem is - HTML page requires effort of entering it and
`perl6doc` is currently too slow to be acceptable for anything. But
perhaps there is possibility of nicer way which would combine
performance (as in, pages render nearly instantly) of HTML version and
performance (as in, you can easily access it) of `perl6doc` tool.

Lately, I have contributed [module for DuckDuckGo]<!----> (if you aren't
using DuckDuckGo, [why not]?), to add search for Perl 6 documentation. It
shortens the process of entering p6doc to simply typing "perl6 chomp"
in search bar (and pressing enter). If this isn't simple, than what is?

Try it at [DuckDuckGo].

[Perl 6 Synopsis]: http://perlcabal.org/syn/ "Perl 6: The Synopsis"
[p6doc]: http://doc.perl6.org/ "Perl 6: The Documentation"
[contribute it]: https://github.com/perl6/doc "GitHub: perl6/doc"
[pull request]: https://github.com/perl6/doc/pulls "GitHub: perl6/doc (Pull Request)"
[module for DuckDuckGo]: https://github.com/duckduckgo/zeroclickinfo-fathead/pull/35 "GitHub: duckduckgo/zeroclickinfo-fathead (Pull Request: Perl 6 documentation parser)"
[why not]: https://duckduckgo.com/about.html "DuckDuckGo: about"
[DuckDuckGo]: http://duckduckgo.com/?q=perl6+chomp "DuckDuckGo: perl6 chomp"
