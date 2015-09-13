# One time setup: #

Initialize your build environment:

https://source.android.com/source/initializing.html

Install Repo:

https://source.android.com/source/downloading.html

# Getting the latest source: #

If you haven’t done so already create a working directory
```
mkdir android-support-test; cd android-support-test
```

Checkout android-support-test branch
```
repo init -u https://android.googlesource.com/platform/manifest -b android-support-test
```

Synchronize the files for all available projects
```
repo sync -j8
```

Source locations
```
frameworks/testing/espresso
frameworks/testing/runner
frameworks/testing/rules
frameworks/uiautomator
```

# Building and Testing: #

## Via Android Studio: (recommended) ##

In Android Studio go to File -> Open… and select the build.gradle file located in the root folder
```
~/android-support-test/build.gradle
```

At this point all Test Support Library project should be loaded and you should be able to build and run your tests as you would run in your app. More details can be found here:

http://developer.android.com/tools/building/building-studio.html


## Via Command Line: ##

From the root directory (~/android-support-test/)

Build debug build type for all projects
```
./gradlew assembleDebug
```

Run tests for all projects
```
./gradlew connectedAndroidTest
```

**Note:** When running connectedCheck you might get “Unable to locate adb” Gradle error. This is a known issue. As a workaround you can use Android Studio to run your tests or you can add the following to the local.properties file:
```
sdk.dir=/path/to/sdk/dir
```

You can also build, test or execute any Gradle task for a specific project by prepending the project name in front of the desired task. For example:
```
./gradlew :espresso:connectedAndroidTest
./gradlew :runner:connectedAndroidTest
./gradlew :rules:connectedAndroidTest
./gradlew :uiautomator-v18:connectedAndroidTest
```

For a list of available Gradle commands
```
./gradlew tasks
```

# Contributing your work #

Read the following instructions on how to contribute to an open source project:

  1. [Code Style Guidelines for Contributors](https://source.android.com/source/code-style.html)
  1. [Life of a Patch](https://source.android.com/source/life-of-a-patch.html)
  1. [Submitting Patches](https://source.android.com/source/submit-patches.html)


We are looking forward to all of your contributions!