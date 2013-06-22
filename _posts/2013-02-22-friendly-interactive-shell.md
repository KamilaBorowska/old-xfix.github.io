---
layout: post
title:  friendly interactive shell
---
I'm not sure if anybody noticed, but lately, nothing was happening on
my blog. So, I'm going to write an article about friendly interactive
shell (fish), the best shell I've used ever (it's my personal opinion,
in case you haven't noticed, see my footer blog). Actually, I think
I've moved to it half of the year ago or something.

These days, the two popular shells exist - bash and Z shell. I'm aware
of that more shells exist - just nobody uses them these days By the
way, if you try to find logical fallacy, I agree, cmd.exe and Windows
PowerShell are often used, but those aren't UNIX shells. Oh, and
programming language REPLs are often used too (like irb), but they
aren't UNIX shells too.

### Bash

The bash shell is standard shell, included in many Linux distributions.
It will make you bash your head against the brick wall (pun intended).

One of examples of bash code I could have found is this. And trust me,
it's not the worst thing (thanks, [Wikibooks]).

    # print the powers of two, from 1 to 512:
    for ((i = 1; i < 1000; i *= 2)); do
    echo $i
    done

Do you see double parens here? This is really strange math syntax that
only supports integers. Why double parenthesis? Well, the answer is
compatibility with Bourne shell (really, really old shell, usually
called sh (but sometimes sh is actually bash, to make you bash)).

Also, you could have noticed keywords `do` and `done`. Bash could
easily not require `do` keyword before `for` loop body. Yet, it's
required. The other strange quirks is that you aren't allowed to put
semicolon after `do` keyword. Why? I've no idea, really. The `done`
word, while I think it's needed, differs between blocks. For example,
`if` blocks end with `fi`.

Bourne again shell has lots of strange syntax you really wouldn't ever
use. For example, bash has `$''` syntax. It unescapes escape sequences
inside. The strange part is `''` in bash doesn't do any processing at
all, yet `$''` does. Bash also has `$""` syntax, but it doesn't do
escape processing - instead it uses gettext to translate string inside.

    # Prints 'Hello world.', with new line in middle.
    echo Hello$'\n'world.
    # Prints 'Hello\nworld.', literally.
    echo Hello$"\n"world.

Consistency in bash is even worse than in languages such as PHP. There
are many ways to do something, but none of them are obvious. And I mean
that, none. Expanding bash is adding new syntax that totally doesn't
make sense, but was a syntax error in previous versions. `$` sign is
used for many features that have nothing with variables.

Also, bash integrates many programs in it that really shouldn't be
inside. For example, `echo`.

    [glitchmr@pineapple ~]$ echo --help
    --help
    [glitchmr@pineapple ~]$ which echo
    /bin/echo
    [glitchmr@pineapple ~]$ /bin/echo --help
    Usage: /bin/echo [SHORT-OPTION]... [STRING]...
      or:  /bin/echo LONG-OPTION
    Echo the STRING(s) to standard output.

    ...

    NOTE: your shell may have its own version of echo, which usually supersedes
    the version described here.  Please refer to your shell's documentation
    for details about the options it supports.

    Report echo bugs to bug-coreutils@gnu.org
    GNU coreutils home page: <http://www.gnu.org/software/coreutils/>
    General help using GNU software: <http://www.gnu.org/gethelp/>
    Report echo translation bugs to <http://translationproject.org/team/>
    For complete documentation, run: info coreutils 'echo invocation'

The `/bin/echo` on my system is GNU echo. The `bash` on my system is
GNU bash. They were both made by GNU. Yet, they act differently. Is
there a better recipe for failure than program that actually isn't
called because your shell is "clever".

It's not just `echo`. Many commands are implemented in shell, when
they should be implemented outside shell. For example, `pwd`. Why
exactly? To confuse you when dealing with recursive structures.

    [glitchmr@pineapple ~]$ mkdir recursion
    [glitchmr@pineapple ~]$ cd recursion/
    [glitchmr@pineapple recursion]$ ln -s . recursion
    [glitchmr@pineapple recursion]$ cd recursion
    [glitchmr@pineapple recursion]$ pwd
    /home/glitchmr/recursion/recursion
    [glitchmr@pineapple recursion]$ which pwd
    /bin/pwd
    [glitchmr@pineapple recursion]$ /bin/pwd
    /home/glitchmr/recursion
    [glitchmr@pineapple recursion]$ 

The shell outright lies about your location. You're in
`/home/glitchmr/recursion`. Why shell does that? Well, it does that
to not confuse you when typing `cd ..`. And indeed, if you type
`cd recursion/recursion`, and `cd ..` after that, you will end in
`recursion` directory, not the directory where you created the first
`recursion` directory.

Symbolic links in UNIX are simply redirects. If you made a symbolic
link to `/usr/share/hell`, you shouldn't expect that `cd ..` made
after following link follow return you anywhere that isn't `/usr/share`.
Yet, implementation of symbolic links in bash lies.

