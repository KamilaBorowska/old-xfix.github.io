---
layout: post
title:  Helpful error messages
---
Rakudo lately got an interesting change. Consider following buggy code
(for array binary search), written in functional style (I'm using
recursion). The bug is that it doesn't work because of two different
identifiers `binary-search` and `binary_search`.

{% highlight perl %}
    multi binary-search ($x, @array) {
        binary_search $x, @array, 0, end @array;
    }
    multi binary-search ($x, @array, Int $low, Int $high) {
        return Int unless $low before $high;
        my Int $middle = ($low + $high) div 2;
    
        # In Perl 6, cmp is DWIM comparison, unlike Perl 5
        given $x cmp @array[$middle] {
            when Increase { binary_search $x, @array, $low,        $middle - 1 }
            when Decrease { binary_search $x, @array, $middle + 1, $high       }
            when Same     { $middle }
        }
    }
    say binary-search 42, [1, 5, 7, 23, 33, 37, 39, 41, 42, 45, 56, 72];
{% endhighlight %}

Trying to run it shows compile time error message. The compile part is
important. If you would remove `binary-search` call at end, it still
would report an error, unlike Python.

    ===SORRY!===
    Undeclared routine:
        binary_search used at lines 2, 8, 9. Did you mean '&binary-search'?

Now you know what's wrong and you can easily fix it.
