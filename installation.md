---
description: How to import FTCLib into your Android Studio FTC Project
---

# Installation

### build.common.gradle 

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

### build.gradle \(TeamCode\)

Add this dependency block in the teamcode `build.gradle` file (or create the dependencies block if it doesn't exist):

{% code title="build.gradle \(Module: TeamCode\)" %}
```groovy
dependencies {
    implementation 'com.arcrobotics:ftclib:2.0.11'
}
```
{% endcode %}

#### Sync Gradle and Finished!

![Click that button and if successful, you can now use FTCLib](.gitbook/assets/image%20%281%29.png)