The lie is obvious after using anything that isn't bash builtin. For
example, `ls ..` will always show contents of `/usr/share`, even if
you made symbolic link in `/tmp/heaven`. Yet, `cd ..` will return to
`/tmp/heaven`.

By the way, in case you ask, `cd` is also a builtin. But that's
acceptable, because external command cannot change environment of
caller (such at PATH). So it has to be a builtin. Yet, somehow,
[some people are still confused].

### Z shell
Z shell is a shell that is way better than bash. Well, if you
*configure* it properly. It's a shell that appears to have some
compatibility with Bash scripters (for example `$''` syntax works, but
`$""` doesn't (or rather, it returns literal '$' and contents of
`""`)).

Z shell has lots of features. In fact, the author is "not aware of a
major interactive feature in any other freely-available shell which zsh
does not also have (except smallness)". Something that would be sane
default actually isn't default. In fact, with default settings it
mostly works like `bash`.

For example, completion. By default, Z shell only completes file names
and command names. If you will type `sudo apt-get install`, press space
and then press Tab, it will give you file names. Useful? I don't think
so.

Of course, the option to enable better Tab completion is possible to
turn on. It's just not default. Why the shell would have default that
don't make sense. Simiarly, by default Tab completion cannot expand
globs. Yet, it's possible to enable.

When you start Z shell for first time, you see the configuration
wizard (called `zsh-newuser-install`). The problem is, it's the most
non-user-friendly thing I've seen. It asks about minor details you
don't care about, without proposing any useful default. For example,
the maximum number of errors you can make for the command to be still
proposed in error message. I just have pressed Enter, and the response
is "Please enter a number". Do you think I care about this?

Actually, as far I know, nobody actually uses this wizard. In most
cases, zsh is configured using so called `oh-my-zsh`. What it is
actually? Well, it's a config script with sane things configured by
default. Yet, it has got 8,648 stars on GitHub and 3,326 forks as I'm
writing it.

Yes, it's just ZSH config file that has things that should be default,
but they aren't. Oh, and `plugins` array that contains list of plugins
that mostly make aliases and completion adders that aren't in main zsh
(like CoffeeScript). For example `git` plugin adds aliases like `gc`
for `git commit -v`.

I don't know about you, but I don't like programs which take 4 hours to
configure. Z shell is one of these. `oh-my-zsh` makes the job easier,
but I still don't think it's worth wasting the time. Perhaps if `fish`
wouldn't exist, it would be worth it.

### friendly interactive shell
Z shell, aside of totally insane configuration isn't a bad shell (if
I would want to talk about bad shells, it would be csh or cmd.exe).
friendly interactive shell is as good as Z shell (with good Z shell
configuration), except without having to configure it.

In fact, most of things cannot be configured. You cannot configure
nonsense like maximal history length (by design). You cannot configure
interpretation of numbers starting with 0 as octal (you can in Z
shell - I have no idea why Z shell even allows you to type "echo 010"
to get "8" when configured to do so - but it does). You cannot disable
file globs.

Remember `.bashrc`/`.zshrc` configuration file? You won't need it.
The colors and themes can be easily configured using a web service
(but if you prefer, you can use `set` command directly).

For example, if you want to configure your `EDITOR` to be `vim`, you
can run the following command.

    set -Ux EDITOR vim

The `-U` modifier means that this value is stored between session. The
`-x` means that this variable is exported to programs ran by shell.
After you have done that, your variable magically appears in every
`fish` session.

Adding new functions is also easy. It's enough to type
`funced function-name`. After 

Fish also offers features I haven't seen in other shells, like syntax
highlighting. Ommited quote? Syntax highlighting will help you.

In fish, everything is tab-completeable. Even if the program doesn't
have specific tab completion definitions, it's still tab-completeable,
thanks to generating tab completions from man pages. For example,
`gedit` doesn't have specific tab completions, yet fish managed to
create them.

    glitchmr@pineapple ~> gedit -
    --background                                         (Run gedit in the background.)
    --encoding        (Set the character encoding to be used for openi… [See Man Page])
    --geometry                     (Set the X geometry window size (WIDTHxHEIGHT+X+Y).)
    --help                                           (Prints the command line options.)
    --list-encodings  (Display list of possible values for the encodin… [See Man Page])
    --new-document            (Create a new document in an existing instance of gedit.)
    --new-window       (Create a new toplevel window in an existing instance of gedit.)
    --standalone                                        (Run gedit in standalone mode.)
    --version                                    (Output version information and exit.)
    --wait                                    (Open files and block the gedit process.)
    -b                                                   (Run gedit in the background.)
    -g                             (Set the X geometry window size (WIDTHxHEIGHT+X+Y).)
    -s                                                  (Run gedit in standalone mode.)
    -w                                        (Open files and block the gedit process.)

