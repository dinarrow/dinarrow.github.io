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

## Initial Makefile
Automation starts with the makefile in the project source directory (project-name) bein called via ["make"](https://www.gnu.org/software/make/) utility. An example file looks like this:
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
	./compile.script option1 option2

.PHONY : 
complex-n :
	./compile.script option1 option-n
```
Note that all the make builds are ["Phony Targets"](https://ftp.gnu.org/old-gnu/Manuals/make-3.79.1/html_node/make_34.html) that simply call scripts. This is because the make utility is normally integrated in some form to most code editors and IDEs so is easier than calling the scripts directly. It also allow a single script to be used with options in the complex cases.

## Scripts

An example script file looks like this:
```
#!/bin/bash

cd ../build-X

rm configuration.o
if make -j12
then
cd ../program-name
exit 0
else
cd ../program-name
exit 1
fi

```
Basically the script shifts to the correct build directory calls the make utility and then returns the working directory to the project source directory. While in the build directory the script can run other commands and continue differently if the make operation fails.

In this example the script remove the configuration file object before calling the make utility. This is commonly done when the same source code is being used to generate different outputs where the configurable variables are placed in a single source file. Deleting the related object file ensures that the correct variables are used when linking.

An example test.compile.script shows how the return from the make utility can be used.
```
#!/bin/bash

cd ../build-test

if make test -j12
then
./test
cd ../program-name
exit 0
else
cd ../program-name
exit 1
fi

```
Here the output of the compilation (./test) is run only if the code has been built successfully as this is most common action.

A more complex compile.script can use the options provided to change the make utility parameters or run other commands
```
#!/bin/bash

cd ../build-test

if make TARGET=${1} -j12
then
${2}
cd ../program-name
exit 0
else
cd ../program-name
exit 1
fi

```
Note that most, if not all, of the actions taken by the scripts could be done by expanding the project makefile as can be found in many published code bases. However as noted in ["Selecting a Coding Toolset" ](../../../2019/10/08/coding-toolset-notes.html) I have a minimalist philosophy when using tools and I apply it here. Make is a powerful tool with many options that can require deep knowledge. Similarly Bash (the shell) is also complex. While details of Make can be learnt once they are their use is confined to the make utility. Detailed knowledge of Bash however can be used in other scripts and thus have a far greater utility. Further complex makefiles may still require shell scripts to be used so a knowledge of Bash is required anyway.

This is not to say that knowledge of Make is not important or that no codebase should use complex make files. Simply that if working with many coding tools it is better to learn about the tools with the greatest utility. This also means that it is more likely that another coder will have sufficient knowledge to understand the build process when they are introduced to the project for the first time. It is an example, as per the philosophy, that having or using tools in a project that require deep and specialized knowledge to understand may make things initially easier but get really hard when they need to be maintained.

## Build Makefile

The makefile in the build directory is where the real automation is done but again uses a minimal knowledge of Make functionality for simplicity and maintainability.
