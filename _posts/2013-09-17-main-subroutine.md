---
layout: post
title:  The MAIN subroutine in Perl 6
---
Because of one year of making Perl 6 changes articles, I decided
to start a small series involving various tricks in Perl 6. As I said
before, the first article will be about `MAIN` function, surprisingly
very useful, even for small programs, because of its behavior.

First, some historical (sort of) background. In languages like C, Go or
Java, every program has to start with the [`main`
function](https://en.wikipedia.org/wiki/Main_function).

    #include <stdio.h>
    int main(int argc, char* argv[]) {
        int i;
        puts("Hello, world!");
        for (i = 1; i < argc; i++) {
            printf("Argument %d: %s\n", i, argv[i]);
        }
        return 0;
    }

However, many programming languages decided to not have a `main`
function, but instead, assume everything you run in main file is ran.
Usually, that also means the argv is a global variable. One of examples
of such behavior is Perl 5, where you can write code like the
following.

    use 5.012;

    say "Hello, world!";
    for (1 .. @ARGV) {
        say "Argument $_: $ARGV[$_ - 1]";
    }

As Perl 6 is designed to be mostly like Perl 5, this is still possible
in Perl 6. Of course, being Perl 6, many things changed - the
compatibility with Perl 5 is not a concern (but there is work on
enabling to use Perl 5 code in Perl 6). For example, `@ARGV` is now
`@*ARGS`, in order to remove C heritage.

    use v6;

    say "Hello, world!";

    for @*ARGS.kv -> $i, $value {
        say "Argument {$i + 1}: $value";
    }

But that doesn't mean you have to write everything just like you would
in Perl 5. Unlike Perl 5 (well,
[with default environment](https://metacpan.org/module/Devel::Main)),
Perl 6 offers main routine, called `MAIN`. The reason why name is
written uppercase is that magical keywords, like `BEGIN` are written in
uppercase in Perl 6 (it's a convention in Perl 6, and in my opinion,
it's better than hacks like four underscores in Python).

    use v6;

    sub MAIN(*@arguments) {
        say "Hello, world!";

        for @arguments.kv -> $i, $value {
            say "Argument {$i + 1}: $value";
        }
    }

However, Perl 6 main routine, unlike main routine in other programming
languages is not just there to run code inside structured function. It
can actually do a lot more, including option parsing. In other
languages, you could get argv at most from `MAIN`. Perl 6 however, puts
complete option parser in `MAIN` subroutine. For example, if your
program takes two arguments, input and output, it could be done this
way.

    use v6;

    sub MAIN($input, $output) {
        ...;
    }

If you forget to use two arguments, Perl 6 friendly reminds you to use
two arguments, and stops the program, reminding you arguments program
takes.

    Usage:
      program <input> <output>

You can also specify types of arguments. For example, you could want to
implement a program that repeats shell command. The first argument
would be integer containing number of arguments.

    use v6;

    sub MAIN(Int $repeats, $program, *@arguments) {
        for ^$repeats {
            run $program, @arguments;
        }
    }

This program requires that `$repeats` argument will be `Int`. Something
like `4` or `0xA` would pass, as it's integer, but not values like `a`
or `4.3`. If the value is passed correctly, the program runs.

    $ perl6 repeat 4 echo Hello, world!
    Hello, world!
    Hello, world!
    Hello, world!
    Hello, world!

If not, Perl 6 shows standard error message, in order to help you with
the arguments the program needs.

    Usage:
      repeat <repeats> <program> [<arguments> ...]

Also, as you could expect from options parser, it can also parse
options starting with `-`. For example, let's assume somebody crazy
decided that this `MAIN` program needs way to make it slower, and
verboser. In Perl 6, it's very easy to do.

    use v6;

    sub MAIN(
        Int $repeats,
        $program,
        *@arguments,
        Bool :$verbose,
        Real :$wait = 0,
    ) {
        for ^$repeats {
            if $verbose {
                say "Running $program @arguments[]";
            }
            run $program, @arguments;
            sleep $wait;
        }
    }

The variables beginning with `:` are named arguments, and can be called
in normal code with `name => $value` notation (however, there is a
shortcut for booleans - `:name`, and `:!name`). `Real` type means any
non-complex number. Unlike Perl 5, `sleep` can take floating point
arguments, without converting them to integer.

Now, you can run it like in the example below, and it will wait 0.5
seconds between every invocation, and it will say it runs a program
every time it runs it - just because you wanted a verbose program.

    $ perl6 repeat --verbose --wait=.5 4 echo Hello, world!
    Running echo hello world
    hello world
    Running echo hello world
    hello world
    Running echo hello world
    hello world
    Running echo hello world
    hello world

You can also modify usage instructions, if you want to. The default
usage instructions are rather nice, but you may want to rewrite them
(for example, to look exactly as you want). In Perl 6, you can create
`USAGE` function, in order to create new usage instructions.

    use v6;

    # Silly program that always fails. I'm sure Acme namespace would love it.
    sub MAIN($value where 0 == 1) {
        ...;
    }

    sub USAGE {
        say "This program always fails.";
    }

The last interesting thing would be multiple `MAIN` subroutines, you
could already know from tools like `apt` or `git`. Perl 6 attempts to
select most specific subroutine or shows specific usage instructions.

    use v6;

    #= Install stuff
    multi MAIN("install", $package = "everything") {
        say "Installing $package.";
    }

    #= Get information
    multi MAIN("info") {
        say "Getting information.";
    }

This code requires you to call the program either using `install`
followed by optional string, or `info` without any arguments.
Otherwise, you would get typical usage instructions.

    Usage:
      program install [<package>] -- Install stuff
      program info -- Get information

The great example of `MAIN` subroutine already exists in panda package
manager. The package manager uses multiple `MAIN` subroutines, in order
to let it do multiple things. The code is available for reading in
[panda repository,
/bin/panda](https://github.com/tadzik/panda/blob/master/bin/panda).
Trying to use panda incorrectly (like with `remove` command), shows
following usage.

    Usage:
      /usr/bin/panda [--notests] [--nodeps] install [<modules> ...] -- Install the specified modules
      /usr/bin/panda [--installed] [--verbose] list -- List all available modules
      /usr/bin/panda update -- Update the module database
      /usr/bin/panda info [<modules> ...] -- Display information about specified modules
      /usr/bin/panda search <pattern> -- Search the name/description

And that's all for now. There are still some interesting things (for
example, you can use postfix `!` on variable in order to make named
parameter mandatory). You can also read more in [the Perl 6
specification](http://perlcabal.org/syn/S06.html#Declaring_a_MAIN_subroutine),
althrough some things aren't implemented in Rakudo for now (like
`:f(:$frompart)` syntax for aliasing).
