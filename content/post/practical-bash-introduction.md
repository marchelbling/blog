---
draft: true
title: A practical introduction to bash
categories:
- tools
tags:
- script
- shell
- tools
description: Some stuff about Bash
---


# Introduction

## terminal vs console vs shell

[This](http://superuser.com/questions/144666/what-is-the-difference-between-shell-console-and-terminal) [is](http://superuser.com/questions/231005/terminal-vs-shell) [very](http://superuser.com/questions/231005/terminal-vs-shell) [confusing](http://askubuntu.com/questions/506510/what-is-the-difference-between-terminal-console-shell-and-command-line) [at](http://unix.stackexchange.com/questions/33881/what-is-the-difference-between-a-console-shell-terminal-terminal-emulator-te) [first](http://stackoverflow.com/questions/21014344/terminal-or-console-or-shell-or-command-prompt). But it should not.

A terminal is a hardware device providing input/display data. A terminal emulator (or tty) emulates a video terminal in a windowing system.

A console runs a terminal that specifically inputs from a keyboard and outputs to a monitor, everything being *local* and attached to the computer.

A shell is a command line interpreter i.e. a computer program relaying on a terminal for user interaction.

## POSIX

The POSIX [specifications](http://pubs.opengroup.org/onlinepubs/9699919799/)

> defines a standard operating system interface and environment, including a command interpreter (or “shell”), and common utility programs to support applications portability at the source code level. POSIX.1-2008 is intended to be used by both application developers and system implementors and comprises four major components (each in an associated volume)

POSIX is thus both a “program-level” and a “user-level” specification for interoperability over unix-y OSes.


## sh == bash?

http://stackoverflow.com/questions/5725296/difference-between-sh-and-bash

## Interactivity vs scripting

Shebang. `$-`.

```
$ cat foo.sh
#!/bin/bash

echo "$-"
```

```
$ chmod +x foo.sh && ./foo.sh
hB

$ echo "$-"
himBH
```


# Variables

## Definition

## Referencing a value

Quoting `''` vs `""`.
`$variable` is the same as `${variable}`.

## Default value

`${value:-default_value}`. Can be recursive: `${value:-${Value:-$( echo "VALUE" )}}`

## Strings

Single vs double quotes. Escaping.

## Numerics

### Floating values

Need `bc -l`.

## Scope

`local`
`unset`

```
function func {
  echo "global foo: ${foo}"
  local foo="foo"
  echo "${foo}"
  foo="${foo}baz"
}

foo="bar"
func
echo "${foo}"
```

## Arrays

seq start [increment] stop

## Hash

## Weak typing

```bash

$ foo=1
$ echo $(( $foo + 1 ))
2
$ foo="a"
$ echo $(( $foo + 1 ))
1
$ foo=1
$ echo $(( "${foo}" + 1 ))
-bash: "1" + 1 : syntax error: operand expected (error token is ""1" + 1 ")
```

## Special variables

See http://tldp.org/LDP/abs/html/internalvariables.html

* `$0`: name of the script.
* `$1 $2`: command line arguments given to the script. `$1` is the first argument, `$2` the second and so on.
* `$#`: How many command line arguments were given to the script.
* `$*`: All of the command line arguments.
* `$-`: is shell interactive or not
* `$$`: process id
* `$?`: status of the *last* command
* `!!`: rerun last command
* `!foo`: rerun last command involving `foo`
* `!$`: last argument from the last command


# Builtins

See official [builtins](http://www.gnu.org/software/bash/manual/html_node/Bash-Builtins.html) list

* [`alias`](http://ss64.com/bash/alias.html): set/get aliases
* `bind`: bind a key sequence to a Readline function or a macro, or set a Readline variable
* [`builtin`](http://ss64.com/bash/builtin.html): run a shell builtin, passing it args, and return its exit status
* `caller`: returns the context of the current subroutine call
* [`command`](http://ss64.com/bash/command.html): execute a simple command or display information about commands
* [`declare`](http://ss64.com/bash/declare.html): set/get variable values and attributes
* [`echo`](http://ss64.com/bash/echo.html): write to standard output
* [`enable`](http://ss64.com/bash/enable.html): enable/disable shell builtins
* `help`: display help for builtin commands
* [`let`](http://ss64.com/bash/let.html): evaluate arithmetic expressions
* [`local`](http://ss64.com/bash/local.html): define local variables to functions
* [`logout`](http://ss64.com/bash/logout.html): exit shell login
* `mapfile`: read lines from the standard input into an indexed array variable
* [`printf`](http://ss64.com/bash/printf.html): formats and prints arguments following a given format
* [`read`](http://ss64.com/bash/read.html): mark shell variables as unchangeable
* `readarray`: read lines from a file into an array variable
* [`source`](http://ss64.com/bash/source.html) or dot (`.`) operator: source filename
* [`type`](http://ss64.com/bash/type.html): display information about command type
* [`typeset`](http://tldp.org/LDP/abs/html/declareref.html): set variable values and attributes
* [`ulimit`](http://ss64.com/bash/ulimit.html): modify shell resource limits
* [`unalias`](http://ss64.com/bash/alias.html): remove some aliases


# Pipes & Redirects

http://vincebuffalo.com/2013/08/08/the-mighty-named-pipe.html
https://gist.github.com/marchelbling/dd72ae78d4cd6845a038

# Traps


# Debugging

http://www.tldp.org/LDP/Bash-Beginners-Guide/html/Bash-Beginners-Guide.html#sect_02_03

Short notation	Long notation	Result
set -f	set -o noglob	Disable file name generation using metacharacters (globbing).
set -v	set -o verbose	Prints shell input lines as they are read.
set -x	set -o xtrace	Print command traces before executing command.

http://bashdb.sourceforge.net/


# Utils

## xargs

http://stackoverflow.com/questions/4185017/maximum-number-of-bash-arguments-max-num-cp-arguments

## Network

### Requests

* `curl`
* `wget`

### Syncing data

* `scp`
* `rsync`

## Computation

## Parallel

http://www.gnu.org/software/parallel/
+ `-P` option of [`xargs`](http://web.archive.org/web/20101202135616/http://teddziuba.com/2010/10/taco-bell-programming.html)


## Executing a script remotely

```
$ function remote_hostname1 {
    local credentials="$1"
    ssh "${credentials}" 'bash -s' <<'ENDSSH'
        echo ${HOSTNAME}
        echo \${HOSTNAME}
ENDSSH

$ remote_hostname1 "me@remote"
remote
$HOSTNAME

$ function remote_hostname2 {
    local credentials="$1"
    ssh "${credentials}" 'bash -s' <<ENDSSH
        echo ${HOSTNAME}
        echo \${HOSTNAME}
ENDSSH
}

$ remote_hostname2 "me@remote"
localhost
remote

```

# Useful binaries

* `nm`: list symbols in an object file
* `objdump` (`otool` on OSX)):  display information from object files
* `hexdump`:
* `ld`: [linker](https://github.com/0xAX/linux-insides/blob/master/Misc/linkers.md)


# Moving away from Bash


## Why bash?

very good glue
pervasive (still relevant in a docker world)
lots of tools

## Why moving away from bash?

do you write `cat toto | grep tata`
or `if [ "$( grep tata toto | wc -l )" ]; ...`

well, you probably don’t know how to bash or your colleague doesn’t

defensive bash programming is difficult (e.g. failure in a pipe, global variable leaking)


## Evolution of our stack

C++/Python/Bash => C++/Python(/Bash)
plotting evolution of line counts in bash/python


## Difficulties

Bash return value vs output result
Global vs local
Unicode

## Steps

Quote joel on software

Mostly standard practice when rewriting large code base:
Rewrite legacy code in an 'appropriate' way:

1. write some tests (testing 3D is hard but still there are things you can test)
2. split legacy code into functions
3. use local variables wherever possible
4. move code part after part
5. drop legacy ftw!

## Rewrite steps

1. you'll be happy that argparse is nicer than bash option parsed by hand
2. use subprocess
3. use NamedTuple to not
* leak/introduce undesire  `env` variables
* control when any variable should be updated
4. replace return values with exceptions


# References

* [Bash Reference Manual](http://www.gnu.org/software/bash/manual/bashref.html)
* [(Google) Shell style guide](https://google.github.io/styleguide/shell.xml)
* [Command-line tools can be 235x faster than your Hadoop cluster](http://aadrake.com/command-line-tools-can-be-235x-faster-than-your-hadoop-cluster.html)
* [Pipes and Filters](http://blog.petersobot.com/pipes-and-filters)
* [Defensive BASH Programming](http://www.kfirlavi.com/blog/2012/11/14/defensive-bash-programming/)
* [The Unix Shell’s Humble If ](http://robots.thoughtbot.com/the-unix-shells-humble-if)
* [Shell programming with bash](http://matt.might.net/articles/bash-by-example/)
* [How “Exit Traps” Can Make Your Bash Scripts Way More Robust And Reliable](http://redsymbol.net/articles/bash-exit-traps/)
* [sh tricks](http://www.etalabs.net/sh_tricks.html)
* [advancing in the bash shell](http://samrowe.com/wordpress/advancing-in-the-bash-shell/)
