---
theme: jekyll-theme-hacker
layout: default
title: SSH No Password
date: 2018-10-30  00:00:00
---

# SSH No password

If you do not have a public/private keyset

ssh-keygen

and return for everything.

Then

ssh-copy-id -i ~/.ssh/id_rsa.pub -pPort [user@]Address

using the password.

Final test by 

ssh  -pPort [user@]Address