Of course, just like zsh (with correct configuration), it can expand
more stuff. For example, kill command expands process IDs.

    glitchmr@pineapple ~> kill 
    1            (systemd)  127           (systemd-udevd)  479                (gconfd-2)
    2           (kthreadd)  132         (systemd-journal)  508                   (gvfsd)
    3        (ksoftirqd/0)  229               (hd-audio0)  512              (gvfsd-fuse)
    5       (kworker/0:0H)  230               (kpsmoused)  527         (cinnamon [tty1])
    7       (kworker/u:0H)  299              (irq/46-mei)  528                  (colord)
    8        (migration/0)  307                (cfg80211)  534  (polkit-gnome-au [tty1])
    9        (rcu_preempt)  308                    (hci0)  536          (dropbox [tty1])
    10            (rcu_bh)  309            (kworker/u:1H)  539        (nm-applet [tty1])
    11         (rcu_sched)  310               (scsi_eh_6)  540  (gnome-screensav [tty1])
    12        (watchdog/0)  311         (rts5139-control)  558      (gsd-printer [tty1])
    13        (watchdog/1)  312         (rts5139-polling)  566         (gvfs-udisks2-vo)
    14       (ksoftirqd/1)  314                (ttm_swap)  568                 (udisksd)
    15       (migration/1)  325          (NetworkManager)  598             (gvfsd-trash)
    17      (kworker/1:0H)  329             (dbus-daemon)  728          (firefox [tty1])
    18            (cpuset)  331          (systemd-logind)  744          (hexchat [tty1])
    19           (khelper)  332                   (login)  746      (pxgsettings [tty1])
    20         (kdevtmpfs)  335                 (polkitd)  797           (dconf-service)
    21             (netns)  341                  (mysqld)  798   (gnome-terminal [tty1])
    22       (bdi-default)  344          (wpa_supplicant)  805  (gnome-pty-helpe [tty1])
    23           (kblockd)  348                   (nginx)  806            (fish [pts/0])
    26        (khungtaskd)  349                   (nginx)  1374            (kworker/1:2)
    27           (kswapd0)  351                 (php-fpm)  5790           (fish [pts/1])
    28              (ksmd)  352                 (php-fpm)  10502        (gvfsd-metadata)
    29        (khugepaged)  353                 (php-fpm)  13551          (fish [pts/2])
    30     (fsnotify_mark)  373                (dhclient)  13635           (ssh [pts/2])
    31            (crypto)  385             (fish [tty1])  13866           (kworker/1:0)
    35          (kthrotld)  387                   (fishd)  18892            (gvfsd-http)
    38           (deferwq)  412           (startx [tty1])  20044          (fish [pts/1])
    62             (khubd)  429            (xinit [tty1])  20046        (python [pts/1])
    63           (ata_sff)  430                       (X)  20405           (vim [pts/0])
    64         (scsi_eh_0)  434    (gnome-session [tty1])  21370           (kworker/0:1)
    65         (scsi_eh_1)  437      (dbus-launch [tty1])  21373           (kworker/u:2)
    66         (scsi_eh_2)  438             (dbus-daemon)  22399          (fish [pts/3])
    67         (scsi_eh_3)  440         (at-spi-bus-laun)  22472           (vim [pts/3])
    68         (scsi_eh_4)  444             (dbus-daemon)  22521           (kworker/u:0)
    69         (scsi_eh_5)  447         (at-spi2-registr)  22863           (kworker/0:0)
    93      (kworker/0:1H)  455  (gnome-settings- [tty1])  22945           (kworker/u:1)
    95      (kworker/1:1H)  464              (pulseaudio)  23238            (ps [pts/4])
    103      (jbd2/sda6-8)  465            (rtkit-daemon)  23239          (grep [pts/4])
    104  (ext4-dio-unwrit)  468                 (upowerd)  23240          (tail [pts/4])
    110        (flush-8:0)  476            (gconf-helper)

Of course, fish has some disadvantages. One of disadvantages is the
syntax. It's not POSIX compatible by design. For example, the history
substition doesn't exist. Instead, you're supposed (if you wanted,
let's say, sudo) to press Up, Home, and type 'sudo'. Command
substitution uses `()`, not ``` `` ``` or `$()`. Still, if you will
accidentally use `$()` bashism, the shell will inform you with an
error.

Also, the newest stable is really old and slow. Instead I would use
version from git, which is usually more stable than stable version.
In Arch Linux, it's `fish-shell-git` from AUR, on other operating
systems (like CentOS I use on my VPS), you have to compile it yourself.
It isn't so difficult. The GitHub repository is [fish-shell/fish-shell].

As for why I'm mentioning fish shell, well, I have lately contributed
a small patch to [hint -C suboptions in perl] - like `-CSDL`. Nothing
special, but hey, why not make a blog post, considering I write them
rarely.

[Wikibooks]: https://en.wikibooks.org/wiki/Bash_Shell_Scripting#Non-integer_arithmetic "Wikibooks: Bash Shell Scripting"
[some people are still confused]: http://superuser.com/questions/241129/why-wont-sudo-cd-work "Super User: Why won't 'sudo cd' work?"
[fish-shell/fish-shell]: https://github.com/fish-shell/fish-shell/ "GitHub: fish-shell/fish-shell"
[hint -C suboptions in perl]: https://github.com/fish-shell/fish-shell/commit/a61e2caadb532ddbe73b85fd539c58c173e9f051 "GitHub: fish-shell/fish-shell - Update Perl completion"
