---
theme: jekyll-theme-hacker
layout: default
title: Connecting C++ code to Android React Native with Djinni
date: 2018-02-10  00:00:00
---

# Connecting C++ code to Android React Native with Djinni


Ensure that the necessary Android Paths are in place as per [React Native Getting Started](https://facebook.github.io/react-native/docs/getting-started.html)

```
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

Create at react native project and build it as per [React Native Getting Started](https://facebook.github.io/react-native/docs/getting-started.html)
```
react-native init Aproject
cd AProject
react-native run-android
```

Now do the Djinni setup as per the previous post [Connecting C++ code to Android with Djinni](https://dinarrow.github.io/2018/01/23/cpp-djinni-android.html)

```
aproject_service = interface +c {
    static create_service(): aproject_service;
    # Call this in bg thread
    run();
}
```

Then add the sourceSets and externalNativeBuild sections to app/build.gradle


```
....

android {
....
    defaultConfig {
....
        externalNativeBuild {
            cmake {
                cppFlags "-std=c++11 -frtti -fexceptions"
            }
        }
    }
....
    externalNativeBuild {
        cmake {
            path "CMakeLists.txt"
        }
    }
    sourceSets {
        main.java.srcDirs += 'src/djinni/generated/java'
    }

}

```


Copy in the app/CMakeList.txt file 
```
# Sets the minimum version of CMake required to build the native
# library. You should either keep the default value or only pass a
# value of 3.4.0 or lower.

cmake_minimum_required(VERSION 3.4.1)

# Path to the djinni support code
set(support_dir src/djinni/support-lib/jni)
# Path to the generated code and our own c++ implementations
set(include_dirs src/djinni/generated/cpp/ src/djinni/generated/jni/ src/main/cpp/)

# Djinni support code that needs to be compiled
file(
  GLOB_RECURSE support_srcs
  ${support_dir}/*.cpp)
# Generated code and c++ implementations that need to be compiled
file(
  GLOB_RECURSE lib_srcs
  src/djinni/generated/cpp/*.cpp
  src/djinni/generated/jni/*.cpp
  src/main/cpp/*.cpp)

# All the implementation files that make up our library
set(complete_srcs ${support_srcs} ${lib_srcs})

# Define library referring to the sources above
add_library(native-lib SHARED ${complete_srcs})

# Define INCLUDE_DIRECTORIES property for native-lib
target_include_directories(
  native-lib
  PUBLIC
  ${include_dirs}
  ${support_dir})
```

and the local.properties file ( this effects the CMake setup)

```
ndk.dir=/home/bruce/Android/Sdk/ndk-bundle
sdk.dir=/home/bruce/Android/Sdk
```

Finally add the requires implementation code in src/main/cpp and Modify MainActivity.java

```
package com.aproject;

import com.facebook.react.ReactActivity;
import android.os.Bundle;
import com.aproject.djinni.AprojectService;

public class MainActivity extends ReactActivity {
 AprojectService aprojectService;
    // Used to load the 'native-lib' library on application startup.
    static {
        System.loadLibrary("native-lib");
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        aprojectService = AprojectService.createService();
        new Thread(new Runnable() {
            public void run() {
	aprojectService.run();
            }
        }).start();
    }

    /**
     * Returns the name of the main component registered from JavaScript.
     * This is used to schedule rendering of the component.
     */
    @Override
    protected String getMainComponentName() {
        return "AProject";
    }
}
```
