---
theme: jekyll-theme-hacker
layout: default
title: Connecting C++ code to Android React Native with Djinni
date: 2018-02-10  00:00:00
---

# Connecting C++ code to Android React Native with Djinni


The starting point for this is the [rep](https://github.com/MathieuDebit/ReactNativeCPP).


Ensure that the necessary Android Paths are in place as per [React Native Getting Started](https://facebook.github.io/react-native/docs/getting-started.html)

``
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/platform-tools
``

The path given in the README file did not work so replaced it with above

``
git clone --recursive https://github.com/MathieuDebit/ReactNativeCPP
cd ReactNativeCPP
npm install
npm run djinni
make ios #Not on linux :-)

react-native run ios #Not on linux :-)
react-native run-android
``

The Android build failed initially with the error
``
Error: Could not find or load main class org.gradle.wrapper.GradleWrapperMain
``

To fix it I copied the android/gradle/wrapper/ directory for a default (HelloWorld) Reactive Native project and everything was fine ( missing in git? Version difference?)

