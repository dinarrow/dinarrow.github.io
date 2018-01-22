---
theme: jekyll-theme-hacker
layout: default
title: Connecting C++ code to Android with Djinni
date: 2018-01-23  00:00:00
---

# Connecting C++ code to Android with Djinni


This [video](https://www.youtube.com/watch?v=TXhLidEIxiI) and the related[rep](https://github.com/jaetzold/djinniDemo) proved usefull mainly because he kept parts separate so the configuration is easier to follow.


First in the clone of the [Djinni rep](https://github.com/dropbox/djinni) create a directory at the same level as their example.

Then in that directory create as script run_djinni.sh

```
#!/bin/bash

rm -r generated/*

../src/run \
    --java-out generated/java/com/example/djinni \
    --java-package com.example.djinni \
    \
    --cpp-out generated/cpp/ \
    --cpp-optional-template std::experimental::optional \
    --cpp-optional-header "<experimental/optional>" \
    \
    --jni-out generated/jni/ \
    --ident-jni-class JNIFooBar \
    --ident-jni-file jni_foo_bar \
    \
    --objc-out generated/objc/ \
    --objcpp-out generated/objcpp/ \
    --objc-type-prefix SJ \
    \
    --idl idl/example.djinni
```
Create a sub-directory idl and a definition file idl/example.djinni

```
example_service = interface +c {
    static create_service(display: example_display): example_service;
    # Call this in bg thread
    run(who: list<string>);
}

example_display = interface +j +o {
    display(example_record: example_record);
}

example_record = record {
    message: string;
    color: example_color;
}

example_color = enum {
    RED;
    BLUE;
}
```
