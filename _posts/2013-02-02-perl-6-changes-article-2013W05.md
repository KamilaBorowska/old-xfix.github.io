---
layout: post
title:  Perl 6 changes article - 2013W05
---
The changes are so fun. jnthn is working on porting. nqp will be on
JVM. And then, Rakudo will be. Java is very, very fast. There are some
interesting problems. But nothing impossible to solve. At least, I
hope so. I would continue, but well. This is not the topic. This is
article about changes. Changes in Perl 6, obviously. If you want,
enjoy reading.

Rakudo Star 2013.01 was released. It's based on Rakudo 2013.01. Those
changes aren't in.

On sidenote, a small note. Five word sentences are interesting. I am
not stealing ideas. Well, actually, I am now. I should stop talking
now.

### New features to build stuff
#### Rakudo, a Perl 6 distribution
* `any` and `all` are optimized. To be exact, in statements. The
  `if` and `while` statements. And opposites - `unless` and `until`.
* Many operators are pure now. That means they are sideeffect-free.
* Site libraries are loaded first.
* Pure subroutines are constant folded.
* REPL supports natively typed variables.
* REPL has better error reporting.
