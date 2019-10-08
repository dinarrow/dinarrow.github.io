---
theme: jekyll-theme-hacker
layout: default
title: Selecting a Coding Toolset
date: 2019-10-08  00:00:00
---

# Selecting a Coding Toolset

These are just some basic notes of how, overtime, I have come to select the Coding Tools that I use and some of the reasoning behind them. This is is part because the boss asked me to do some documentation but also because I think I have reached a point of clarity on what works for me. Over years of coding I have tried many things and this is starting to be a 'final' toolset (until the need changes) and a recommendation to others just starting out.

## Initial Position

I write code first and how I do it is secondary. Everything I uses is a tool to make it easier create working and maintainable code.

This may seem obvious but it is a matter of perspective. What I want to see is code in a form that can be passed to a computer to make it perform the desired task. If the computer fails to perform the task the reason can be found IN THE CODE. The computer does not work or fail due to something else, not described by The Code, that is changing a configuration or starting something that causes the computer to work or fail.

Examples of the something else are IDEs and build systems like CMake. Both tend to have "simplified" configurations or methods that make it easier to setup and get a program running. While the goals are laudable they do it by masking or hiding the real requirements. All is good until something goes wrong and there is no way to figure it out with a deep knowledge of what the IDE or Build System is doing for you.

If you use only one or two tools obtaining and maintaining this deep knowledge may be practical and worthwhile. Alternative in a large organisation you can have specialist with deep knowledge about a tool that can sort it out. However when you are and individual or a small team and start to use more than a few tools the volume of deep knowledge expands dramatically to the point where it is impossible to keep up with the change and new versions as they occur. 

Versions and Changes can in themselves be a problem if they modify what hidden work is being done for you by an IDE or Build System. A Project that is stable and functional can suddenly become nonworking because of a "upgrade" that is not backwardly compatible with the previous version. Worse yet a Project that appears stable and functional after the upgrade is actually not in some circumstance that it previously was.

This is where the focus on code arises. Two programs are the same if their Code is the same AND it is only the code that controls how the computer performs the task. If an upgrade of a tool causes a change it can be found in the Code and detected by comparing the Code. 

Therefore the most important thing is to have knowledge of the Code and why it is required. Once this is known and understood you can use tools to make coding simpler and faster, only requiring knowledge about them sufficient to generate the required code. If they don't generate the correct code you can then get deeper knowledge and continue to use them or choose other tools that do the job. You are not bound however to using a tool just because it is the only way you have learnt to do something.

The idea is one of minimalism and there are obvious limits. You cannot code without using some tools and having knowledge of how they work but you should choose your tools with a focus on clear code based inputs and output that can be compared and reviewed by a human with simple and stable tools.

## Minimal Tool Set

A Compiler/Interpreter - Unless you wish to write in binary some form of compiler/interpreter is obviously required. This is going to be very complex and require much deep knowledge to fully understand what it does but by its nature the required knowledge to use it should be comparatively shallow, particularly at the start. Fundamentally it should take code and turn it into a functional program requiring only a small number of (common) options with uncommon options only required to be learnt for specific cases. It should not be necessary to establish a complex series of options to get a simple program to run. This is so knowledge can grow with need and the reasons why an option is require.

IDE driven compilers are a complete anti-pattern here. While starting with an IDE to create a simple program may only require the clicking of a few menu buttons there is no clear connection to what each menu item actually does. Even if the IDE generates clean code that can be read the relationship between menu and code is unclear at best. Further IDE have a tendency to generate more code and options than are necessary to do the required functionality simply because "most real programs require these at some point". This may be true but generating it before it is needed only makes it harder to understand what the code does, what is required and what is just boilerplate.

Also a Compiler/Interpreter should have a clear and robust standard so knowledge of what it does is stable and can be discovered to whatever depth may be required. This should also mean that you have a choice of what Compiler/Interpreter you can use. The reasons for your choice many be non-obvious at the start but having a Standard means it can be made if and when required.

Finally your Compiler/Interpreter should have backwards compatibility. If you change to a later version it should still produce the same functional program with no additional changes other than options to tell it to work EXACTLY like the previous version BUGS and ALL. It is ok for the new version to tell you about a bug or a better way to do something but it should never change the program behaviour once you have told it to be compatible with old version. If it cannot be compatible then it should not work at all when presented with code that ran on the previous version. If it cannot produce the same program then you have to use the previous version until you understand what code changes are required and the effects they have on program function.

A Build System - Unless you are going to write relatively small, single file programs some form of build system will be required. This should allow you to specify with some form of code how your program is put together. Again, like the Compiler/Interpreter, it should initially require simple and common formats and options to be used  and then grow to a required complexity only as and when needed. Unlike the Compiler/Interpreter however what it does should not particularly complex as the requirements are relatively simple. A Build System should merely be a way of automating actions and configurations that could be done manually if the project was not of an increasingly large size.

Again many higher level Build Systems are an anti-pattern here. They often have options that do "big things", like setting up default configuration for different platforms and environments, without clear connections between the options and what it does. When the default is changed for some reason or is just wrong for your circumstances how much deep knowledge do you need to understand the issues. Further while they may set up defaults how well do they maintain them? Do they expect that only they ( the Build System) will change the files in the future or even that the default once generated continue to exist. Can they detect when a change is made and tell you any more than it is broken.

Having standards here can be an advantage if it leads to choice. However as the job that the build system is doing should be kept simple switching to another choice for some reason should be a mechanical process provided neither system hides what it does.


