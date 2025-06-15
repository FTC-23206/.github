---
title: "Getting Started"
permalink: /pond/getting-started
toc: true
---

## Installation Methods

There are 2 available installation methods:

* **Quick Start Repository**: zero-code process that gets you started from a new FTC Repo which is ready to run. This is recommended for beginner to intermediate teams which are not looking into bringing their existing code *as-is* to the new repository.

* **In-Place Update**: adding Pond library reference to your existing code. This is recommended for advanced teams who wants to be selective about how to integrate and which functionality to depend on.

> **Tip:** The 2 installation methods allow you to reach the same result as the Pond library is always used as a library reference. The only difference is the path to get there. In other words, no matter which way you chose to start, both methods allow for the same result in the end.
{: .notice--info}

## Option 1: Quick Start Repository

### From Android Studio

1. Install *Android Studio* from <https://developer.android.com/studio>
2. Open *Android Studio* and choose 'Clone Repository'
    <img src="/images/pond/pond-android-clone.png" width="600" style="display: block; margin: 0 auto;" />
3. Give the repository URL:
   `https://github.com/FTC-23206/automaducks-pond-starter.git`

### From Command Line

1. Install *git* from <https://git-scm.com/downloads>
2. Open `Command Prompt` or `Terminal` and navigate to the desired repository location
3. Clone the [Pond-Starter](https://github.com/FTC-23206/automaducks-pond-starter) repository:
    ```powershell
    git clone https://github.com/FTC-23206/automaducks-pond-starter.git
    ```
4. Open the the root of the repository with *Android Studio*

### Change the configuration to match your robot configuration

1. Use FTC Dashboard to try the correct configuration
   1. Connect your computer to your robot wifi
   2. Open <http://192.168.43.1:8080/dash> on web browser
   3. Configure `Chassis` motors to match your robot hub configuration
2. Run The robot (see section below for configuration)

### Motors  Configuration Troubleshooting

(Pending)

### Sample Code Walkthrough

(Pending)

## Option 2: In-Place Update

### Clone The Repository

#### Add the Pond package reference (Required)

1. Make sure your `TeamCode` project successfully builds in Android Studio (`F7`)
2. Add the following to your Team Code gradle file
    File: `(RepositoryRoot)\TeamCode\build.gradle`
    ```gradle
    dependencies {
        implementation project(':FtcRobotController')

        // Pond Library Reference
        implementation 'com.automaducks:pond:0.9.0'
    }
    ```
3. Sync the project with gradle files (`CTRL+SHIFT+O`)
4. Rebuild `TemCode` project (`F7`)

After a successful build, you are ready to start leveraging the *Pond* libraries.

#### Add FTC Dashboard and Road Runner (Recommended)

This is not require but Pond is made to work well with FTC dashboard as it can leverage dynamic configuration changes and graphing.

1. Similar to the above, update your Team Code `build.gradle` per below:
    ```gradle
    //
    // build.gradle in TeamCode
    //
    // Most of the definitions for building your module reside in a common, shared
    // file 'build.common.gradle'. Being factored in this way makes it easier to
    // integrate updates to the FTC into your code. If you really need to customize
    // the build definitions, you can place those customizations in this file, but
    // please think carefully as to whether such customizations are really necessary
    // before doing so.


    // Custom definitions may go here

    // Include common definitions from above.
    apply from: '../build.common.gradle'
    apply from: '../build.dependencies.gradle'

    android {
        namespace = 'org.firstinspires.ftc.teamcode'

        packagingOptions {
            jniLibs.useLegacyPackaging true
        }
    }

    repositories {
        maven {
            url = 'https://maven.brott.dev/'
        }
    }

    dependencies {
        implementation project(':FtcRobotController')

        // Pond
        implementation 'com.automaducks:pond:0.9.0'

        // ACME Libraries
        implementation 'com.acmerobotics.roadrunner:core:1.0.1'
        implementation 'com.acmerobotics.dashboard:dashboard:0.4.16'
    }
    ```
2. Sync the project with gradle files (`CTRL+SHIFT+O`)
3. Rebuild `TemCode` project (`F7`)

## Leveraging Pond

1. See how to best leverage Pond features from the [documentation](/pond).
2. You can also find sample code here:
    <https://github.com/FTC-23206/automaducks-pond-starter/tree/master/TeamCode/src/main/java/org/firstinspires/ftc/teamcode>