---
theme: jekyll-theme-hacker
layout: default
title: Systemd Service Setup
date: 2018-11-28  00:00:00
---

# Systemd Service Setup

Create a unit file with the extention .service somewhere in the file system.

Example: master.service
```
[Unit]
Description=Master Relay
Requires=network.target

[Service]
Type=simple
ExecStart=/home/user/service/master-relay-single@4000
WorkingDirectory=/home/user/service/
Restart=always
RestartSec=15

[Install]
WantedBy=multi-user.target
Alias=master.service
```

```
chmod +x ./master.service
```

then as root, enable using systemctrl to register the service (including necessary sysmbolic links)

```
sudo systemctrl enable /home/user/service/master.service

```
Note full file path.

then reload daemons
```
systemctl daemon-reload
```

Finally start

```
sudo systemctrl start master.service
```

Note to remove a service

```
systemctl stop [servicename]
systemctl disable [servicename]
rm /etc/systemd/system/[servicename]
rm /etc/systemd/system/[servicename] symlinks that might be related
systemctl daemon-reload
systemctl reset-failed
```
