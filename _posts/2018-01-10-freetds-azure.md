---
theme: jekyll-theme-hacker
layout: default
title: Connecting to Azure using FreeTDS
date: 2018-01-03 00:00:00
---

#  Connecting to Azure using FreeTDS

Needed to connect to a MSSQL Server on Azure from Linux

1. Clone git rep https://github.com/FreeTDS/freetds.git

2. ./autogen.sh
Need autotools

3. ./configure --prefix=/usr/local  

4. make

5. sudo make install

6. TDSVER=7.0 tsql -H xxxx.database.windows.net -p 1433 -U xxxx -P xxxx -Dxxxx
-D is required
-S does not work 
