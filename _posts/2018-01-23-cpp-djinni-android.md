---
theme: jekyll-theme-hacker
layout: default
title: Connecting C++ code to Android with Djinni
date: 2018-01-23  00:00:00
---

# Connecting C++ code to Android with Djinni


This [video](https://www.youtube.com/watch?v=TXhLidEIxiI) and the related [rep](https://github.com/jaetzold/djinniDemo) proved usefull mainly because he kept parts separate so the configuration is easier to follow.


### Djinni

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

Run the script to generate the djinni code in the generated sub-directory.

### Android Studio

Create a C++ project supporting exceptions and RTTI

In the app/src directory create a new directory djinni.

In the djinni directory create symbolic links to the generated and idl directory in the djinni project. Also create a symbolic link to the support_libs directory in the djinni rep.

Add the following to the app/build.gradle file

```
    sourceSets {
        main.java.srcDirs += 'src/djinni/generated/java'
    }
```
so it looks something like this

```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 26
    defaultConfig {
        applicationId "com.example.project"
        minSdkVersion 23
        targetSdkVersion 26
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
        externalNativeBuild {
            cmake {
                cppFlags "-std=c++11 -frtti -fexceptions"
            }
        }
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
    externalNativeBuild {
        cmake {
            path "CMakeLists.txt"
        }
    }
    sourceSets {
        main.java.srcDirs += 'src/djinni/generated/java'
    }

}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:26.1.0'
    implementation 'com.android.support.constraint:constraint-layout:1.0.2'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.1'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.1'
}
```
Replace the app/CMakeList.txt file with this

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

and if everything is correct it should compile to produce a library file (*.so) in the app/build/intermediates/cmake/debug/obj/armeabi-v7a/ directory.

Java level code can then be written to use the  library similar to this
```java
package com.example.project.name;

import android.graphics.Color;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;

import android.view.animation.AlphaAnimation;
import android.view.animation.Animation;

import android.widget.TextView;

import java.util.ArrayList;
import java.util.Arrays;

import com.example.djinni.Greeting;
import com.example.djinni.GreetingColor;
import com.example.djinni.GreetingDisplay;
import com.example.djinni.GreetingService;

public class MainActivity extends AppCompatActivity {
    ExampleService exampleService;
    // Used to load the 'native-lib' library on application startup.
    static {
        System.loadLibrary("native-lib");
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Example of a call to a native method
        final TextView tv = (TextView) findViewById(R.id.sample_text);
        // Example of a call to a native method
        exampleService = ExampleService.createService(new ExampleDisplay() {
            @Override
            public void display(Example example) {
                MainActivity.this.display(example, tv);
            }
        });
        new Thread(new Runnable() {
            public void run() {
                exampleService.run(new ArrayList<>(Arrays.asList("World", "Devoxx", "Djinni", "Android")));
            }
        }).start();
    }

    private void display(final Example example, final TextView textView) {
        runOnUiThread(new Runnable() {
            public void run() {
                String message = example.getMessage();
                textView.setText(message);
                ExampleColor color = example.getColor();
                switch (color) {
                    case RED:
                        textView.setTextColor(Color.RED);
                        break;
                    case BLUE:
                        textView.setTextColor(Color.BLUE);
                        break;
                }
                fadeOut(textView, ExampleService.COOLDOWN_MS / 2, ExampleService.COOLDOWN_MS / 4);
            }
        });
    }

    private void fadeOut(final TextView textView, long delay, long duration) {
        textView.setAlpha(1);
        AlphaAnimation animation = new AlphaAnimation(1, 0);
        animation.setStartOffset(delay);
        animation.setDuration(duration);
        animation.setAnimationListener(new Animation.AnimationListener() {
            @Override
            public void onAnimationEnd(Animation animation) {
                textView.setAlpha(0);
            }

            @Override
            public void onAnimationStart(Animation animation) {
            }

            @Override
            public void onAnimationRepeat(Animation animation) {
            }
        });
        textView.startAnimation(animation);

    }
}
```
