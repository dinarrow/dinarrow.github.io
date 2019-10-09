---
theme: jekyll-theme-hacker
layout: default
title: Project and Build Note
date: 2019-10-09  00:00:00
---

# Project and Build Notes

These are just some basic notes of how, overtime, I have come to setup Projects and their Builds  and some of the reasoning behind them. This is is part because the boss asked me to do some documentation but also because I think I have reached a point of clarity on what works for me. Over years of coding I have tried many things and this is starting to be a 'final' methodology (until the need changes) and a recommendation to others just starting out.

For a general discussion of philosophy ["Selecting a Coding Toolset" ](../../../2019/10/08/coding-toolset-notes.html)

## Directory Structure

For small, self contained projects I use a directory structure like this

```
project_name
           |
           |-project_name 
           |-data 
           |-build-test 
           |-build-X
           |    ...
           |-build-X(n)
           |-library_name
           |    ...
           |-library_name(n)
```
And for larger projects with multiple components I use a directory structure like this

```
project_name
           |
           |-project_name 
           |            |-project_name 
           |            |-data 
           |            |-build-test 
           |            |-build-X
           |            |    ...
           |            |-build-X(n)
           |            |-library_name
           |            |    ...
           |            |-library_name(n)
           |
           |
           |-sub_project_name 
           |            |-sub_project_name 
           |            |-data 
           |            |-build-test 
           |            |-build-X
           |            |    ...
           |            |-build-X(n)
           |            |-library_name
           |            |    ...
           |            |-library_name(n)
           |
           |     ...
           |-sub_project_name(n) 
           |            |-sub_project_name(n)
           |            |-data 
           |            |-build-test 
           |            |-build-X
           |            |    ...
           |            |-build-X(n)
           |            |-library_name
           |            |    ...
           |            |-library_name(n)
           |
           |-library_name
           |    ...
           |-library_name(n)
           |-documents
```
The larger project can be seen to be a relatively simple expansion of the small project allowing for subprojects as well as common code. The names of the directories are entirely flexible but there are some basic guidelines.

For the simple project the root directory obviously is named to indicate what the project is about. Below this is a directory of the same name which contains the source files (and directories if required). This directory could equally be named 'source' or the more traditional 'src' but using the root name allows source code to be placed in a parallel directory if some separation becomes apparent as the code develops.

The next directory is called 'data' and contains variable components that change when the project is tested and debugged. This may be configuration files or persistent files required to run the project. It may also be material that is read or imported into the project or images and similar graphical material. The data can optionally be broken down into further directory if there is an advantage.

The build directories are where different versions of the (compiled) project output are generated to. The names build-X to build-X(n) should indicate the feature that differentiate  the build. For example it is common to have a 'build-64' directory for default configured 64 bit code. 'build-test' is the output directory for the test (i.e. BDD or Unit) build of the project.

Note that the build directories and the data directory are at the same level. This is to allow all builds to refer to the same data (../data) without any special knowledge of the data directory path which would be required if the data was stored below the source directory.

Finally there are zero to n library directories. These contain code and other materials that was not created as part of the project but are used by the project and are not installed globally on the host machine or are required to be different from the global versions. Commonly these are clones of Git (or other) repositories that can be updated ( pulled) independent of the project as required but they may also be just collections of relatively static files that are required fro some reason.

Returning once again to the larger project we can see that the patterns are repeated just with an additional level. Sub-projects are distinct parts of the overall project that are in some way not directly related to the functioning of the project. For example a sub-project could be a maintenance tool used on the data generated while the project is in use or a preparation tool that transforms input material into a required format.

Sub-projects, like the main project, can have their own special use library directories but libraries common to more than one (sub-)project can be located in the root project directory.

Another variant is that with large projects the main subproject directory ( the one containing the source and build directories) may contain source code. This can occur if some of the sub-project code is required by the project itself or other sub-projects. An example would be a code for working with a common file format or protocol. In these cases the source files are moved above the source directory to avoid name clashes with files that a often used in multiple project ( main.c for example).

Also in a large project the root directory may have material common to more than one sub-project such as documentation either directly or directories.

## Repository Structure (Git)

Overlaying the directory structure for both small and large projects there will obviously be a repository of some form for backup, branching etc. This will generally be Git.

The source of the repository will be the root directory ( .git directory location). Build, data and library directories should be generally ignored as these contain either dynamic material or material sourced elsewhere ( other Git repositories). However certain files in those directories ( scripts for example) can be forced into the repository as required.

Note that including cloned repositories as Sub-repositories in the main Git is avoided unless the main repository is to be publicly publish and the relationship between various versions and branches need to be maintained. Otherwise it is simpler to manually push the cloned repositories as and (only) when required to limit any effect external changes may have.
