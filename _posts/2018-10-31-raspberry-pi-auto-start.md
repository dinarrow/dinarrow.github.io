---
theme: jekyll-theme-hacker
layout: default
title: Raspberry Pi Auto Start
date: 2018-10-30  00:00:00
---

# Raspberry Pi Auto Start

Raspian is a version linux so this is just the specific auto-start method used

A file is required in /etc/init.d like this

```
#! /bin/sh
### BEGIN INIT INFO
# Provides:          ### description ###
# Required-Start:    $all
# Required-Stop:     
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: ### Description ###
### END INIT INFO

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/bin

. /lib/init/vars.sh
. /lib/lsb/init-functions
# If you need to source some other scripts, do it here

case "$1" in
  start)
    log_begin_msg "Starting ### Description ###"
#### path-to-exe ####
    log_end_msg $?
    exit 0
    ;;
  stop)
    log_begin_msg "Stopping ### Description ###"
#### path-to-exe ####
    log_end_msg $?
    exit 0
    ;;
  *)
    echo "Usage: /etc/init.d/### file_name ###  {start|stop}"
    exit 1
    ;;
esac

```

Do a chmod +x and then run

sudo update-rc.d ### file_name ### defaults

