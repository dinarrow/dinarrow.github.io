---
theme: jekyll-theme-hacker
layout: default
title: HP Pavillion 15 Freeze Fix
date: 2019-09-29  00:00:00
---

# HP Pavillion 15 Freeze Fix

My new Laptop would randomly freeze particularly when left idle with nothing obvious appearing in the logs.

The cause was finally traced to two issues both fixed by adding kernel options. This was done by editing the following line in /etc/default/grub

```
GRUB_CMDLINE_LINUX_DEFAULT="i915.enable_psr=0 nomodeset"
```

and then running
```
~$ sudo update-grub2
```

The first option
```
i915.enable_psr=0
```
turns off the Panel Self Refresh (PSR) which is a power saving feature for the  Intel iGPUs.

The second option
```
nomodeset
```
turns off the screen resolution being set by the kernel. This is apparently so that you can display a high resolution splash screen on boot

So both issues appear to be screen related and the real question is why are they in the Ubuntu 18.04 LTS kernel in the first place. At best they should be modules rather than hard baked in the kernel itself but really they should be things you add if you wish after install.
This is particularly true for the PSR. The laptop has a Nvidia GPU.
We are going the way of Windows :-(
