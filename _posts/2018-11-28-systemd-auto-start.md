---
theme: jekyll-theme-hacker
layout: default
title: Systemd Service Setup
date: 2018-11-28  00:00:00
---

# Systemd Service Setup

Create a unit file with the extention .service somewhere in the file system.

Example: master.service
[2020-05-06] Added StartLimitIntervalSec to ensure restart
```
[Unit]
Description=Master Relay
Requires=network.target
StartLimitIntervalSec=0

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

then as root, enable using systemctl to register the service (including necessary sysmbolic links)

```
sudo systemctl enable /home/user/service/master.service

```
Note full file path.

then reload daemons
```
sudo systemctl daemon-reload
```

Finally start

```
sudo systemctl start master.service
```

Note to remove a service

```
systemctl stop [servicename]
systemctl disable [servicename]
systemctl daemon-reload
```
Note if the service was not setup with the above methods further actions may be required
