---
description: How to import FTCLib into your Android Studio FTC Project
---

# Installation

## build.common.gradle

First, you need to add the `jcenter` library repository to your `build.common.gradle` file:

{% code title="build.common.gradle" %}
```groovy
    repositories {
        jcenter()
    }
```
{% endcode %}

Next, `minSdkVersion` to `24` and `multiDexEnabled` to `true`:

{% code title="build.common.gradle" %}
```groovy
defaultConfig {
    applicationId 'com.qualcomm.ftcrobotcontroller'
    minSdkVersion 24
    targetSdkVersion 26
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

## build.gradle \(TeamCode\)

Add this dependency block:

{% code title="build.gradle \(Module: TeamCode\)" %}
```groovy
dependencies {
    implementation 'com.arcrobotics:ftclib:1.1.0' // current version
}
```
{% endcode %}

## Install EasyOpenCV Dependency on the Phone

Since FTCLib depends on EasyOpenCV for vision, and because EasyOpenCV depends on [OpenCV-Repackaged](https://github.com/OpenFTC/OpenCV-Repackaged), you will need to copy [`libOpenCvNative.so`](https://github.com/OpenFTC/OpenCV-Repackaged/blob/master/doc/libOpenCvNative.so) from the `/doc` folder of that repo into the `FIRST` folder on the USB storage of the Robot Controller \(i.e. connect the Robot Controller to your computer with a USB cable, put it into MTP mode, and drag 'n drop the file\)

### Sync Gradle and Finished!

![Click that button and if successful, you can now use FTCLib](.gitbook/assets/image%20%281%29.png)

