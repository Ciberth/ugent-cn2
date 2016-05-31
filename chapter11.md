# Chapter 11 Shell Script Basics

## Shell Script Basics
_(11.1)_

The shebang (#!) starts a script followed by the type of shell the script should execute. A bash scripts starts as follows: `#!/bin/sh`.

## Quoting and Literals
_(11.2)_

Quotes are important. `echo $100` or `echo "$100"` or `echo '$100'` each mean different things and might give different output (output is: 00, 00, $100 respectively).

When writing scripts and working on the command line, remember what happens whenever the shell runs a command:

* Before running, the shell looks for variables, globs and other substitutions.
* Shell passes the results of the substitutions to the command. 

> `grep r.*t /etc/passwd` might turn into `grep r.input r.output /etc/passwd` if you have these files in your active directory. Be careful!

**Single quotes** wil resolve the example above. As far as the shell is concerned all characters between two single quotes, including spaces, make up a single parameter. 

> Note: That's why `grep 'r.*t' /etc/passwd` will work and 'r.*t /etc/passwd'` wont.

**Double quotes** work just like single quotes, except that the shell expands any variables that appear withing double quotes. 

> Note: a b c are three parameters while a "b c" are only two.

## Special Variables
_(11.3)_

_$1_, _$2_ and all variables named as positive nonzer integers contain the values of the script parameters. The built-in shell command _shift_ can be used to remove the first argument ($1) and advance the rest forward ($2 becomes $1). 

Others:

* $# is the number of arguments
* $@ reprensents all of a script's arguments
* $0 holds the name of the script
* $$ holds the process ID of the shell
* $? holds the exit code

## Exit Codes
_(11.4)_











## Extra (slides + notities)

TODO