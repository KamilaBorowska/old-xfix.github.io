---
layout: post
title:  Helpful error messages
---
Rakudo lately got an interesting change. Consider following buggy code
(for array binary search), written in functional style (I'm using
recursion). The bug is that it doesn't work because of two different
identifiers `binary-search` and `binary_search`.

<script src="https://gist.github.com/4577858.js">
// ASDF why you auto close tags XML style
</script>

Trying to run it shows compile time error message. The compile part is
important. If you would remove `binary-search` call at end, it still
would report an error, unlike Python.

    ===SORRY!===
    Undeclared routine:
        binary_search used at lines 2, 8, 9. Did you mean '&binary-search'?

Now you know what's wrong and you can easily fix it. Unlike let's say,
Jekyll that I use for my blog. Not only it doesn't work on my PC for
some reason, but also [refused to highlight my code] - it gave some sort
of XML error (it simply said "REXML could not parse this XML/HTML").
I gave up and put it on Gist. And the first attempt was failed, because
embed code is `<script>` and Jekyll self-closed the tag (XML-style, but
this is HTML). This is annoying.

[refused to highlight my code]: https://github.com/GlitchMr/glitchmr.github.com/commits/master/_posts/2013-01-20-helpful-error-messages.md "GitHub: GlitchMr/glitchmr.github.com (history for this post)"
