---
description: How to import FTCLib into your Android Studio FTC Project
---

# Installation

## build.common.gradle

First, you need to add the `mavenCentral` library repository to your `build.gradle` file at the project root:

{% code title="build.gradle" %}
```groovy
    repositories {
        mavenCentral()
    }
```
{% endcode %}

Next, `minSdkVersion` to `24` and `multiDexEnabled` to `true`:

{% code title="build.common.gradle" %}
```groovy
defaultConfig {
    applicationId 'com.qualcomm.ftcrobotcontroller'
    minSdkVersion 24
    targetSdkVersion 28
    multiDexEnabled true
```
{% endcode %}

Next, change `JavaVersion` to `8` :

{% code title="build.common.gradle" %}
```groovy
compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
}
```
{% endcode %}

## Only If Using CV:

Remove all instances of `"arm64-v8a"`

{% code title="build.common.gradle" %}
```groovy
ndk {
    abiFilters "armeabi-v7a"
}

ndk {
    abiFilters "armeabi-v7a"
}
```
{% endcode %}

## build.gradle \(TeamCode\)

Add this dependency block for the base library:

{% code title="build.gradle \(Module: TeamCode\)" %}
```groovy
dependencies {
    implementation 'org.ftclib.ftclib:core:2.1.1' // core
```
{% endcode %}

## OR

Add this dependency block for the vision library:

{% code title="build.gradle \(Module: TeamCode\)" %}
```groovy
dependencies {
    implementation 'org.ftclib.ftclib:vision:2.1.0' // vision
    implementation 'org.ftclib.ftclib:core:2.1.1' // core
}
```
{% endcode %}

## Install EasyOpenCV Dependency

Since FTCLib depends on EasyOpenCV for vision, and because EasyOpenCV depends on [OpenCV-Repackaged](https://github.com/OpenFTC/OpenCV-Repackaged), you will need to copy [libOpenCvAndroid453.so](https://github.com/OpenFTC/OpenCV-Repackaged/tree/9a4d3d4bc001feffb3767842fa2de0c38a98883a/doc/native_libs/armeabi-v7a) into the `FIRST` folder of the Robot Controller (i.e. connect the Robot Controller to your computer with a USB cable, put it into MTP mode, and drag 'n drop the file).

### Sync Gradle and Finished!

![Click that button and if successful, you can now use FTCLib](.gitbook/assets/gradle-sync.png)

