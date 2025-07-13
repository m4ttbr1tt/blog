+++
draft = false
date = 2025-07-11T17:35:51+02:00
title = "LFCS - Operations Deployment - Task Scheduling"
authors = ["Matt Britt"]
tags = ["LFCS", "Linux", "Certificates"]
series = ["LFCS"]
+++

Running periodic tasks can be very helpful with system maintenance, or application development tasks. Linux uses cron jobs for this.

<!--more-->

#### Schedule Tasks to Run at Set Date and Time

```shell
# cron
# repetitive jobs

# anacron (days, weeks, no smaller units)

cat /etc/crontab # shows syntax and can set cronjob - systemwide table

# cron
# * matches all  values
# , match multiple 15,45
# - range   2-4
# / steps */4

which touch

crontab -e # edits table of current user
35 6 * * * /user/bin/touch test
crontab -l # list currents users crontabs
sudo crontab -e -u jane # edits cron of another user (needs sudo)
crontab -r # removes your crontab
sudo crontab -r -u jane # removes for different user (root required)

# special dirs
/etc/cron.daily/
/etc/cron.hourly/
/etc/cron.monthly/
/etc/cron.weekly/

touch shellscript # no extension for cron
sudo cp shellscript /etc/cron.daily/ # and make executable

# after installing anacron
sudo vim /etc/anacrontab
anacron -T # verifies syntax

sudo apt install at

# single running jobs

at '15:00' 
at 'now + 3 hours' 
at 'now + 3 days' 
at 'now + 3 week' 

atq # query 
at -c 1 # show
atrm 1 # removes
```

A useful site to test your crontabs [https://crontab.guru/](https://crontab.guru/)
