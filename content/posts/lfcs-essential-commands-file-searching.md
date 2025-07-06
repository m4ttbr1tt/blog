+++
draft = false
date = 2025-07-06T17:35:51+02:00
title = "LFCS - Essential Commands - File searching"
authors = ["Matt Britt"]
tags = ["LFCS", "Linux", "Certificates"]
+++


This post covers the commands for finding files on your system and searching the contents of files for patterns.

<!--more-->

### Search for files 

```shell
find [path] [params]
#    go-there   find it  (if you leave out path its current dir)
find /usr/share/ -name '*.jpg' #regex of name
find /lib64/ -size +10M # files greater than 10Meg
find /dev/ -mmin -1 # modified in last minute

find -iname felix # will find felix and Felix 
find -name "f*" # all files starting with f
find -mmin [minute]  #modified minute

# can have multiple in search expression
find -name "f*" -size 512k # implied AND 
find -name "f*" -o -size 512k # explicit OR 
find -not -name "f*" -o -size 512k # not being with f
#or 
find \! -name "f*" -o -size 512k # need to escape not

find -perm 664  # file with these permissions
find -perm -664  # file with at least these permissions
find -perm /664  # any of these OR  
find -perm 664
#or 
find -perm u=rw,g=rw,o=r # exactly 664 permission

find \! -perm -o=r # files others cannot read
```

###  Grep in files

```shell
grep [options] 'search_pattern' ./file # options are optional

grep -i 'password' ./config.txt # case insensitive
grep -r 'password' ./dir # recursive 
sudo grep -r --color 'password' ./dir # sets colour
grep -v 'password' ./file # inverse search
grep -w 'password' ./file # word search (exact word)
grep -o 'password' ./file # only matching (just shows match not rest of line)
```

###  Regex 

```shell
# operatiors
^ # line beginning with
grep -v '^#' ./file # lines that dont begin with # -v is inverse
$ # last character
grep -w '7$' ./file # match word (single 7)
. # any character
\ # escaping
grep '\.' ./file # escapes the period
* # 0 - many times
grep 'let*' ./file
# mactches
# le
# let
# lett
grep '/.*/' ./file # period any character * is any number of times, so this will match any string between / and /
+ # element exists at least once or more
grep -r '0\+' ./dir # need to escape the + (unless using extended regex)
```

###  Extended Regex 

```shell
# You dont have to escape characters with Extended Regex
grep -Er '0+' /etc/ # E with capital
# egrep is same as grep -E
egrep -r '0{3,}' /etc/ # min and max amount of repetitions
egrep -r '10{,3}' /etc/ # string with 1 the at most 3 zeros
egrep -r '0{3}' /etc/ # exactly 3 zeros 
egrep -r 'disabled?' /etc/ # d is optional
egrep -r 'disabled|enabled' /etc/ # or
egrep -r 'c[au]t' /etc/ # range [a-z]  set [az]
egrep -r '/dev/[a-z]*' /etc/ # any number of letters from a-z 
egrep -r '/dev/[a-z]*[0-9]?' /etc/ # any number of letters from a-z, with optional 0-9
egrep -r '/dev/([a-z]*[0-9]?)*' /etc/ # subexpression (regex gets repeated 0 or more times)  will match tty0p0
egrep -r '/dev/(([a-z]|[A-Z])*[0-9]?)*' /etc/ # subexpression (regex gets repeated 0 or more times)  will match tty0p0
egrep -r 'https[^:]' /etc/  # negated ranges
egrep -r 'http[^s:]' /etc/  # negated range set will only match http
```

https://regexr.com/
