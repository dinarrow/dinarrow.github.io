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
           |
           |
           |-sub_project_name 
           |            |-sub_project_name 
           |            |-data 
           |            |-build-test 
           |            |-build-X
           |            |    ...
           |            |-build-X(n)
           |
           |     ...
           |-sub_project_name(n) 
           |            |-sub_project_name(n)
           |            |-data 
           |            |-build-test 
           |            |-build-X
           |            |    ...
           |            |-build-X(n)
           |
           |-library_name
           |    ...
           |-library_name(n)
```
