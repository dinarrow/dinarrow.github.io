---
theme: jekyll-theme-hacker
layout: default
title: Changing the Java Path in a React Native Project
date: 2018-02-10  00:00:00
---

# Changing the Java Path in a React Native Project

After running
```
 reactive-native init project_name
```

The java paths in the android portion of the project are basically com/project_name.

To give the project a valid path ( nz/co/valid/project_name) edit the build.gradle and the AndroidManifest.xml.

Then create the correct source path and move the MainActivity.java and MainApplication.java files to the new location.

Finally modify the package name at the top of the MainA* files
