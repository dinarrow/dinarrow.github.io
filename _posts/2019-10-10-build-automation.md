---
theme: jekyll-theme-hacker
layout: default
title: Build Automation
date: 2019-10-10  00:00:00
---

# Build Automation Notes

These are just some basic notes of how, overtime, I have come to setup the automation of building code to its output format ( executables)  and some of the reasoning behind it. This is is part because the boss asked me to do some documentation but also because I think I have reached a point of clarity on what works for me. Over years of coding I have tried many things and this is starting to be a 'final' methodology (until the need changes) and a recommendation to others just starting out.

For a general discussion of philosophy ["Selecting a Coding Toolset" ](../../../2019/10/08/coding-toolset-notes.html)

## Automation Related Files

The directory structure for a simple project described in ["Project and Build Notes" ](../../../2019/10/09/project-and-build-notes.html) can be expanded to show the build automation related files.

```
project_name
           |
           |-project_name
           |            |
           |            |-makefile
           |            |-compile.script
           |            |-compile.script(n)
           |
           |-data 
           |-build-test_ 
           |            |
           |            |-makefile
           |
           |-build-X____
           |            |
           |            |-makefile
           |
           |    ...
           |-build-X(n)_
           |            |
           |            |-makefile
           |
           |-library_name
           |    ...
           |-library_name(n)
```
Automation starts with the makefile in the project source directory (project-name) bein called via ["make"](https://www.gnu.org/software/make/) utility. This is because the make utility is normally integrated in some form to most code editors and IDEs. An example file looks like this:
```make
.PHONY : 
compile_script:
	./compile.script 

.PHONY :
console:
	./console.compile.script

.PHONY : 
test:
	./test.compile.script

.PHONY : 
complex :
	./compile.script option
```

