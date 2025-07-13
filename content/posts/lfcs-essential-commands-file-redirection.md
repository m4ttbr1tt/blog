+++
draft = false
date = 2025-07-08T17:35:51+02:00
title = "LFCS - Essential Commands - File redirecton"
authors = ["Matt Britt"]
tags = ["LFCS", "Linux", "Certificates"]
series = ["LFCS"]
+++

File redirection is a core part of Linux, this post covers file stdin, stdout, stderr and other commands.

<!--more-->

#### Input-Output Redirection

```shell
# output redirection (file is overwritten)
sort file.txt > sortedfile.txt
sort file.txt 1> sortedfile.txt # equivalent stdout

# appends
sort file.txt >> sortedfile.txt

#stdin  < 
#stdout 1>
#stderror  2>

# redirect input 
< file.txt
# redirect to stdout
> file.txt  or 1> file.txt
# redirect error
2> error.txt

# eg dont show errors with grep
grep -r '^The' /etc/ 2>/dev/null # dev null is a black hole

grep -r '^The' /etc/ 1>output.txt 2>/dev/null # can redirect to multiple places 
grep -r '^The' /etc/ 1>>output.txt 2>>/dev/null # append 
grep -r '^The' /etc/ 1>alloutput.txt 2>&1 # stderror goes into stdout (will all go to alloutput file)

# input redirection
# heredoc
sort <<EOF


>EOF # can be any word here

# here string
bc <<<1+2+3+4
10

# Piping
grep -v '^#' /etc/ | sort | column -t
```
